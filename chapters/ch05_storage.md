# Chapter 5 --- Storage (vSAN 9)

## The Storage Challenge in 2025

Every enterprise infrastructure team I work with is fighting the same war on multiple fronts. Data volumes are compounding at forty percent or more per year. Ransomware has moved from headline risk to operational certainty---sixty-seven percent of organizations surveyed in 2024 reported at least one successful encryption event. Regulatory frameworks like DORA, NIS2, and the SEC cyber-disclosure rules now demand provable immutability and auditable recovery points. And then there is AI: the moment a line-of-business leader decides to fine-tune a large language model on proprietary data, the storage conversation changes completely, because training pipelines consume bandwidth and IOPS at a scale that traditional SAN architectures were never designed to deliver.

Against this backdrop, the storage layer of VMware Cloud Foundation 9 has to accomplish something genuinely difficult. It must be dense enough to keep costs competitive with public-cloud block storage, fast enough for latency-sensitive inference workloads, resilient enough to survive both hardware failures and deliberate cyberattacks, and simple enough that a two-person infrastructure team can operate it without a dedicated storage administrator. vSAN 9, anchored by the Express Storage Architecture and a suite of new data-services capabilities, is VMware's answer to all four demands simultaneously.

This chapter walks through the full storage story: the object model that underpins every policy decision, the architectural split between ESA and the Original Storage Architecture, the headline features---global deduplication, native replication with immutable snapshots, expanded principal storage---and the operational tooling that keeps it all visible. If you are planning a greenfield VCF 9 deployment or migrating a brownfield vSAN 7 or 8 environment, this is the chapter you will return to most often.

---

## vSAN Fundamentals: The Object Model and Storage Policies

Before we examine what is new in vSAN 9, we need a shared vocabulary. vSAN is not a traditional file system layered on top of a RAID controller. It is an object store distributed across every host in the cluster, governed by policies that the hypervisor enforces automatically. Understanding the object model is essential for making correct sizing and availability decisions.

### Objects, Components, and Witnesses

When a virtual machine is provisioned on a vSAN datastore, each of its virtual devices---VMDK files, VM home namespace, swap file---becomes a discrete *object*. vSAN then decomposes each object into one or more *components* and distributes those components across hosts in the cluster according to the storage policy attached to the VM. If a mirroring policy is in effect, vSAN creates full copies of each component on separate hosts. If erasure coding is in effect, it stripes data and parity fragments across multiple hosts. In either case, a lightweight *witness* component is placed on a third host to break ties during a network partition.

This object-level granularity is what separates vSAN from array-based storage. A traditional SAN protects an entire LUN with a single RAID level; every VM on that LUN inherits the same protection whether it needs it or not. vSAN protects each VM---indeed, each virtual disk---independently. A mission-critical database can run RAID-1 with FTT=2 (triple mirror, tolerating two simultaneous failures) while a disposable build server on the same cluster uses RAID-5 with FTT=1, consuming a fraction of the capacity. The policies travel with the VM, surviving vMotion, storage vMotion, and even cross-cluster migration.

### Storage Policy Based Management (SPBM)

Storage Policy Based Management is the control plane that translates human intent into vSAN object placement. An administrator defines a policy---specifying parameters like failures to tolerate, failure tolerance method, stripe width, IOPS limit, and whether compression and deduplication are enabled---and attaches it to a VM or a class of VMs. vSAN's distributed runtime then continuously reconciles the actual placement of components with the desired state expressed in the policy. If a host fails and a component becomes non-compliant, vSAN automatically rebuilds it on a surviving host, provided sufficient capacity and fault domains exist.

SPBM is not unique to vSAN; it is a vSphere-wide framework that also governs Virtual Volumes and I/O filters. But vSAN is where SPBM reaches its fullest expression, because every data-placement and data-services decision---protection level, compression, deduplication, encryption, IOPS reservation---is driven by policy rather than by manual LUN configuration.

### Failures to Tolerate (FTT) and Failure Tolerance Method

The two most consequential policy parameters are *Failures to Tolerate* (FTT) and *Failure Tolerance Method* (FTM). Together, they determine how much redundancy vSAN builds into each object and how much raw capacity that redundancy consumes.

**RAID-1 (Mirroring).** When FTM is set to mirroring, vSAN creates FTT + 1 full copies of every component. FTT=1 means two copies across two fault domains; FTT=2 means three copies across five fault domains; FTT=3 means four copies across seven fault domains. Mirroring is write-efficient because each write operation touches only the mirror copies---there is no parity calculation. It is the right choice for write-intensive transactional workloads where every microsecond of write latency matters. The cost is capacity: FTT=1 with RAID-1 consumes 2x the logical data size.

**RAID-5/6 (Erasure Coding).** When FTM is set to erasure coding, vSAN stripes data and parity across hosts. RAID-5 (FTT=1) distributes data plus one parity fragment; RAID-6 (FTT=2) distributes data plus two parity fragments. The capacity overhead is dramatically lower: RAID-5 consumes roughly 1.33x the logical data size, while RAID-6 consumes 1.5x. The trade-off in the Original Storage Architecture was performance---every write required additional read-modify-write cycles to compute parity, adding latency. As we will see, the Express Storage Architecture eliminates this penalty entirely.

The minimum host counts deserve memorization:

| Protection Level | Minimum Hosts (OSA) | Minimum Hosts (ESA) | Capacity Overhead |
|---|---|---|---|
| RAID-1, FTT=1 | 3 | 3 | 2x |
| RAID-5, FTT=1 | 4 (4+1) | 3 (2+1 adaptive) | 1.33x (4+1) or 1.5x (2+1) |
| RAID-1, FTT=2 | 5 | 5 | 3x |
| RAID-6, FTT=2 | 6 | 6 | 1.5x |
| RAID-1, FTT=3 | 7 | 7 | 4x |

Note the critical difference for ESA: the adaptive RAID-5 scheme introduced in vSAN 8 ESA allows erasure coding on clusters as small as three hosts, using a 2+1 layout. When the cluster grows to six or more hosts, ESA automatically transitions to a more efficient 4+1 layout. This adaptive behavior means that small clusters no longer pay the full mirroring tax just because they lack enough hosts for traditional erasure coding.

---

## ESA vs. OSA: A Complete Comparison

The Express Storage Architecture, introduced in vSAN 8 and reaching maturity in vSAN 9, is not merely a performance optimization. It is a ground-up redesign of how vSAN ingests, protects, compresses, and places data. The Original Storage Architecture remains supported for existing deployments, but ESA is the strategic direction, and every new feature in vSAN 9---global deduplication, native replication, enhanced data path---requires ESA.

### Hardware Model

**OSA** organizes storage into *disk groups*. Each disk group contains exactly one high-performance caching device (typically an NVMe or SAS SSD) and one or more capacity devices (which can be SSDs or even spinning disks in all-flash or hybrid configurations). All write I/O flows through the cache device before being de-staged to capacity devices. This architecture was elegant for its era, but the cache device becomes a single point of failure for the entire disk group: if it fails, every capacity device in that group goes offline, triggering a large-scale rebuild.

**ESA** eliminates disk groups entirely. Every NVMe device in the host participates in a single *storage pool*. There is no dedicated cache tier; instead, ESA's log-structured file system (vSAN LFS) buffers incoming writes in a highly efficient log and prepares them for full-stripe writes directly to the NVMe devices. The hardware requirement is straightforward: all storage devices must be NVMe-based TLC (or better) flash, and the server must be on the vSAN ESA ReadyNode list. There are no spinning disks, no SAS SSDs, and no separate cache/capacity distinction.

### Performance Characteristics

The elimination of the cache bottleneck, combined with the log-structured write path, gives ESA a fundamentally different I/O profile. Random write latency drops significantly because writes are absorbed into the log and coalesced into sequential full-stripe writes rather than being funneled through a single cache device. Random read performance improves because any NVMe device in the pool can serve data directly, distributing read load across all devices rather than concentrating it on whichever capacity device holds the requested block.

The most striking performance claim is around erasure coding. In OSA, RAID-5/6 carried a well-documented write penalty: each write required reading existing data and parity, computing new parity, and writing both. This made RAID-5/6 measurably slower than RAID-1 for write-heavy workloads, and many administrators defaulted to mirroring despite the capacity cost. In ESA, the log-structured write path enables full-stripe writes that bypass the read-modify-write cycle entirely. VMware's guidance for ESA clusters is unambiguous: use RAID-5/6 erasure coding for all cluster types and sizes that support it, because there is no longer a performance penalty.

In vSAN 9, the Enhanced Data Path pushes performance further. Through kernel-level optimizations and optional offload to Data Processing Units (DPUs), vSAN 9 ESA can deliver up to three times faster data transfer speeds compared to the previous generation, with lower latency and reduced CPU overhead. DPUs can offload compression, encryption, and deduplication processing, freeing host CPUs for application workloads---a critical advantage when those CPUs are sharing time with GPU-accelerated AI inference.

### Compression and Deduplication Behavior

**OSA** performs compression and deduplication together as a post-process operation at the disk-group level. When enabled, data is first written to the cache tier, then de-staged to capacity devices where it is compressed and deduplicated within the boundary of a single disk group. The deduplication domain is therefore limited to the data on one disk group on one host---a relatively small population of blocks, which limits deduplication ratios for most workloads. Compression operates at a 4 KB block granularity and achieves roughly 2:1 ratios on typical mixed workloads.

**ESA** transforms both capabilities. Compression is applied inline---before data is persisted---and operates on each 4 KB block with an improved algorithm that can achieve up to 4x better compression ratios than OSA. Because there is no cache tier bottleneck, inline compression does not create a performance chokepoint.

Deduplication in ESA, as of vSAN 9, operates at the *cluster-wide* level, which we cover in depth in the next section.

### Failure Domain Impact

In OSA, losing a cache device means losing the entire disk group. If a host has two disk groups, each with five capacity devices, a single cache-device failure takes five capacity devices offline and forces vSAN to rebuild all data that was stored on those devices. The blast radius is large.

In ESA, there is no cache device to lose. A single NVMe device failure affects only the data stored on that specific device. The blast radius is dramatically smaller, which means faster rebuilds, lower rebalancing I/O, and less risk of a second failure during the rebuild window.

### Ideal Workloads and Cost Considerations

**OSA** remains appropriate for existing deployments that cannot justify a hardware refresh, environments that still use hybrid (SSD + HDD) configurations, and edge sites where NVMe ReadyNode hardware is not yet available.

**ESA** is the right choice for all new deployments, particularly those requiring high IOPS (databases, VDI, AI/ML pipelines), maximum space efficiency (erasure coding without performance penalty, cluster-wide deduplication), and resilience against ransomware (native replication with immutable snapshots, which requires ESA).

From a cost perspective, ESA hardware---all-NVMe ReadyNodes---carries a higher per-host acquisition price than a comparable OSA configuration with SAS SSDs. However, the total cost of ownership frequently favors ESA because: (1) erasure coding at RAID-1 performance reduces raw capacity requirements by 33--50 percent; (2) cluster-wide deduplication and improved compression deliver additional capacity savings; and (3) the simplified operational model---no disk groups to manage, smaller failure domains, fewer rebuilds---reduces ongoing labor costs.

### Migration Path

VMware does not support in-place conversion from OSA to ESA. The migration path is to add new ESA-capable hosts to the cluster (or build a new ESA cluster), migrate workloads using vMotion or storage vMotion, and then decommission the OSA hosts. In a VCF environment managed by SDDC Manager, this process is orchestrated through workload-domain lifecycle operations, allowing a phased transition with no application downtime.

---

## Global Cluster-Wide Deduplication: The Full Story

If there is a single feature in vSAN 9 that justifies the upgrade conversation, it is global cluster-wide deduplication. Previous versions of vSAN ESA performed deduplication within a single host. vSAN 9 expands the deduplication domain to the *entire cluster*, which fundamentally changes the economics of storage-dense workloads.

### What Changed

In vSAN 8 ESA, deduplication operated on a per-host basis. Each host independently identified duplicate 4 KB blocks within its own storage pool and eliminated redundant copies. This was effective for workloads where duplicate data happened to land on the same host, but it missed duplicates spread across multiple hosts---which is the norm in large-scale VDI, dev/test, and template-heavy environments.

vSAN 9 ESA makes the *cluster* the deduplication domain. Every 4 KB block written anywhere in the cluster is hashed and compared against a cluster-wide metadata index. When a duplicate is detected, the redundant copy is eliminated and replaced with a pointer to the single remaining instance. As hosts are added to the cluster, the deduplication domain grows automatically, increasing the probability of finding duplicates and improving the overall deduplication ratio over time.

### Technical Mechanism

The implementation is built on two new object types that are instantiated when global deduplication is enabled:

1. **Deduplication metadata objects.** These maintain a hash entry for every 4 KB chunk across the cluster. The metadata index is the lookup table that determines whether a newly written block is a duplicate of an existing block. The metadata objects are distributed across hosts for both performance and resilience.

2. **Deduplication data objects.** When a block is identified as a duplicate, the canonical copy of the data is moved into a deduplication data object. These objects are *sharded* across hosts in a way that preserves spatial locality---contiguous blocks from the same source are kept together in the same shard wherever possible. This design minimizes read fragmentation: when a VM performs a large sequential read, the storage layer can satisfy it from a single shard rather than gathering fragments from many hosts.

All deduplication activity occurs *after* data has been persisted to storage (post-process), which minimizes the impact on guest VM write latency. The process is intelligent about data temperature: vSAN's metadata mapping distinguishes recently written (hot) data from cold data and always prioritizes deduplication of cold data first. This prevents the engine from wasting cycles deduplicating blocks that are likely to be overwritten again shortly.

### Space Savings Math: A VDI Example

Consider a 500-desktop VDI deployment on a ten-host ESA cluster. Each desktop has a 60 GB virtual disk based on a common Windows 11 gold image, plus 20 GB of user-profile data. The gold image is effectively identical across all 500 desktops---roughly 55 GB of the 60 GB VMDK is shared content.

Without deduplication, the raw storage consumed by desktop VMDKs alone is 500 x 60 GB = 30 TB. With cluster-wide deduplication, the 55 GB of shared gold-image content is stored once, plus 500 copies of the roughly 5 GB of per-desktop deltas: 55 GB + (500 x 5 GB) = 2.555 TB. That is a deduplication ratio of approximately 11.7:1 on the VMDK data alone. Add the 10 TB of user-profile data (which typically deduplicates at 1.5--2:1 because of common application caches, temp files, and Office templates), and the blended ratio across the environment lands in the 4--6:1 range.

Broadcom's published guidance states that cluster-wide deduplication can reduce storage footprint by up to 8x, depending on data characteristics. In my field experience, VDI and dev/test workloads routinely achieve 4--8x, while more heterogeneous production environments see 1.5--3x. The key insight is that per-host deduplication might achieve 1.5--2x on the same VDI workload because the gold-image copies are distributed across ten hosts, each host holding only fifty instances. Cluster-wide deduplication sees all five hundred instances at once and collapses them to a single canonical copy.

### Inline vs. Post-Process

A common question is why vSAN 9 uses post-process deduplication rather than inline. The answer is deliberate: inline deduplication requires a hash lookup on every write in the I/O hot path, which adds latency to every guest write operation. Post-process deduplication absorbs writes at full speed, persists them, and then scans for duplicates asynchronously during periods of lower I/O activity. The trade-off is that the cluster temporarily consumes more capacity before deduplication reclaims space, but the performance impact on production workloads is negligible.

This is a different design decision from most traditional storage arrays, which favor inline deduplication. The rationale is that array controllers have dedicated hardware ASIC pipelines for hash computation, so the latency cost of inline dedup is minimal. vSAN runs in software on general-purpose CPUs (or DPUs), where the hash computation competes with application workloads for cycles. Post-process is the correct engineering choice for a software-defined architecture.

### Release Status and Requirements

Global cluster-wide deduplication shipped in vSAN 9.0 as a *limited-availability* feature, meaning it requires a Request for Product Qualification (RPQ) through your VMware account team. The limited-availability designation reflects the fact that the initial release does not support all topologies---stretched clustering, for example, is excluded. Check with your TAM or account team for the latest supportability matrix before enabling in production. In vSAN 9.0.1, the feature moved closer to general availability with expanded topology support.

---

## Native vSAN-to-vSAN Replication

### How It Works

vSAN 9 introduces native asynchronous replication between vSAN ESA clusters, built on vSAN Data Protection's snapshot technology. Unlike traditional array-based replication, which operates at the LUN or volume level, vSAN replication operates at the VM level. An administrator creates a *protection group*, assigns VMs to it, specifies an RPO (as low as one minute), and designates a target vSAN ESA cluster. vSAN then captures crash-consistent snapshots of the protected VMs at the specified interval and replicates the changed blocks to the target cluster.

The replication leverages vSAN ESA's native snapshot engine, which produces *deep snapshots*---snapshots that are first-class objects in the vSAN object model rather than redo logs chained to a base disk. This is a critical distinction from traditional vSphere snapshots, which degrade performance as the chain length grows. vSAN deep snapshots maintain consistent read and write performance regardless of how many snapshots exist, because they are implemented as metadata pointers into the log-structured file system rather than as sequential delta chains.

### Immutable Snapshots: Up to 200 Per VM

Each protection group can retain up to 200 snapshots per VM. When the snapshot count exceeds 200, the oldest snapshots are automatically expired. Critically, these snapshots can be configured as *immutable*---a simple toggle in the protection-group settings. An immutable snapshot cannot be modified, deleted, or encrypted by any process, including a compromised administrator account or a ransomware payload running inside the guest.

The 200-snapshot limit is generous by any standard. With a one-minute RPO, 200 snapshots provide over three hours of granular recovery points. With a 15-minute RPO---a more common configuration for general-purpose workloads---200 snapshots provide 50 hours of recovery history. With an hourly RPO, the retention window extends to over eight days. Administrators can mix RPO intervals across protection groups to balance granularity against storage consumption.

### Ransomware Recovery Workflow

The combination of immutable snapshots and native replication creates a purpose-built ransomware recovery workflow:

1. **Detection.** An anomaly-detection system (VMware Advanced Cyber Compliance, a third-party EDR, or even a manual alert) identifies a potential encryption event.

2. **Isolation.** The affected VMs are quarantined on the source cluster using NSX microsegmentation or by powering them off.

3. **Recovery-point selection.** The administrator navigates to the target cluster's snapshot catalog and identifies the last known-good snapshot before the encryption event began. Because snapshots are immutable, the attacker cannot have tampered with them.

4. **Restoration.** The VM is recovered from the selected snapshot on the target cluster---or, if the source site is compromised, directly at the recovery site. Because the snapshot is a full vSAN object, recovery is a metadata operation that completes in seconds; the VM can power on immediately and lazily populate data blocks on first access.

5. **Validation.** The restored VM is booted in an isolated network segment for forensic validation before being placed back into production.

### VMware Live Recovery Integration

vSAN-to-vSAN replication integrates directly with VMware Live Recovery (VLR), the unified disaster-recovery and cyber-recovery orchestration platform in VCF 9. VLR provides automated recovery plans---scripted failover and failback sequences with dependency ordering, IP re-mapping, and validation scripts---on top of the raw snapshot replication.

A key operational benefit of this integration is data-transfer efficiency. VLR, combined with vSAN Data Protection's snapshot-level replication, can reduce data transfer during a disaster or cyber recovery event by 93 to 96 percent compared to full-VM replication, depending on VM size and rate of change. For an organization with hundreds of VMs, this translates to tens or hundreds of terabytes less data transferred during recovery---a transformative reduction in both egress costs and time to recovery.

In VCF 9, VLR ships as a single unified appliance that manages all recovery operations: vSphere-based enhanced replication for site-to-site DR, vSAN-based snapshot replication for cyber recovery, and orchestrated failover/failback across both. This consolidation eliminates the operational overhead of managing separate Site Recovery Manager and vSAN replication appliances.

### RPO and RTO Expectations

| Scenario | Typical RPO | Typical RTO |
|---|---|---|
| Critical databases (SQL, Oracle) | 1--5 minutes | Minutes (snapshot mount + lazy hydration) |
| General production VMs | 15--30 minutes | Minutes |
| Dev/test environments | 1--4 hours | Minutes to tens of minutes |
| Cyber recovery (immutable snapshots) | Varies by retention policy | Minutes (plus validation time) |

The RTO is consistently in the minutes range because recovery from a vSAN snapshot is a metadata operation---the VM can boot immediately and read data on demand from the snapshot's underlying objects. There is no need to hydrate an entire VMDK before the VM becomes operational.

---

## Expanded Principal Storage

### Why It Matters

In previous versions of VMware Cloud Foundation, vSAN was the only supported option for *principal storage* in the management workload domain during an automated (greenfield) deployment through SDDC Manager. Supplemental storage---NFS, iSCSI, Fibre Channel---could be added afterward for workload domains, but the management domain had to start with vSAN. This requirement meant that every VCF deployment began with a minimum of three vSAN-capable hosts in the management domain, even if the organization had an existing enterprise SAN with abundant capacity.

VCF 9.0 changes this equation. NFSv3 and Fibre Channel VMFS datastores are now supported as principal storage for the management workload domain in greenfield deployments initiated through the VCF Installer. This is not just a licensing concession---it reflects a deliberate architectural decision to make VCF accessible to organizations whose storage strategy is built around external arrays.

### NFS and FC: Now First-Class Citizens

With NFS or FC as principal storage, the minimum host count for the management domain drops from three (required for vSAN) to two. This reduces the entry cost for a VCF deployment significantly and allows organizations to leverage existing investments in NetApp, Pure Storage, Dell PowerStore, or any other array that presents NFSv3 or FC-backed VMFS datastores.

NFSv3 is supported for both management and workload domains as principal or supplemental storage. NFSv4.1, which adds stronger authentication (Kerberos) but introduces additional complexity, is supported only as supplemental storage in the current release.

For storage types not available in the greenfield installer---iSCSI, NFS v4.1, FCoE, and NVMe over Fabrics (FC, TCP, or RDMA transport)---alternative configuration methods are documented for adding them as principal storage to workload domains post-deployment.

### Decision Guide: vSAN vs. External Principal Storage

The choice between vSAN and external storage as principal storage is not binary; many organizations use both, with vSAN for workload domains where HCI simplicity and linear scale-out are valued, and external arrays for the management domain or for workloads with specific array-based features (synchronous replication, inline deduplication at the array controller, etc.).

**Choose vSAN when:** You want a single operational model across compute and storage. You are building a greenfield environment with no existing SAN. You need the data-protection features described in this chapter (native replication, immutable snapshots). You want linear scale-out---adding a host adds both compute and storage simultaneously.

**Choose external NFS/FC when:** You have an existing enterprise SAN with available capacity and expertise. Your management domain needs to be as small as possible (two hosts). You require array-specific features (synchronous metro replication, storage-level encryption with key management, array-level snapshots for compliance). Your organization has a dedicated storage team that manages the array lifecycle independently.

**Hybrid approach:** Use external storage for the management domain (smaller footprint, leverages existing array) and vSAN ESA for workload domains (linear scale-out, native data protection, cluster-wide deduplication). This pattern is increasingly common among customers I advise who are modernizing gradually.

---

## Storage for AI Pipelines

The intersection of private-cloud infrastructure and artificial intelligence is no longer theoretical. Organizations are deploying GPU-accelerated inference clusters inside VCF to maintain data sovereignty, avoid public-cloud egress costs, and comply with regulations that prohibit sending sensitive data to third-party model providers. The storage layer is often the bottleneck that determines whether these workloads succeed or fail.

### Training vs. Inference: Different Storage Profiles

**Model training** is bandwidth-hungry and moderately latency-tolerant. A training pipeline reads massive datasets---often tens or hundreds of terabytes---in sequential sweeps, epoch after epoch. The storage layer must sustain high sequential read throughput (multiple GB/s per GPU node) and store large datasets efficiently. Latency on individual I/O operations matters less than aggregate bandwidth, because the training framework pipelines data loading with GPU computation.

**Real-time inference** is latency-sensitive and IOPS-intensive. An inference server loads a model into GPU memory at startup (a large sequential read) and then serves predictions against a stream of small, random input requests. The storage layer must deliver low-latency random reads for model loading and checkpoint retrieval, with consistent sub-millisecond response times under concurrent load.

vSAN ESA's all-NVMe architecture serves both profiles. The log-structured write path and full-stripe reads deliver high sequential throughput for training data ingestion. The distributed storage pool, with every NVMe device available to serve read requests, delivers the low-latency random-read performance that inference workloads demand. The Enhanced Data Path in vSAN 9, with optional DPU offload, reduces CPU overhead further---critical when host CPUs are already sharing duty with GPU management and vMotion operations.

### NVMe Tiering as AI Accelerant

VCF 9 also introduces advanced memory tiering that allows NVMe devices to extend the memory tier. With a default DRAM-to-NVMe ratio of 1:1, a host with 1 TB of DRAM and NVMe-backed memory tiering can present 2 TB of addressable memory to VMs. For inference workloads running large language models, this can mean the difference between fitting a model entirely in "memory" versus paging model weights from storage---a latency cliff that degrades inference throughput dramatically.

The combination of NVMe storage for persistent data and NVMe memory tiering for hot data creates a two-tier NVMe architecture where the same physical drives accelerate both storage I/O and memory access. This is unique to the VCF stack and represents a compelling argument for consolidating AI workloads onto the same infrastructure that runs traditional enterprise applications.

### The Data Sovereignty Argument

Organizations in regulated industries---financial services, healthcare, government, defense---face a structural constraint: the data they want to use for AI cannot leave their premises. Public-cloud AI services are off the table for sensitive datasets. VCF 9 with vSAN ESA provides the storage foundation for a private AI platform that keeps data on-premises, under the organization's physical and logical control, while delivering the performance characteristics that GPU-accelerated workloads require.

vSAN ESA's support for GPUDirect Storage over RDMA takes this further by allowing direct I/O transfer from NVMe storage to GPU memory without CPU involvement. This eliminates a significant bottleneck in the training data pipeline and brings on-premises AI storage performance closer to the purpose-built GPU clusters offered by cloud providers.

---

## vSAN Health and Monitoring

A storage system that you cannot observe is a storage system that will eventually surprise you. vSAN has invested heavily in operational visibility, and VCF 9 brings the monitoring story together under the VCF Operations umbrella.

### The vSAN Health Service

The vSAN Health Service is the first-line monitoring layer, integrated directly into the vSphere Client. It runs a comprehensive suite of health checks organized into categories:

- **Hardware compatibility.** Validates that every host's storage controllers, NVMe devices, and firmware versions are on the vSAN Hardware Compatibility List (HCL). An incompatibility here is not advisory---it is a support boundary.

- **Network configuration.** Verifies that vSAN VMkernel adapters are configured correctly, that multicast (for OSA) or unicast (for ESA) communication is functioning, and that network latency between hosts is within acceptable bounds.

- **Cluster configuration.** Checks for misconfigured time synchronization, inconsistent advanced settings across hosts, and missing ESXi patches.

- **Storage device health.** Monitors SSD/NVMe wear indicators (percentage of rated write endurance consumed), identifies devices reporting uncorrectable errors, and flags devices that are approaching end of life.

- **Data health.** Verifies that all vSAN objects are in a compliant state---that is, their actual placement matches the policy requirements. Objects that are temporarily non-compliant (e.g., during a rebuild after a host failure) are flagged with an expected completion time.

- **Stretched-cluster health.** For stretched-cluster configurations, validates witness connectivity, inter-site latency, and site-awareness correctness.

Each health check produces a status: green (pass), yellow (warning), or red (critical). The vSAN Health Service dashboard aggregates these statuses into an at-a-glance cluster health score, making it easy to identify clusters that need attention during a morning operational review.

### Key Metrics to Monitor

Beyond the health checks, vSAN exposes a rich set of performance metrics that should be part of every monitoring strategy:

- **IOPS** (read and write, by cluster, host, disk group/storage pool, and individual device). Sudden IOPS spikes may indicate runaway processes or backup jobs; sustained high IOPS near device limits may indicate the need for additional hosts.

- **Latency** (read and write, average and percentile). vSAN ESA clusters should sustain average write latency well under one millisecond for most workloads. Sustained latency above two milliseconds warrants investigation.

- **Throughput** (MB/s). Critical for sequential workloads like backups, data migrations, and AI training pipelines.

- **Congestion.** vSAN's internal congestion metric (0--255) reflects back-pressure in the storage stack. Values above 30 indicate that the cluster is struggling to keep up with I/O demand. Persistent congestion above 60 is a capacity or performance planning action item.

- **Resync traffic.** After a host failure, maintenance event, or policy change, vSAN resyncs data to restore compliance. Monitoring resync traffic helps administrators predict when the cluster will return to a fully protected state.

- **Deduplication and compression ratios.** With cluster-wide deduplication enabled, these ratios directly affect usable capacity projections. A sudden drop in the deduplication ratio may indicate a change in workload mix.

### The vSAN Observer Tool

The vSAN Observer is a diagnostic and performance-analysis tool that ships with vSAN and provides deep visibility into the storage stack. Unlike the Health Service, which focuses on status and alerting, Observer provides real-time and historical performance data at a level of granularity suited to troubleshooting:

- **Per-device latency histograms.** Identify individual NVMe devices that are contributing disproportionate latency, which may indicate firmware issues or early device degradation.

- **IOPS and bandwidth per device.** Confirm that I/O is distributed evenly across all devices in the storage pool. Hotspots may indicate policy misconfiguration or a rebalancing need.

- **vSAN memory-pool consumption.** Monitor the memory allocated to vSAN's in-kernel caches and metadata structures. Excessive memory consumption may indicate an unusually large object count or a deduplication metadata index that is growing faster than expected.

- **Network-level statistics.** Per-VMkernel-adapter throughput, retransmits, and latency measurements help isolate storage performance issues caused by network contention rather than device limitations.

Observer runs as an on-demand diagnostic tool---it is typically activated during troubleshooting sessions rather than running continuously. For continuous monitoring, VCF Operations provides a more sustainable operational model.

### VCF Operations and the Observability Workbench

VCF Operations (the evolution of vRealize Operations, now integrated into the VCF platform) provides the enterprise-grade monitoring and analytics layer for vSAN. In VCF 9, VCF Operations includes purpose-built dashboards for storage operations:

**Storage Operations View.** A unified operational overview for all storage types powering the VCF environment---vSAN, NFS, FC, iSCSI---in a single pane. This view surfaces storage-related alerts, capacity forecasting, and performance trends across the entire VCF instance.

**vSAN Health Dashboard.** Aggregates cluster health scores from the vSAN Health Service and presents them alongside performance and capacity metrics. Clusters are categorized by health score: 81--100 (green, healthy), 61--80 (yellow, degraded), and 0--60 (red, unhealthy, requiring immediate attention).

**vSAN Performance Dashboards.** Separate dashboards for ESA and OSA clusters, with a "Metrics Picker" feature that allows operators to drag and drop metrics into chart areas for ad-hoc analysis. Selected metrics are displayed in time-series format for any cluster or host, enabling rapid correlation of performance events.

**The Observability Workbench.** This is the troubleshooting power tool within VCF Operations. It allows operators to generate support bundles, use Log Assist to correlate logs and metrics across the storage, compute, and networking layers, and build custom investigation dashboards during an incident. The Workbench integrates log data, metric data, and topology data into a single investigation workflow, reducing the mean time to diagnose storage performance issues from hours to minutes.

The combination of the vSAN Health Service (real-time, in-client), the Observer tool (deep diagnostic), and VCF Operations (enterprise analytics and workbench-based investigation) creates a three-tier observability model that scales from a single-admin small deployment to a multi-team enterprise operations center.

---

## Putting It All Together

vSAN 9 is not a point release. It is the version where VMware's software-defined storage story reaches the level of capability and maturity that makes it a credible replacement for dedicated storage arrays in most enterprise use cases. Global deduplication eliminates the last major space-efficiency gap. Native replication with immutable snapshots delivers ransomware protection that was previously available only through third-party backup products. Expanded principal storage removes the vSAN-only entry requirement that limited VCF adoption in SAN-heavy shops. And the Enhanced Data Path with DPU offload positions vSAN as the storage foundation for the next wave of AI workloads running on private infrastructure.

The decision framework is straightforward:

- **New deployments:** ESA, always. Use RAID-5/6 erasure coding (the performance penalty is gone). Enable cluster-wide deduplication for VDI, dev/test, and any workload with significant data commonality. Configure native replication with immutable snapshots to at least one secondary site.

- **Existing OSA deployments:** Plan the migration to ESA. Add ESA hosts, migrate workloads, decommission OSA hosts. The migration is non-disruptive but requires ESA-qualified hardware.

- **Mixed environments:** Use external NFS or FC for the management domain if you have an existing array. Use vSAN ESA for workload domains. Let SPBM policies enforce per-VM protection levels.

- **AI initiatives:** Validate that your ESA ReadyNodes include NVMe devices with sufficient endurance for training-write patterns. Evaluate memory tiering to extend addressable memory for inference VMs. Test GPUDirect Storage over RDMA if your GPU nodes support it.

The chapters that follow will build on this storage foundation as we examine networking (Chapter 6) and workload management (Chapter 7). Storage policy decisions made here ripple through every subsequent layer of the VCF stack---get them right, and the rest of the platform operates within well-understood guardrails.
