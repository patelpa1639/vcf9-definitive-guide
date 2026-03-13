# Chapter 4: Compute -- vSphere 9 as the Engine of VCF

Every VMware Cloud Foundation deployment begins and ends with compute. Networking can be elegant, storage can be fast, automation can be clever -- but none of it matters if the hypervisor cannot run the workloads your business demands. With VCF 9.0, the compute layer is vSphere 9, and it represents the most significant generational leap in VMware's hypervisor since the introduction of the Distributed Resource Scheduler over two decades ago. This chapter examines every dimension of that leap: the expanded virtual hardware envelope, the NVMe memory tiering system that rewrites capacity planning assumptions, the clustering and availability changes that finally retire a long-standing architectural crutch, the CPU compatibility decisions that will force uncomfortable conversations in brownfield environments, GPU acceleration for the AI era, Kubernetes-native workload management, and the performance benchmarks that put numbers behind the marketing.

If you have been running vSphere 7 or 8 in production, some of what follows will feel like natural evolution. Other parts will feel like a sharp turn. Both reactions are correct.

---

## 4.1 vSphere 9 in Context: The Compute Contract with VCF

VCF is an opinionated platform. It prescribes how networking, storage, and compute interlock, and it does so to deliver lifecycle consistency across domains. Within that contract, vSphere 9 is the compute substrate -- the layer responsible for CPU scheduling, memory management, device virtualization, VM isolation, and the cluster-level intelligence that keeps workloads running when hardware fails.

In VCF 9.0, vSphere 9 is not optional. You do not pick a hypervisor version at deployment time; SDDC Manager provisions ESXi 9.0 hosts and vCenter 9.0 instances as part of the bring-up workflow. This tight coupling is deliberate. It ensures that every feature discussed in this chapter -- from Virtual Hardware Version 22 to NVMe memory tiering -- is available uniformly across every workload domain in your environment.

For practitioners coming from standalone vSphere deployments, this is the key mental shift: in VCF, the hypervisor is a platform component, not a standalone product. You consume it through SDDC Manager, you patch it through vSphere Lifecycle Manager images, and you monitor it through VCF Operations. The days of SSH-ing into a host and running esxcli commands to apply a one-off patch are over -- or at least, they should be.

---

## 4.2 Virtual Hardware Version 22: The Monster VM Gets Bigger

### 4.2.1 The New Maximums

Every major vSphere release introduces a new virtual hardware version, and vSphere 9.0 brings Version 22. The headline numbers are impossible to ignore:

- **Maximum vCPUs per VM: 960** (up from 768 in Hardware Version 21)
- **Maximum RAM per VM: 24 TB** (up from 16 TB in Hardware Version 21)
- **NVMe 1.3c controller support** with enhanced command set
- **TPM 2.0 library specification Revision 1.59** for virtual Trusted Platform Modules
- **New ISA exposure** for Intel Granite Rapids and AMD Turin (5th-generation EPYC) processors

These are not theoretical numbers thrown onto a spec sheet to win a features comparison. They reflect the actual workload demands that VMware's engineering teams are seeing from customers running SAP HANA, Oracle Database, large-scale in-memory analytics, and increasingly, monolithic AI training jobs that refuse to be decomposed into smaller units.

### 4.2.2 When You Actually Need 960 vCPUs

Let me be direct: the vast majority of VCF deployments will never provision a VM with 960 vCPUs. The median enterprise VM still runs 2 to 4 vCPUs. But the customers who need this capability need it desperately.

Consider a SAP HANA scale-up deployment on a four-socket Intel Granite Rapids server with 480 physical cores (960 threads). In previous vSphere versions, you could not present the full server's compute capacity to a single VM without leaving headroom for the hypervisor itself. Hardware Version 22 removes that constraint. You can now create a VM that maps one-to-one with the physical topology of the largest servers on the market.

The 24 TB RAM ceiling follows the same logic. AMD Turin supports up to 24 TB of addressable memory per socket in certain configurations, and Intel's Granite Rapids Xeon processors push similar boundaries. Hardware Version 22 ensures the hypervisor does not become the bottleneck.

### 4.2.3 New Device Support and the NVMe 1.3c Controller

Hardware Version 22 introduces NVMe 1.3c controller emulation, which matters for two reasons. First, modern guest operating systems -- Windows Server 2025, RHEL 9.x, Ubuntu 24.04 LTS -- expect NVMe as the default storage interface. The paravirtual SCSI (PVSCSI) adapter that served VMware well for fifteen years is increasingly treated as a legacy device by OS vendors. Second, NVMe 1.3c brings command set improvements that enable better queue management and error handling inside the guest, which translates to measurable IOPS improvements on high-throughput storage workloads.

VMware Tools 13, which ships alongside vSphere 9, includes updated NVMe and network drivers optimized for Hardware Version 22. It also adds Direct3D 11.0 rendering support for virtual graphics and ensures compatibility with Windows Server 2025 and Windows 11 24H2 guests.

### 4.2.4 The Compatibility Cliff

Here is the part that matters most in brownfield environments: **Virtual Hardware Version 22 VMs can only run on ESXi 9.0 or later hosts.** There is no backward compatibility. If you upgrade a VM to Hardware Version 22 and then need to migrate it to a cluster still running ESXi 8.0, the migration will fail.

This creates a hard boundary in mixed-version environments. My recommendation is straightforward: do not upgrade VM hardware versions until every cluster that might host that VM is running ESXi 9.0. In a VCF context, this means completing the workload domain upgrade before touching individual VM configurations. SDDC Manager enforces this sequence for infrastructure VMs, but tenant VMs are your responsibility.

The EVC (Enhanced vMotion Compatibility) baseline for Hardware Version 22 requires the Granite Rapids or Turin CPU feature set at minimum. Plan your EVC mode settings carefully during the upgrade window.

---

## 4.3 NVMe Memory Tiering: Cheap RAM That Actually Works

### 4.3.1 The Problem Statement

Memory is expensive. It has always been expensive, and the per-gigabyte cost of DDR5 is not declining as fast as the memory appetite of modern workloads is growing. For years, VMware addressed this with memory overcommitment techniques -- transparent page sharing, ballooning, compression, and swap. These work, but they introduce performance cliffs. When a host starts swapping to disk, VM performance degrades catastrophically.

NVMe memory tiering, which reaches General Availability in vSphere 9.0, offers a fundamentally different approach. Instead of treating NVMe as a swap device of last resort, the hypervisor treats it as a second tier of memory -- slower than DRAM, but orders of magnitude faster than traditional swap to a SAN or even a local SSD used as a swap datastore.

### 4.3.2 How It Works: Hot Pages, Cold Pages, and Transparent Migration

The mechanism is elegant in concept and demanding in implementation. ESXi 9.0 maintains a two-tier memory hierarchy:

- **Tier 0 (DRAM):** The fast tier. This is your server's installed DDR5 memory.
- **Tier 1 (NVMe):** The capacity tier. This is a local NVMe device that ESXi claims exclusively for memory tiering -- it is not available as a datastore.

The hypervisor continuously monitors memory access patterns at the page level. Pages that are actively being read or written -- "hot" pages -- are kept in DRAM. Pages that have not been accessed recently -- "cold" pages -- are candidates for migration to the NVMe tier. When a VM accesses a cold page that resides on NVMe, the hypervisor transparently migrates it back to DRAM and demotes a different cold page to make room.

The critical design principle is that this is entirely transparent to the guest operating system. The VM sees a single, flat memory address space. It does not know that some of its pages are on NVMe. There is no guest-side driver, no application modification, and no memory-aware NUMA tuning required inside the VM. The hypervisor handles everything.

Page migration only occurs under memory pressure. If the host has sufficient free DRAM to accommodate all VM memory, the NVMe tier sits idle. The system avoids unnecessary page movement to minimize compute overhead -- moving pages consumes CPU cycles, and the tiering engine is designed to be conservative about spending them.

### 4.3.3 Capacity Planning: The DRAM-to-NVMe Ratio

The default ratio in VCF 9.0 is **1:1** -- for every terabyte of DRAM, you add one terabyte of NVMe tiering capacity. This effectively doubles your host's addressable memory. Broadcom's best-practice recommendation is to keep this default ratio for general-purpose workloads.

The ratio is configurable as an advanced host parameter, and you can push it as far as **1:4** -- meaning a host with 1 TB of DRAM could present up to 5 TB of total addressable memory (1 TB DRAM + 4 TB NVMe). However, I want to be clear about the tradeoff: as you increase the NVMe proportion, you increase the probability that hot pages will need to be fetched from the slower tier. At a 1:4 ratio, you are betting that no more than 20% of your aggregate VM memory is actively hot at any given moment. That is a safe bet for file servers and lightly used infrastructure VMs. It is a dangerous bet for database workloads.

**The golden rule for sizing:** the total active (hot) memory across all VMs on a host should not exceed 50% of the DRAM capacity. This gives the tiering engine enough headroom to absorb access pattern changes without triggering performance degradation.

Additional constraints to keep in mind:

- The NVMe tier partition cannot exceed **4 TB** per host.
- The NVMe device is claimed exclusively by the memory tiering subsystem. It cannot be shared with vSAN, VMFS, or any other storage function.
- Memory tiering is compatible with vMotion -- a VM can be migrated between tiering-enabled hosts, and the tiering state is rebuilt transparently on the destination.
- Memory tiering supports VM-level and host-level encryption.

### 4.3.4 Ideal Workloads and What It Is Not For

NVMe memory tiering shines in environments with large populations of VMs that have variable memory activity -- VDI desktops, web servers, development environments, file and print servers. These workloads tend to have a small hot working set relative to their total allocated memory.

It is **not** a good fit for latency-sensitive, memory-intensive workloads where the entire memory footprint is actively accessed: real-time trading systems, in-memory databases with full-table scans, and high-frequency analytics. For these workloads, every page fetch from NVMe introduces microseconds of latency that compound into measurable performance degradation.

### 4.3.5 Production Readiness: vSphere 9 vs. vSphere 8

Memory tiering was introduced as a tech preview in vSphere 8.0 Update 3 and required manual configuration with limited monitoring. In vSphere 9.0, it reaches full GA status with significant improvements:

- Integrated monitoring in vCenter with per-host and per-VM tiering metrics
- vMotion awareness -- tiering state is preserved across migrations
- Encryption support for tiered pages
- Improved resiliency against NVMe device failures (graceful degradation to DRAM-only mode)
- Support in VCF lifecycle workflows -- SDDC Manager can configure tiering as part of cluster provisioning

If you experimented with tiering in vSphere 8 and were not confident enough to put it into production, vSphere 9 is the release where it becomes real.

---

## 4.4 DRS and HA in the VCF Context

### 4.4.1 The Death of vCLS

The most architecturally significant change to vSphere clustering in version 9.0 is the deprecation of vSphere Cluster Services (vCLS). Since vSphere 7 Update 1, DRS and HA functionality depended on small agent VMs that ran on every cluster. These vCLS VMs consumed resources, occasionally failed to deploy, interfered with host maintenance modes, and generated support tickets far out of proportion to their size.

In vSphere 9.0, vCLS is deprecated. The functionality that these VMs provided -- primarily a distributed key-value store for cluster state -- has been replaced by an embedded key-value store built directly into the ESXi 9.0 host. Each ESXi host now maintains cluster configuration and state data natively, without requiring agent VMs.

The practical impact is immediate:

- **No more vCLS VM deployment failures** blocking DRS initialization on new clusters.
- **No more resource consumption** by agent VMs (typically 3 VMs per cluster, each consuming a small amount of CPU and memory, but multiplied across dozens of clusters, the overhead was non-trivial).
- **Simplified maintenance mode** workflows -- you no longer need to wait for vCLS VMs to evacuate or worry about their anti-affinity rules.
- **Recommended action:** switch existing vCLS to retreat mode after upgrading to vSphere 9.0 to reclaim resources immediately.

Disabling vCLS has no impact on DRS or HA functionality in vSphere 9.0. This is not a feature removal -- it is an architectural improvement that eliminates a well-known operational pain point.

### 4.4.2 DRS Enhancements for GPU Workloads

DRS in vSphere 9.0 introduces GPU-aware placement intelligence. When a VM requests a vGPU profile, DRS considers not only CPU and memory utilization but also GPU availability and profile compatibility when making initial placement and load-balancing decisions.

Administrators can now reserve GPU capacity for future use through DRS policies, which is critical for AI/ML environments where GPU resources are scarce and expensive. This prevents a situation where DRS migrates a non-GPU workload onto a GPU-equipped host, consuming CPU and memory resources that should be reserved for the next GPU VM.

### 4.4.3 HA Improvements

vSphere HA in VCF 9.0 benefits from the same distributed state architecture that replaced vCLS. Cluster health state is now maintained in the embedded key-value store, which improves HA response times and eliminates a class of split-brain scenarios that occurred when vCLS VMs became unavailable.

The deployment and configuration of HA has been streamlined, with fewer manual steps required when enabling HA on a new cluster provisioned through SDDC Manager. In practice, HA configuration is handled as part of the workload domain provisioning workflow -- you define your admission control policy in the cluster specification, and SDDC Manager applies it consistently.

---

## 4.5 ESXi 9 Image Management: ImageDB and the End of Baselines

### 4.5.1 The Mandatory Shift to Image-Based Lifecycle Management

If you are still managing ESXi hosts using baselines -- the legacy approach inherited from vSphere Update Manager -- vSphere 9.0 forces your hand. **Baselines are deprecated in vSphere 9.0.** Clusters and standalone hosts managed by a vCenter 9.0 instance must use vSphere Lifecycle Manager (vLCM) images.

This is not a soft deprecation with a multi-release grace period. Broadcom has signaled that baseline support will be removed in the next major release. If your operational processes depend on manually curated baselines and scheduled remediation windows, now is the time to migrate.

### 4.5.2 How Image-Based Management Works

A vLCM image is a desired-state specification that defines the complete software stack for every host in a cluster:

- **ESXi base version** (e.g., ESXi 9.0.0)
- **VMware add-on components** (drivers, agents)
- **Vendor-specific components** (OEM drivers, firmware bundles from Dell, HPE, Lenovo, etc.)
- **Third-party components** (monitoring agents, security modules)

When you define an image for a cluster, vLCM compares the desired state against the actual state of every host and reports compliance. Remediation applies the image atomically -- the host reboots into the exact software specification you defined.

### 4.5.3 ImageDB and Stateless Operations

ESXi 9.0 maintains an internal image database (ImageDB) that tracks the installed software state of the host. This database is the ground truth for compliance checks -- when vLCM queries a host, it reads from ImageDB rather than scanning the filesystem.

For stateless deployments using Auto Deploy, vLCM images can be assigned to clusters with stateless hosts. When a stateless host boots, it pulls its image from the Auto Deploy server and populates ImageDB from the image specification. If the Auto Deploy server is unavailable, the host can boot from a cached image stored locally (stateless caching), and ImageDB is populated from the cache.

The practical benefit is cluster-wide host homogeneity. Every host in a cluster runs the exact same software stack, which eliminates the drift that inevitably occurs when hosts are patched individually over time. In a VCF environment, this homogeneity is not just a best practice -- it is a requirement. SDDC Manager enforces image compliance as part of its health checks and will flag non-compliant hosts during lifecycle operations.

---

## 4.6 CPU and Hardware Requirements: Who Gets Left Behind

### 4.6.1 Supported Processor Families

vSphere 9.0 supports the following processor families (this is not exhaustive, but covers the most common deployment scenarios):

**Intel:**
- Xeon Scalable 4th Generation (Sapphire Rapids) and later
- Xeon Scalable 5th Generation (Emerald Rapids)
- Xeon 6 (Granite Rapids)

**AMD:**
- EPYC 4th Generation (Genoa) and later
- EPYC 5th Generation (Turin)

### 4.6.2 Dropped Processor Families

The following processor families are **discontinued** in vSphere 9.0 and cannot be used:

**Intel (notable removals):**
- Xeon Scalable 1st Generation (Skylake-SP) -- Platinum, Gold, Silver, Bronze
- Xeon Scalable 2nd Generation (Cascade Lake-SP)
- Xeon D-1500 series
- Xeon E5 v4 (Broadwell) and earlier

**AMD (notable removals):**
- EPYC 1st Generation (Naples)
- EPYC 2nd Generation (Rome)

### 4.6.3 The Reasoning and the Brownfield Problem

Broadcom's rationale for aggressive CPU deprecation is twofold. First, older processors lack hardware security features (SGX improvements, SEV-SNP, TDX) that vSphere 9.0's confidential computing capabilities depend on. Second, maintaining driver and microcode support for aging silicon consumes engineering resources that Broadcom prefers to redirect toward current-generation platforms.

The reasoning is sound from an engineering perspective. The problem is operational. Many enterprise data centers refresh server hardware on five-to-seven-year cycles. A customer who purchased Cascade Lake servers in 2020 expected to run them until 2025 or 2026. Those servers cannot run ESXi 9.0.

If you are in this situation, your options are:

1. **Remain on vSphere 8.x** for unsupported hardware and run a mixed-version environment. This is operationally complex but technically feasible. Note that VCF 9.0 workload domains require ESXi 9.0 -- you cannot mix ESXi versions within a single workload domain.
2. **Accelerate hardware refresh** to bring all hosts onto supported silicon. This is the clean path but requires capital expenditure.
3. **Use the unsupported CPU override** during ESXi 9.0 installation (allowLegacyCPU boot option). This bypasses the CPU check but puts you in an unsupported configuration. I do not recommend this for production.

There is no comfortable answer here. Plan your hardware refresh timeline against your VCF upgrade timeline, and have the conversation with procurement early.

---

## 4.7 GPU Support for AI and VDI

### 4.7.1 The vGPU Architecture in vSphere 9

vSphere 9.0 supports GPU virtualization through three models:

- **GPU Passthrough (DirectPath I/O):** A physical GPU is exclusively assigned to a single VM. Maximum performance, zero sharing. This is the preferred model for AI training workloads.
- **NVIDIA vGPU:** A single physical GPU is partitioned into multiple virtual GPUs using NVIDIA's vGPU software. Each VM gets a vGPU profile that defines its share of GPU compute and memory. This is the standard model for VDI and inference workloads.
- **vSGA (Virtual Shared Graphics Acceleration):** A software-based shared graphics model. Lower performance, broader compatibility. Suitable for task workers who need basic 3D rendering.

### 4.7.2 What Changed in vSphere 9

Three improvements stand out for GPU-intensive environments:

**Fast Suspend/Resume (FSR) for vGPU VMs:** In previous versions, suspending a vGPU-enabled VM to apply a host patch required transferring the entire GPU frame buffer to disk. For a VM with two NVIDIA L40 GPUs (48 GB each), this took approximately 42 seconds. In vSphere 9.0, FSR reduces this to approximately 2 seconds. This transforms live patching operations for GPU clusters from a multi-hour maintenance window into a routine, minimally disruptive activity.

**6x Faster vMotion for GPU VMs:** The vGPU data channel has been re-architected with multiple parallel TCP connections and a zero-copy data path. Bandwidth peaks at 69 Gbps on 100G-equipped servers, compared to approximately 10 Gbps in vSphere 8.x. The stun time during vMotion -- the brief period when the VM is paused -- drops to roughly 1 second for a 24 GB GPU workload and approximately 2 seconds for a 48 GB workload.

**vGPU Profile Utilization Dashboard:** vCenter 9.0 introduces a consolidated view of all vGPU profiles across your GPU inventory. This sounds mundane, but in environments with hundreds of GPUs spread across multiple clusters, knowing which profiles are allocated, which are idle, and which hosts have capacity for a specific profile type is operationally critical. Previously, this required CLI queries or third-party tooling.

### 4.7.3 VMware Private AI Foundation with NVIDIA

VCF 9.0 integrates with VMware Private AI Foundation with NVIDIA (NVIDIA AI Enterprise 9.0), which provides a validated, supported stack for running AI workloads on virtualized infrastructure. The stack includes:

- NVIDIA GPU Operator for automated GPU driver lifecycle management in Kubernetes
- Pre-validated vGPU profiles for common AI frameworks (PyTorch, TensorFlow, vLLM)
- Integration with VKS for GPU-accelerated Kubernetes pods
- Support for NVIDIA H200 (virtualized) and Blackwell (passthrough/DirectPath I/O) GPUs

This is the path Broadcom is paving for enterprises that want to run AI workloads on their existing VCF infrastructure rather than building separate bare-metal GPU clusters.

---

## 4.8 vSphere Kubernetes Service (VKS)

### 4.8.1 What VKS Is and How It Works

vSphere Kubernetes Service (VKS) -- the successor to Tanzu Kubernetes Grid Service -- is VCF's native mechanism for provisioning and managing Kubernetes clusters. VKS runs on the vSphere Supervisor, which is a Kubernetes control plane embedded directly into vCenter and backed by ESXi hosts as worker nodes.

When a developer or platform team requests a Kubernetes cluster through VKS, the following occurs:

1. The Supervisor provisions a set of VMs on the target vSphere namespace (a resource-bounded partition of a cluster).
2. These VMs are bootstrapped as Kubernetes control plane and worker nodes using the Cluster API (CAPI) framework.
3. The resulting Kubernetes cluster is a conformant, upstream-compatible cluster that supports standard kubectl operations, Helm charts, and any CNCF-certified tooling.

VKS clusters are lifecycle-managed by the Supervisor. Kubernetes version upgrades, node scaling, and certificate rotation are handled through declarative specifications -- you update the desired state, and the Supervisor converges the cluster to match.

### 4.8.2 Persistent Volumes on vSAN

Kubernetes workloads that require persistent storage consume it through the vSphere CSI (Container Storage Interface) driver. In a VCF environment, the backing storage is vSAN.

VCF 9.0 introduces the **Volume Service**, which allows tenants to provision persistent volumes through VCF Automation. Two volume types are supported:

- **ReadWriteOnce (RWO):** Backed by vSAN block storage. A volume is attached to a single pod at a time. This is the standard model for databases and stateful applications.
- **ReadWriteMany (RWX):** Backed by vSAN File Shares. Multiple pods can mount the volume simultaneously. This is used for shared data scenarios -- machine learning training data, content management systems, shared configuration stores.

RWX volumes require vSAN File Services to be enabled on the Supervisor cluster. VCF 9.0 increases the per-cluster file share limit from 250 to 500, which addresses a scaling constraint that affected large multi-tenant deployments in VCF 5.x.

Storage policies are enforced at the tenant level. A platform administrator defines which vSAN storage policies are available to each tenant namespace, and quota enforcement prevents any single tenant from consuming more than their allocated share. This is the storage governance model that was missing from early Tanzu implementations, and it is now a first-class feature.

### 4.8.3 VKS and VCF Automation (VCFA) Integration

VCF Automation (VCFA) is the self-service portal layer of VCF 9.0. It allows platform teams to expose curated services -- VMs, Kubernetes clusters, databases, networking, container registries -- to application teams through a catalog-based interface.

As of VCF 9.0.1, VCFA includes native VKS Cluster Management (VKSM), which enables full lifecycle management of VKS clusters from the VCFA console: provisioning, scaling, upgrading, backup, and restore. This eliminates the need for application teams to interact with vCenter directly -- they request a Kubernetes cluster through VCFA, receive credentials, and deploy their workloads.

### 4.8.4 Argo CD: GitOps Comes to VCF

VCF 9.0 introduces a Broadcom-built Argo CD Operator as a Supervisor Service. Argo CD is the de facto standard for GitOps-based continuous delivery in the Kubernetes ecosystem, and its integration into VCF is significant.

Each Argo CD instance is scoped to a vSphere Namespace, which aligns with the Supervisor's multi-tenancy model. From a single Git repository, teams can declaratively manage:

- VKS cluster specifications (create, update, configure)
- Application deployments into VKS clusters
- VM workloads (declarative VM management via Kubernetes-style manifests)
- vSphere Pod configurations

The workflow is straightforward: a developer commits a change to a Git repository, Argo CD detects the change, and it automatically applies the desired state to the target environment. This is the same GitOps pattern used by hyperscaler-native Kubernetes platforms, and bringing it to VCF closes a significant gap for organizations that want to standardize on Git-driven infrastructure management.

For edge deployments, this is particularly powerful. VCF 9.0 supports single-node vSphere Supervisor, and Argo CD can manage application deployment across hundreds of edge locations from a central Git repository -- a pattern that eliminates the need for site-by-site manual configuration.

---

## 4.9 Performance Benchmarks: What the Numbers Actually Tell Us

### 4.9.1 MLPerf Inference v5.1: VCF Matches Bare Metal

In late 2025, Broadcom submitted VCF 9.0 results to the MLPerf Inference v5.1 benchmark suite, the industry-standard benchmark for AI inference performance administered by MLCommons. The results were submitted in collaboration with Dell, Intel, NVIDIA, and Supermicro.

The headline result: **VCF 9.0 achieved performance on par with bare-metal configurations** across six benchmark categories:

- Speech-to-Text (Whisper)
- Text-to-Video (Stable Diffusion XL)
- Large Language Models (Llama 3.1-405B and Llama 2-70B)
- Graph Neural Networks (R-GAT)
- Computer Vision (RetinaNet)

Hardware configurations included NVIDIA H200 GPUs in virtualized (vGPU) mode, Blackwell GPUs in passthrough mode, and Intel Xeon 6787P dual-socket CPUs for CPU-only inference.

### 4.9.2 What the Results Mean -- and What They Do Not

The MLPerf results demonstrate that the vSphere 9 hypervisor introduces negligible overhead for GPU-accelerated inference workloads. When GPUs are doing the heavy lifting, the CPU and memory consumed by the VM represent a small fraction of the host's total resources, leaving substantial capacity for additional non-GPU workloads. This is the core value proposition of virtualized AI: you do not dedicate an entire server to a single inference workload; you share the infrastructure.

However, I want to offer a few honest caveats:

**These are Broadcom-sourced benchmarks.** The submissions were made by Broadcom and its partners. While MLPerf is an independent framework with standardized rules, the hardware configurations and tuning parameters were chosen to showcase VCF's strengths. Real-world performance depends on your specific workload characteristics, network topology, storage configuration, and VM density.

**Inference is not training.** MLPerf Inference measures how fast a system can run a trained model against new data. AI training workloads -- which involve massive data movement, gradient synchronization across multiple GPUs, and sustained memory bandwidth demands -- are a different beast. Broadcom has not published comparable training benchmarks on VCF, and the overhead of virtualization is likely higher for multi-GPU training jobs that require RDMA and NVLink communication.

**Passthrough vs. vGPU matters.** The Blackwell GPU results used passthrough (DirectPath I/O), which bypasses the hypervisor's device emulation layer entirely. Passthrough delivers bare-metal GPU performance by definition. The H200 results used vGPU, which does incur a small hypervisor overhead, but for inference workloads, this overhead falls within the noise floor of measurement.

Take the MLPerf results as evidence that VCF is a viable platform for AI inference. Do not take them as a guarantee that your specific training pipeline will run at bare-metal speeds.

### 4.9.3 NSX Enhanced Data Path (EDP): The Networking Tax Gets Smaller

One of the persistent complaints about VCF has been the CPU overhead of NSX. The previous-generation NSX data path consumed host CPU cycles for every packet processed, and in high-throughput environments, that overhead was measurable -- sometimes painfully so.

VCF 9.0 makes NSX Enhanced Data Path (EDP) Standard the **default** host switch mode for all new workload domains and fresh NSX installations. EDP Standard delivers up to **3x higher switching performance** compared to the legacy NSX data path, with significantly lower CPU utilization per packet.

The technical improvements behind this number include:

- **EDP Flow Cache:** The first packet in a flow is processed through the full NSX pipeline (firewall rules, routing decisions, encapsulation). Subsequent packets in the same flow hit a cached action, bypassing the full pipeline. This is a run-to-completion optimization that eliminates redundant processing.
- **EDP Mbuf Framework:** The legacy PktHandle packet structure (which consumed significant cache space) is replaced by a 128-byte mbuf format that reduces cache footprint by approximately 50%. Fewer cache misses translate directly to higher packet throughput.
- **Optimized DPDK-style polling:** EDP uses a polling-based packet processing model rather than interrupt-driven processing, which reduces context switching overhead at high packet rates.

For compute workloads, the EDP improvement means that less host CPU is consumed by networking, leaving more capacity for VMs. In environments running 25 GbE or higher network interfaces with significant east-west traffic (which is most VCF deployments), the CPU savings from EDP can be equivalent to recovering one or two cores per host -- capacity that is now available for workload VMs.

EDP Standard is recommended for all VCF deployments, including general compute, network-intensive, and edge workloads. If you are upgrading from an older VCF version, enabling EDP on existing clusters requires a rolling reconfiguration of the host switch mode, which can be performed without VM downtime.

---

## 4.10 Putting It Together: Compute Capacity Planning in VCF 9.0

The compute layer in VCF 9.0 gives you more tools than any previous release, but it also demands more careful planning. Here is a summary of the key capacity planning decisions:

1. **CPU Selection:** Verify that your target hardware is on the vSphere 9.0 HCL. If you are running Cascade Lake or older, you cannot upgrade to VCF 9.0 without a hardware refresh.

2. **Memory Architecture:** Decide whether NVMe memory tiering is appropriate for your workloads. If yes, size the NVMe tier at a 1:1 ratio with DRAM for general workloads, and ensure that aggregate active memory does not exceed 50% of DRAM capacity.

3. **GPU Inventory:** Map your AI and VDI workloads to GPU profiles. Use passthrough for training, vGPU for inference and VDI. Factor in the FSR and vMotion improvements when calculating maintenance window durations.

4. **VM Hardware Version Strategy:** Do not upgrade VMs to Hardware Version 22 until all potential target clusters are running ESXi 9.0. Document your EVC baseline and communicate the compatibility cliff to application teams.

5. **Cluster Services:** After upgrading to vSphere 9.0, switch vCLS to retreat mode and reclaim the resources consumed by agent VMs.

6. **Image Management:** Migrate from baselines to vLCM images before the next major release removes baseline support entirely. This is a project, not a toggle -- allocate time for testing vendor-specific components.

7. **Kubernetes Readiness:** If you plan to offer Kubernetes-as-a-service through VCFA, size your Supervisor cluster with sufficient resources for VKS control plane VMs and the Argo CD operator. Budget vSAN capacity for persistent volumes, especially RWX volumes backed by File Shares.

8. **Network Overhead:** Enable EDP Standard on all workload domains to minimize the CPU tax of NSX. Measure before and after -- the improvement is real, and it directly increases your usable compute capacity.

---

## Summary

vSphere 9 is not a minor version bump. It redefines the compute contract in VCF: bigger VMs for scale-up workloads, NVMe memory tiering for cost-effective capacity expansion, a simplified clustering architecture without vCLS, GPU acceleration that finally makes live migration practical for AI workloads, and a Kubernetes platform that integrates GitOps natively. The CPU compatibility restrictions will cause pain in some environments, and the mandatory shift to image-based lifecycle management will force operational changes. Both are necessary trade-offs for a platform that is positioning itself as the enterprise standard for private cloud compute -- from traditional three-tier applications to large language model inference.

The compute layer is the foundation. In Chapter 5, we turn to the storage layer -- vSAN 9 -- and examine how it complements the compute capabilities discussed here.
