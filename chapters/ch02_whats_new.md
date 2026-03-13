# Chapter 2: What's New in VMware Cloud Foundation 9

VMware Cloud Foundation 9.0, generally available as of June 17, 2025, is not an incremental update. It is an architectural reset. Every major subsystem---compute, storage, networking, automation, operations, lifecycle management, and the developer toolchain---has been rethought, restructured, or outright replaced. If you have spent years building muscle memory around SDDC Manager workflows, Cloud Builder spreadsheets, and Aria Suite product names, VCF 9 will feel unfamiliar at first. That unfamiliarity is deliberate. Broadcom has rebuilt the platform around two gravitational centers: *operations* and *consumption*. Everything else---the hypervisor, the distributed storage, the network virtualization layer---is now positioned as core innovation that those two centers orchestrate, rather than as standalone products that administrators stitch together through manual integration.

This chapter walks through every significant change in VCF 9.0 and its early maintenance releases, organized so that each feature receives the treatment it deserves: what it is, how it works, and why it matters to the practitioners who will deploy and operate it. Whether you are evaluating the upgrade from VCF 5.2, planning a greenfield deployment, or simply trying to map old terminology to new, this chapter is your definitive reference.

---

## The Philosophy Shift: Operations and Consumption as the Center of Gravity

Before we examine individual features, it is worth pausing on the design philosophy that animates VCF 9. In prior releases, VMware Cloud Foundation was, at its core, a lifecycle and deployment wrapper around a collection of best-of-breed products: vSphere for compute, vSAN for storage, NSX for networking, and a loosely coupled set of management tools (vRealize / Aria) that sat alongside the stack. The integration was real but often felt bolted on. SDDC Manager handled Day 0 and Day 1. Aria handled Day 2. vCenter handled the hypervisor. Customers toggled between three, four, sometimes five different management consoles to accomplish what should have been a single workflow.

VCF 9 inverts that model. The platform now presents two primary surfaces to its users:

1. **VCF Operations** --- the *operations plane* --- where infrastructure administrators build, monitor, patch, secure, and govern the private cloud.
2. **VCF Automation** --- the *consumption plane* --- where application teams, DevOps engineers, and line-of-business owners request and consume infrastructure through self-service catalogs, policy-driven guardrails, and Infrastructure as Code pipelines.

Everything beneath those two planes---ESXi hosts, vSAN datastores, NSX segments, vCenter instances---still exists and still matters, but the administrative center of gravity has shifted upward. You no longer start your day in SDDC Manager. You start it in VCF Operations. You no longer hand-craft networks in the NSX Manager UI for every tenant. You define VPC policies in VCF Automation and let the platform instantiate them. This is the single most important conceptual shift in VCF 9, and every feature discussed in this chapter flows from it.

---

## The New Hierarchy: Fleet, Instance, Private Cloud

### What It Is

VCF 9 introduces a formal, five-level hierarchy for describing your infrastructure:

| Level | Definition |
|---|---|
| **VCF Private Cloud** | The highest organizational boundary. A private cloud contains one or more fleets and represents the entirety of an organization's VCF footprint. |
| **VCF Fleet** | A set of VCF instances managed by a single deployment of fleet-level management components (VCF Operations and VCF Automation). A fleet is the governance and shared-services umbrella. |
| **VCF Instance** | A discrete VCF deployment containing its own management domain, its own SDDC Manager back end, and optionally one or more workload domains. Instances are often aligned to a physical site or region. |
| **VCF Domain** | A lifecycle and workload isolation boundary within an instance. Every instance has exactly one management domain; additional VI workload domains are optional. |
| **vSphere Cluster** | The familiar grouping of ESXi hosts within a domain, providing compute, storage, and network resources. |

### How It Works

The hierarchy is more than just naming. It defines *who owns what*. Fleet-level management components---VCF Operations, VCF Automation, the Identity Broker---sit above individual instances. They provide centralized certificate management, password rotation, license administration, configuration drift detection, and lifecycle orchestration across every instance in the fleet. But they do not merge instance-level control planes. Each instance retains its own vCenter, its own NSX Manager, and its own SDDC Manager back end. This is a critical architectural distinction: the fleet centralizes governance without collapsing operational autonomy.

Fleet topologies are flexible. A single-site organization might run one fleet containing one instance. A global enterprise might run one fleet spanning instances in Frankfurt, Singapore, and Virginia---or separate fleets per region with distinct governance boundaries. The SSO trust boundary defines the outer edge of a fleet; instances within a fleet share an SSO domain, while separate fleets maintain independent identity fabrics.

### Why It Matters

For years, customers managing multiple VCF deployments had no formal construct to represent "all of my VCF infrastructure." Each SDDC Manager was an island. Patching ten sites meant logging into ten consoles. The fleet construct eliminates that fragmentation. It gives infrastructure architects a vocabulary that maps cleanly to organizational structures---regions, business units, compliance zones---and it gives operations teams a single pane through which to enforce consistency at scale.

---

## Terminology Changes: A Complete Reference

VCF 9 renames or replaces several foundational components. The following table consolidates every major terminology change:

| VCF 5.x / Prior Name | VCF 9.0 Name | Notes |
|---|---|---|
| Cloud Builder | **VCF Installer** | New appliance; UI-driven workflow replaces the Deployment Parameters Workbook (Excel spreadsheet). |
| SDDC Manager (UI) | **VCF Operations** (fleet management section) | The SDDC Manager UI is deprecated in VCF 9.0. SDDC Manager persists as a back-end component but will be fully deprecated in a future release. |
| VMware Aria Operations | **VCF Operations** | Monitoring, observability, cost management, and fleet governance consolidated under one brand. |
| VMware Aria Automation | **VCF Automation** | Self-service cloud consumption, catalog, IaC, and tenant management. |
| VMware Aria Suite Lifecycle | **VCF Fleet Management** | Lifecycle management of fleet-level components. |
| VMware Aria Operations for Logs | **VCF Operations for Logs** | Centralized log analytics, now accessed from within VCF Operations. |
| VMware Aria Operations for Networks | **VCF Operations for Networks** | Network observability and planning, also accessed from within VCF Operations. |
| vCloud Director (VCD) | **VCF Automation** (tenant model) | Service provider multi-tenancy migrated to the VCFA provider/org model. |
| VMware Site Recovery Manager | **VMware Live Recovery** | DR orchestration rebranded and expanded with vSAN-native replication. |
| PowerCLI (VMware.PowerCLI) | **VCF PowerCLI (VCF.PowerCLI)** | Module renamed; new versioning aligned to VCF releases. |

Understanding these name changes is essential not just for navigating the new UI but for reading release notes, searching knowledge bases, and communicating with VMware support. When a colleague mentions "Aria Operations," they mean VCF Operations. When documentation references "Cloud Builder," it means VCF Installer. The sooner your team internalizes this mapping, the smoother the transition will be.

---

## VCF Operations Console

### What It Replaced

In VCF 5.x, Day 2 operations were fragmented across SDDC Manager (lifecycle, certificates, passwords, network pools), Aria Operations (monitoring, capacity, cost), Aria Operations for Logs (log analytics), Aria Operations for Networks (network visibility), and vCenter (host management, cluster operations). Administrators experienced the infamous "swivel-chair effect," toggling between consoles during outages and change windows.

### What It Is

VCF Operations in VCF 9 is the *single, unified operations console* for the private cloud. It is not simply a rebranded Aria Operations. It is an expanded platform that absorbs functions previously scattered across SDDC Manager and multiple Aria products, presenting them through a single UI with shared navigation, shared identity, and shared context.

### Capabilities in Depth

**Fleet Management.** VCF Operations provides a fleet-level view of all instances, domains, and clusters. From this view, administrators can manage licenses centrally, enforce configuration baselines, rotate certificates across every component in the fleet without service disruption, and manage account passwords across all VCF components with a consolidated status dashboard. Scheduled drift detection for vCenter and cluster objects proactively identifies configuration deviations from the desired state, flagging them before they become incidents.

**Lifecycle Management.** The upgrade planner and patch planner now live in VCF Operations. Administrators select target versions for individual VCF core components---ESXi, vCenter, NSX, SDDC Manager---and the planner calculates dependencies, validates compatibility, and orchestrates the upgrade sequence. This is a significant shift from VCF 5.x, where lifecycle operations were initiated in SDDC Manager.

**Observability Workbench.** VCF Operations integrates metrics, logs, and network telemetry into a single investigative workflow. During an incident, an administrator can begin with a performance anomaly detected by the metrics engine, pivot to correlated log entries surfaced by VCF Operations for Logs, and then inspect the affected network path through VCF Operations for Networks---all without leaving the console. Health and diagnostics findings surface VMSA/CVE exposure, known-issue KB matches, and trending anomalies in real time.

**Security Operations.** SecOps dashboards provide visibility into authentication events, user activity, security advisories, and compliance posture. The platform embeds sovereign-by-design capabilities including data residency tags, geo-fencing, and automated certificate rotation, allowing organizations to satisfy regulatory requirements as part of their operational workflow rather than as an afterthought.

**Network Operations.** VCF Operations is now the observability portal for NSX. Transport nodes, edge clusters, logical switches, and distributed firewall rule hit counts are all visible and searchable from within VCF Operations, providing infrastructure teams with a network-aware operations experience.

### Why It Matters

The consolidation of operations into a single console is the most tangible manifestation of VCF 9's philosophy shift. It reduces tool sprawl, eliminates context-switching during critical incidents, and creates a single audit trail for all administrative actions. For organizations subject to regulatory oversight, the ability to demonstrate that all operations---lifecycle, security, configuration---flow through one governed console is a significant compliance advantage.

---

## VCF Automation (VCFA)

### What It Is

VCF Automation is the consumption layer of VCF 9. It replaces both VMware Aria Automation and, for service provider use cases, VMware Cloud Director (VCD). It enables IT teams and cloud service providers to deliver a self-service private cloud for AI workloads, Kubernetes clusters, and traditional VM-based applications.

### The Provider and Organization Model

VCFA introduces a multi-tenancy model built around *provider* and *organization* (tenant) constructs:

- The **Provider Organization** is operated by the infrastructure team. It owns the physical resources, defines cloud zones, creates catalog items, and establishes governance policies. The provider maps VCF compute, storage, and network resources into consumable abstractions.
- **Tenant Organizations** represent internal business units or, in service-provider scenarios, external customers. Each organization can connect to its own external identity provider via OIDC, maintaining complete identity isolation. Organizations consume resources through a self-service portal, subject to quotas, placement policies, and budget constraints defined by the provider.

This model is familiar to administrators who have worked with VCD's provider/tenant architecture, but it is now natively integrated into VCF rather than running as a separate product with its own lifecycle.

### Infrastructure as Code and the Terraform Provider

The Terraform Provider for VCFA (TP4VCFA) enables full automation of both provider-side and tenant-side configuration. Provider modules handle cloud zone definitions, catalog publishing, and quota enforcement. Tenant modules handle VM and Kubernetes deployment, network configuration, and day-two operations. This means that the entire private cloud---from infrastructure provisioning to workload deployment---can be expressed as version-controlled code.

### Argo CD and GitOps Integration

VCF 9 ships with a Broadcom-built Argo CD Operator, available as part of the vSphere Supervisor Release 9.0.0.0100. The operator enables GitOps-driven management of Kubernetes clusters, VM workloads, and vSphere Pods. The workflow is familiar to any GitOps practitioner: developers commit application code or infrastructure definitions to a Git repository; Argo CD continuously monitors those repositories; upon detecting a change, Argo CD applies the desired state to the target clusters automatically. This closes the loop between version control and infrastructure reality, enabling automated, auditable, version-controlled delivery pipelines.

### Event Broker and Extensibility

VCF Automation retains and extends the event broker model from Aria Automation. Event topics---such as Compute Allocation, Compute Post Provision, and Deployment Requested---can trigger extensibility subscriptions that execute Orchestrator workflows or Action-Based Extensibility (ABX) actions. Subscriptions can be scoped to individual projects or shared across all projects in an organization. Blockable subscriptions enable approval gates, ITSM ticket creation, IP address management integration, and custom validation logic, making the platform extensible without forking the core code.

### Budget Constraints and Cost Governance

VCFA introduces project-level budget constraints. A project constraint is a key-value governance definition that controls which resources a deployment request may consume or must avoid. Combined with VCF Operations' embedded cost control (discussed later in this chapter), this creates a closed-loop governance model: the provider defines budgets, VCFA enforces them at deployment time, and VCF Operations reports on actual consumption against those budgets.

### Why It Matters

VCFA transforms VCF from an infrastructure platform into a cloud operating model. Application teams no longer file tickets and wait for infrastructure administrators to provision VMs. They browse a catalog, select a blueprint, and deploy within guardrails that the infrastructure team has defined. For service providers, the provider/org model provides native multi-tenancy without the operational overhead of a separate VCD stack. For enterprises embracing GitOps, the Argo CD integration means that VCF infrastructure can participate in the same CI/CD pipelines that manage application code.

---

## vSphere 9: The Hypervisor Evolves

### Virtual Hardware Version 22

**What It Is.** vSphere 9 introduces Virtual Hardware Version 22 (vmx-22), the latest revision of the virtual machine hardware abstraction layer. The headline specifications are dramatic:

- **960 vCPUs** per virtual machine (up from 768 in v21)
- **24 TB of memory** per virtual machine (up from 16 TB in v21)
- Native support for AMD Zen 5 (Turin) and Intel Granite Rapids processors
- NVMe 1.3c virtual controller support
- Enhanced PCI passthrough capabilities

**How It Works.** When you create or upgrade a VM to hardware version 22, ESXi 9 exposes the expanded resource ceilings to the guest operating system. The new hardware version also enables platform features that require guest-visible changes, such as updated virtual NVDIMM support and enhanced device emulation.

**Why It Matters.** Monster VMs are no longer an edge case. SAP HANA, Oracle in-memory databases, large-scale AI training frameworks, and high-performance computing workloads increasingly demand resource allocations that would have required bare metal just a few years ago. Virtual Hardware v22 ensures that vSphere remains a viable platform for these workloads without compromising on the operational benefits of virtualization---live migration, snapshotting, resource pooling, and centralized management.

### NVMe Memory Tiering: Production-Ready

**What It Is.** NVMe Memory Tiering allows ESXi to use local NVMe flash devices as a secondary memory tier beneath DRAM. The hypervisor's memory manager transparently migrates cold pages from DRAM to NVMe and promotes hot pages back, expanding effective memory capacity at a fraction of the cost of additional DRAM DIMMs.

**How It Works.** The feature leverages ESXi's existing memory resource management framework. When enabled, ESXi partitions local NVMe devices as tiered memory. The hypervisor continuously monitors page access frequency and uses heuristic-based promotion and demotion algorithms to keep frequently accessed pages in DRAM while relegating infrequently accessed pages to the NVMe tier. Virtual machine profiles define performance expectations, allowing administrators to tune the aggressiveness of tiering based on workload characteristics.

NVMe Memory Tiering was introduced as a technology preview in vSphere 8.0 Update 3. In vSphere 9, it is fully production-ready, with support from major server vendors including Lenovo and Dell, and with comprehensive monitoring integration in VCF Operations.

**Why It Matters.** Memory is often the most expensive component in a server, and it is frequently the constraint that limits VM density. NVMe Memory Tiering breaks that constraint. For workloads with large memory footprints but moderate access locality---virtual desktop infrastructure, file servers, development environments, and certain database workloads---NVMe tiering can increase effective memory capacity by 2x to 4x without proportional cost increases. This translates directly to fewer hosts, lower licensing costs, and higher ROI.

### ESXi Image-Based Management and Stateless Hosts

**What It Is.** ESXi 9 continues the evolution toward a fully image-based, declarative host management model. vSphere Lifecycle Manager (vLCM) images define the desired state of an ESXi host---including the ESXi version, firmware levels, and driver versions---as a single, versioned artifact. In VCF 9, all ESXi clusters must use vLCM images; legacy baseline-based management is no longer supported.

**How It Works.** Administrators define a target image in vCenter. vLCM compares the target image against the current state of each host in the cluster, calculates the required changes, and orchestrates a rolling remediation. Hosts can be configured for stateless caching, where the image is stored on a local disk but the canonical state is always the vLCM image, or for stateful installs, where the image is applied persistently. The elimination of baseline-based management simplifies the upgrade matrix and ensures that every host in a cluster runs an identical software stack.

**Why It Matters.** Configuration drift between hosts has been a persistent source of operational incidents in vSphere environments. Image-based management eliminates this class of problem by enforcing a single, auditable desired state. For large-scale deployments with hundreds or thousands of hosts, the operational savings are substantial: one image definition replaces hundreds of individual patching workflows.

### Deprecated and Removed Virtual Devices

vSphere 9 continues the platform's modernization of virtual device emulation. The LSI SAS virtual SCSI controller, long the default for new VMs, is being phased out in favor of the VMware Paravirtual (PVSCSI) controller, which delivers significantly better I/O performance. vSphere 9 can automatically replace LSI SAS controllers with PVSCSI during hardware upgrades, simplifying the transition. Legacy device emulations---including older virtual NIC types and deprecated storage controllers---have been removed or deprecated. Administrators planning upgrades from vSphere 8 should audit their VM inventories for deprecated devices before initiating the migration. In particular, the CIM/SFCB and OpenSLP stacks have been fully removed from ESXi 9, and Intel Optane PMEM is no longer supported.

---

## NSX Enhanced Data Path: 3x Network Performance

### What It Is

NSX Enhanced Data Path (EDP) is a high-performance network data plane that replaces the standard NSX virtual switch for packet processing. In VCF 9, EDP Standard mode is the *default* switch mode when configuring NSX, meaning every new VCF 9 deployment benefits from enhanced networking performance out of the box.

### How It Works

The performance improvements stem from three architectural changes:

1. **Kernel Bypass and DPDK.** EDP uses Data Plane Development Kit (DPDK) polling-mode drivers instead of traditional interrupt-driven packet processing. Rather than waiting for the kernel to signal that a packet has arrived, EDP continuously polls the NIC for new packets, eliminating interrupt overhead and reducing latency to microsecond levels.

2. **Direct Data Forwarding.** Packets bypass portions of the traditional virtual switch logic, following a streamlined forwarding path that reduces per-packet CPU cycles.

3. **Multi-Core Parallelization.** EDP distributes packet processing across multiple CPU cores, avoiding the single-core bottlenecks that can constrain throughput in interrupt-driven models.

The result, validated in real-world deployments, is up to **3x more switching performance** compared to the standard NSX data path.

### DPU Offload

For environments that require even greater performance or that need to reclaim CPU cycles for workload processing, EDP supports offloading network operations to Data Processing Units (DPUs, also known as SmartNICs). When a DPU is present, NSX moves overlay encapsulation/decapsulation, firewall rule evaluation, and other network functions from the host CPU to the DPU's embedded processor. This delivers higher throughput at lower latency with minimal CPU overhead on the hypervisor, effectively making networking "free" from the perspective of workload compute.

### When It Matters

EDP is mandatory in VCF 9---you must have an EDP-compatible network adapter. This requirement reflects Broadcom's confidence that the performance benefits justify the hardware dependency. Workloads that benefit most include high-frequency trading platforms, real-time analytics pipelines, AI inference services, large-scale east-west traffic between microservices, and any application where network latency directly impacts user experience or business outcomes. Even for general-purpose workloads, the reduced CPU overhead translates to higher VM density and lower infrastructure costs.

---

## VPC Networking in VCF

### What It Is

VMware Virtual Private Cloud (VPC) brings the public-cloud networking paradigm to VCF. A VPC is a secure, isolated multi-tenant network environment hosted within a VCF private cloud. It enables different teams, projects, or tenants to operate in logically separated network spaces with self-service network provisioning, all while sharing the same physical infrastructure.

### How It Works

The VPC model in VCF 9 is built on NSX Projects and VPC Gateways:

- An **NSX Project** defines a tenant. It groups networking and security objects, enables delegation of administrative controls, and enforces resource quotas. Each project operates in its own administrative namespace.
- Within a project, one or more **VPCs** are created. Each VPC owns its own VPC Gateway, which routes packets between the VPC's subnets. Users can create on-demand subnets defined as *private* (isolated) or *public* (routable to external networks).
- A **Transit Gateway** within each project connects all VPC Gateways together, enabling controlled inter-VPC communication.

Available network services within a VPC include Network Address Translation (NAT), Gateway Firewall, and the vDefend Distributed Firewall. VCF 9 makes deployments VPC-ready out of the box, and VPC configuration can be initiated directly from vCenter by selecting VPC configuration options in the network tab.

### Edgeless Connectivity

One of the most significant innovations in VCF 9's VPC implementation is **distributed network connectivity without NSX Edge VMs**. The VPC fabric can reach the physical network directly from the host, bypassing the need for Edge node deployment and advanced routing configuration. This simplifies the external connectivity model dramatically, reducing operational complexity and eliminating a common performance bottleneck.

### Why It Matters

VPC networking is the bridge between private cloud operations and the public cloud experience that application teams increasingly expect. Instead of filing a ticket requesting a new VLAN and waiting for a network administrator to configure it, a development team creates a VPC, defines its subnets, attaches firewall policies, and begins deploying workloads---all within minutes, all governed by policies the infrastructure team has defined. For organizations operating hybrid or multi-cloud architectures, VPC provides a familiar abstraction that aligns private cloud networking with AWS VPC or Azure VNet semantics, reducing cognitive overhead for teams that work across environments.

---

## vSAN: Global Cluster-Wide Deduplication

### What It Is

vSAN in VCF 9 introduces software-based, cluster-wide global deduplication for the Express Storage Architecture (ESA). Unlike previous vSAN deduplication, which operated per disk group, global deduplication identifies and removes redundant 4 KB data blocks across *the entire cluster*, dramatically improving data reduction ratios.

### How It Works

The deduplication engine operates as a **post-process** mechanism. After data is written to vSAN, the deduplication engine runs in the background during periods of low CPU utilization, scanning for duplicate 4 KB blocks across all hosts in the cluster. When duplicates are identified, redundant copies are eliminated and replaced with references to a single canonical block. The deduplicated data is then sharded using a novel distribution algorithm that spreads deduplicated blocks across hosts, delivering both scalability and resilience.

This post-process architecture is a deliberate design choice. Inline deduplication---which identifies duplicates at write time---imposes CPU overhead on the write path and can introduce latency. By deferring deduplication to background processing, vSAN preserves write performance while still achieving aggressive data reduction.

### What Changed from Prior Versions

In vSAN 7.x and 8.x with OSA (Original Storage Architecture), deduplication operated at the disk-group level. The deduplication domain was limited to the capacity devices behind a single disk group on a single host. This constrained the deduplication ratio because identical blocks on different hosts or in different disk groups were invisible to each other.

vSAN ESA in VCF 9 eliminates this limitation. The deduplication domain is now the entire cluster. As the cluster grows---more hosts, more capacity---the deduplication domain grows with it, and deduplication ratios typically improve. In benchmarks, VCF 9 global deduplication achieves up to **8x data reduction**, compared to the 2x typical of per-disk-group deduplication in older architectures.

### RPQ Note

Global deduplication is available for vSAN ESA clusters and is included in the VCF license. It does not require a separate license or entitlement. However, organizations running vSAN OSA will not benefit from this feature; the cluster-wide deduplication engine is an ESA-exclusive capability. If your hardware does not meet ESA requirements, consult your VMware account team about a hardware refresh program or Request for Product Qualification (RPQ) to explore options.

### Why It Matters

Storage capacity is a direct cost driver. An 8x reduction in storage footprint means that a cluster that previously required 100 TB of raw flash can achieve the same effective capacity with 12.5 TB. For environments with significant data redundancy---VDI, development/test, template-heavy deployments---the savings are transformational. And because the deduplication runs post-process with minimal CPU impact, it delivers these savings without compromising workload performance.

---

## Native vSAN-to-vSAN Replication, Immutable Snapshots, and VMware Live Recovery

### What It Is

VCF 9 introduces native vSAN-to-vSAN replication with deep, immutable snapshots, integrated with VMware Live Recovery for full disaster recovery and cyber recovery orchestration. This eliminates the need for third-party replication appliances or array-based replication for many use cases.

### How It Works

**Native Replication.** vSAN replicates data at the VM level from a source vSAN cluster to a target vSAN cluster. Unlike array-based replication, which operates at the LUN or volume level, VM-level replication provides granular control over which VMs are protected, their RPO targets, and their recovery priorities. Enhanced vSphere Replication provides automated load balancing and scaling to achieve higher performance, supporting an RPO as low as **1 minute** when enabled with VMware Live Recovery.

**Immutable Snapshots.** Replicated data can be stored as immutable snapshots in a vSAN-based data vault. Immutable snapshots cannot be modified, encrypted by ransomware, or deleted before their retention period expires. With VCF 9 and VMware Live Recovery 9.0.3, organizations can build an on-premises isolated recovery environment and store up to 200 immutable snapshots per protected VM, with configurable RPO between one minute and 24 hours.

**VMware Live Recovery.** VMware Live Recovery (formerly VMware Site Recovery Manager) provides full DR orchestration: automated failover, failback, recovery plan testing, and compliance reporting. The integration with vSAN-native replication simplifies the architecture---no separate replication appliances, no array-level coordination---while delivering enterprise-grade recovery capabilities. A single appliance handles lifecycle management, reducing administrative overhead.

### Why It Matters

Ransomware has made immutable recovery points a board-level priority. vSAN-native replication with immutable snapshots provides a *platform-integrated* answer to this challenge, eliminating the need to bolt on third-party backup and replication products for basic DR and cyber recovery scenarios. The 1-minute RPO ensures that even the most transaction-intensive workloads can be protected with minimal data loss. And because the entire solution---replication, snapshots, orchestration---is integrated into VCF, it participates in the same lifecycle management, monitoring, and governance workflows as the rest of the platform.

---

## Expanded Principal Storage: NFS and Fibre Channel in VCF 9.0.1

### What It Is

Historically, VCF greenfield deployments required vSAN as the principal storage for the management workload domain. VCF 9.0 expanded this by supporting NFSv3 and Fibre Channel VMFS datastores as principal storage for the management domain in greenfield deployments. VCF 9.0.1 extended this further with support for stretched management domains on Fibre Channel-based VMFS principal storage.

### How It Works

During greenfield deployment with the VCF Installer, administrators can now select NFSv3 or Fibre Channel VMFS as the principal storage type for the management domain. The installer validates the storage configuration and proceeds with deployment without requiring any vSAN infrastructure. Additional principal storage types---including iSCSI, NFS v4.1, FCoE, and NVMe over Fabrics (FC, TCP, RDMA)---can be configured using alternative deployment methods, though they are not yet available through the standard greenfield workflow.

### Why It Matters

This is a transformational change for organizations with significant investments in external storage arrays. Many enterprises have purchased NetApp, Pure Storage, Dell PowerStore, or Hitachi Vantara arrays with multi-year support contracts. Previously, deploying VCF meant either maintaining a parallel vSAN infrastructure for the management domain or foregoing VCF entirely. With NFS and FC principal storage support, these organizations can adopt VCF without stranding their storage investments. The "VCF is vSAN-only" narrative is officially over, and VCF 9 is now described by Broadcom as "ready for all storage."

---

## VCF PowerCLI 9.0

### The Rename

The PowerShell automation module has been renamed from **VMware.PowerCLI** to **VCF.PowerCLI**, aligning the module name with the VCF release versioning and branding. The module is published to the PowerShell Gallery as `VCF.PowerCLI`.

### 70% Faster

Core cmdlets have been rewritten as .NET-based compiled classes, eliminating the overhead of interpreted PowerShell script modules. The most visible improvement is module import time: the legacy VMware.PowerCLI module took over 60 seconds to import; VCF.PowerCLI imports in approximately 18 seconds. Across the board, execution performance has improved by up to 70% in common automation scenarios.

### The Initialize/Invoke Pattern

VCF PowerCLI 9.0 introduces auto-generated SDK cmdlets that follow a consistent two-verb pattern:

- **Initialize-** cmdlets prepare data structures (request bodies) for API calls. They construct the objects that the API expects, with parameter validation and type checking.
- **Invoke-** cmdlets execute the actual operations on the server side, sending the prepared request and returning the response.

This pattern separates data construction from execution, making scripts easier to read, debug, and compose. It also enables a "prepare and review" workflow where administrators can inspect the request body before committing the operation.

### New Modules

VCF PowerCLI 9.0 ships with new modules for NSX and SDDC Manager, expanding the automation surface to cover network configuration and lifecycle operations that previously required direct API calls or the NSX Manager UI.

### Why It Matters

PowerCLI is the automation backbone for thousands of VMware environments. The 70% performance improvement alone justifies the upgrade for teams running large-scale automation. The Initialize/Invoke pattern aligns PowerCLI with modern API design practices, making it more approachable for developers who are accustomed to working with REST APIs and SDKs rather than monolithic cmdlets.

---

## Unified SDK, OpenAPI 3.0, VI JSON, and the API Changelog

### Unified VCF SDK

VCF 9 consolidates previously separate component SDKs---for vCenter, NSX, vSAN, SDDC Manager, and others---into a single, unified VCF SDK available for both Python and Java. All packages are published to Maven Central (Java) and PyPI (Python), and the source is available on the VMware GitHub repository. This means developers no longer need to hunt across multiple repositories, manage conflicting dependency versions, or learn different SDK conventions for each VCF component.

### OpenAPI 3.0

Approximately 90% of VCF APIs now conform to the OpenAPI 3.0 specification. vCenter 9.0 adds OpenAPI 3.0 support for all vCenter and vSAN APIs, complementing the existing vCenter REST APIs. OpenAPI specifications define APIs in a standardized, machine-readable format (YAML/JSON), enabling automatic generation of client libraries, documentation, and testing harnesses in any language.

### VI JSON Protocol

The VI JSON protocol converts legacy SOAP-based vSphere APIs into a modern, REST-style experience. Rather than generating WSDL stubs and marshaling XML, developers can interact with the full breadth of vSphere APIs using JSON payloads over HTTP. This dramatically lowers the barrier to entry for integrating with vSphere from modern application frameworks, serverless functions, and CI/CD pipelines.

### API Changelog

For the first time, VCF publishes a formal API changelog with each release. The changelog provides a consolidated summary of all modifications across every VCF component: newly introduced APIs, updated APIs, deprecated APIs, removed APIs, and structural modifications. This is a seemingly small change with outsized impact. Previously, determining what had changed in the API layer between releases required reading multiple sets of release notes, comparing OpenAPI specifications manually, or discovering changes through trial and error. The changelog makes API evolution transparent and predictable.

### Why It Matters

The developer experience around VMware APIs has historically been a source of friction. The proliferation of SOAP, REST, and custom protocols across different products created a steep learning curve. VCF 9's unified SDK, universal OpenAPI 3.0 adoption, VI JSON bridge, and formal changelog collectively represent the most significant improvement in VMware's developer experience in over a decade. For organizations building automation, integrations, or custom management tools, VCF 9 is dramatically easier to program against than any prior release.

---

## Lifecycle Overhaul: Component-Level Patching and BOM-Driven Upgrades

### What It Is

VCF 9 introduces a flexible, Bill of Materials (BOM)-driven lifecycle management model. Rather than upgrading all components in lockstep to a single VCF version, administrators can now select specific target versions for individual VCF core components---ESXi, vCenter, NSX Manager, SDDC Manager---within a domain.

### How It Works

The **upgrade planner** in VCF Operations presents a matrix of available target versions for each component. Administrators select the desired target version for each component independently, subject to compatibility constraints that the planner enforces automatically. The planner calculates the correct upgrade sequence, validates prerequisites, and orchestrates the rollout.

The **patch planner** enables updating individual components with maintenance patches in any order. Patch order only matters for major and minor version upgrades; maintenance patches can be applied independently, giving administrators the flexibility to address critical vulnerabilities immediately without waiting for a full upgrade cycle.

For fleet-level components, the upgrade sequence follows a defined order: fleet-level management components (VCF Operations, Fleet Management) are updated first, followed by instance-level core components (SDDC Manager, NSX, vCenter, ESXi) within each instance.

### Why It Matters

The rigid, all-or-nothing upgrade model of prior VCF versions was one of the platform's most common pain points. Upgrading VCF 4.x or 5.x often meant coordinating a "big bang" update that touched every component in a single maintenance window. If one component failed compatibility validation, the entire upgrade stalled. BOM-driven lifecycle management eliminates this problem. You can patch ESXi to address a CVE on Monday, upgrade NSX to enable a new feature on Wednesday, and upgrade vCenter when your change advisory board approves it next month. Each component moves independently within the guardrails of the compatibility matrix.

---

## The 6+1 Support Model and New Release Cadence

### What Changed

VCF 9 introduces a new support model and release cadence:

**Support Model: 6+1.** The previous 5+2 model provided five years of general support with the option to purchase two years of extended support. The new 6+1 model provides **six years** of support for each major release, with the option to purchase **one additional year** of extended support. While the total potential support period decreases by one year (seven vs. eight), the guaranteed general support period increases by one year (six vs. five), which is the metric most organizations care about.

**Release Cadence: Three-Year Majors, Nine-Month Minors.** Major releases will now ship every three years (up from two), and minor releases will ship every nine months (up from six). Each major release will have four minor releases (e.g., VCF 9.0, 9.1, 9.2, 9.3). Initial minor releases receive 27 months of support each; the final minor release in a major version receives 45 months of support, giving customers a generous window to plan their upgrade to the next major.

### Why It Matters

The shift to three-year major cycles and nine-month minor cycles directly addresses the most common feedback from VCF customers: "We cannot upgrade this frequently." The previous two-year major cycle, combined with six-month minor releases, meant that organizations often fell behind, running unsupported versions because they could not schedule upgrade windows frequently enough. The new cadence gives more time between releases, longer support windows, and more flexibility to choose an upgrade path that aligns with organizational change-management processes.

For planning purposes, the VCF 9 timeline looks approximately like this: VCF 9.0 (June 2025), VCF 9.1 (approximately Q1 2026), VCF 9.2 (approximately Q4 2026), VCF 9.3 (approximately Q3 2027), with VCF 10.0 expected around 2028.

---

## Embedded Cost Control and Governance

### What It Is

VCF 9 embeds cost management and governance directly into the platform, eliminating the need for third-party cost-management tools for most private cloud use cases.

### How It Works

**Chargeback and Showback.** VCF Operations provides dashboards that translate resource consumption---CPU hours, memory GB-hours, storage GB-days, network throughput---into invoice-ready monetary amounts for each tenant, project, or business unit. Showback reports provide transparency without billing enforcement; chargeback integrates with financial systems for actual cost recovery.

**Cost Predictability.** Capacity forecasting models project future resource consumption and associated costs based on historical trends, planned deployments, and growth assumptions. These projections enable finance teams to budget for infrastructure with confidence rather than relying on rough estimates.

**Budget Alerts and Compliance.** Administrators define budget thresholds at the project, organization, or fleet level. When consumption approaches or exceeds the budget, alerts are triggered. Usage forecasts predict when a budget will be exhausted, enabling proactive intervention before overspend occurs.

**Business Intents.** Business Intents are governance definitions that control where workloads are permitted to reside based on compliance requirements, licensing constraints, and organizational policy. For example, a business intent might require that all GDPR-regulated workloads run on hosts in the Frankfurt instance, or that development workloads avoid hosts licensed for production use.

### Why It Matters

In most enterprises, infrastructure costs are opaque. Business units consume resources without visibility into the costs they generate, and finance teams lack the data to hold them accountable. VCF 9's embedded cost control closes this gap. By providing cost visibility at the project and tenant level, with budget enforcement and predictive analytics, the platform enables a FinOps operating model without requiring a separate FinOps tool. For organizations that have struggled to justify private cloud investments against public cloud alternatives, the ability to demonstrate precise cost-per-workload metrics is a powerful argument for keeping workloads on-premises.

---

## From VCF 5.2 to VCF 9.0: A Narrative Comparison

To fully appreciate the magnitude of VCF 9, it helps to walk through the same set of administrative tasks as they would be performed in VCF 5.2 versus VCF 9.0. This is not a feature checklist comparison---it is a story about how the daily experience of operating a private cloud has changed.

### Day 0: Deploying a New VCF Instance

**VCF 5.2.** You download the Cloud Builder OVA and deploy it to a temporary vCenter or directly to an ESXi host. You open the Deployment Parameters Workbook---a Microsoft Excel spreadsheet with dozens of tabs and hundreds of cells---and painstakingly fill in hostnames, IP addresses, credentials, network pool definitions, and DNS records. You validate the workbook, upload it to Cloud Builder, and initiate the bringup process. If a validation error occurs, you return to the spreadsheet, correct the value, re-upload, and re-validate. The process is functional but fragile; a single typo in the workbook can derail the deployment.

**VCF 9.0.** You deploy the VCF Installer appliance. Instead of an Excel workbook, you are presented with a UI-driven workflow that guides you through each configuration step with real-time validation and contextual help. You select your principal storage type---vSAN, NFS, or Fibre Channel---directly in the wizard. The installer validates connectivity, DNS resolution, and credential access as you proceed, catching errors immediately rather than deferring them to bringup. The experience is closer to a cloud provider's onboarding wizard than to an enterprise infrastructure deployment tool.

### Day 1: Configuring Networking for a New Tenant

**VCF 5.2.** You log into NSX Manager and manually create a Tier-1 gateway, define segments, configure DHCP, attach firewall rules, and coordinate with the network team for physical connectivity. If you need to repeat this for another tenant, you repeat the entire manual process or write custom automation.

**VCF 9.0.** You define an NSX Project for the tenant and create one or more VPCs within it. Each VPC gets its own gateway automatically. The tenant's administrator can create on-demand subnets---public or private---through the VCF Automation self-service portal or directly from vCenter. Firewall policies are inherited from the project template. External connectivity is available without deploying Edge VMs, using the new distributed connectivity model. What took hours and cross-team coordination in VCF 5.2 takes minutes and is fully self-service in VCF 9.

### Day 2: Monitoring and Troubleshooting

**VCF 5.2.** An alert fires in Aria Operations indicating high CPU ready time on a cluster. You investigate in Aria Operations, but the root cause appears to be network-related. You switch to Aria Operations for Networks to inspect the overlay. You find a log entry that might be relevant, so you switch to Aria Operations for Logs. Each tool has its own navigation, its own session, and its own query language. You lose context with every switch.

**VCF 9.0.** The same alert fires in VCF Operations. You click through to the affected cluster, see correlated log entries in the same view, pivot to the network topology for the affected VMs, and identify a misconfigured distributed firewall rule that is causing packet drops. You remediate the rule, verify the fix, and close the incident---all without leaving VCF Operations. The Observability Workbench provides a unified investigative surface that treats metrics, logs, and network telemetry as facets of the same data set.

### Lifecycle: Patching a Critical CVE

**VCF 5.2.** A critical ESXi CVE is published. You log into SDDC Manager, check for available bundles, and discover that the patch is bundled with a full VCF update that also includes new versions of vCenter and NSX. You cannot patch ESXi independently; you must upgrade the entire stack or apply the patch manually outside of VCF lifecycle management, which takes the environment out of the supported configuration matrix. You schedule a multi-hour maintenance window, coordinate across teams, and upgrade everything at once.

**VCF 9.0.** You open the patch planner in VCF Operations. You select the ESXi patch for the affected domain. The planner validates compatibility and confirms that the patch can be applied independently without upgrading vCenter or NSX. You schedule the remediation for the next available window, and the planner orchestrates a rolling host upgrade that completes with zero workload downtime. The vCenter and NSX upgrades can follow on their own schedule, when the change advisory board approves them.

### Automation: Deploying a Workload

**VCF 5.2.** An application team submits a request for three VMs with specific CPU, memory, and storage configurations. The request goes through an approval workflow in your ITSM tool. An infrastructure administrator logs into vCenter, creates the VMs manually or runs a PowerCLI script, assigns them to the correct network, and notifies the application team. Elapsed time: two to five business days.

**VCF 9.0.** The application team browses the VCF Automation catalog, selects a VM blueprint that matches their requirements, and submits the deployment. VCFA validates the request against the project's budget constraints, placement policies, and quota limits. The event broker triggers an approval workflow in ServiceNow via an extensibility subscription. Upon approval, VCFA provisions the VMs automatically, attaches them to the correct VPC subnet, and notifies the application team. Elapsed time: minutes to hours, depending on approval latency. The entire process is auditable, repeatable, and governed by policy.

### Developer Experience: Building Integrations

**VCF 5.2.** Your DevOps team needs to integrate VCF with their CI/CD pipeline. They discover that vCenter uses SOAP APIs, NSX uses a REST API with a custom authentication model, SDDC Manager uses its own REST API, and Aria products each have their own APIs with separate documentation. They spend weeks writing integration code that handles four different authentication mechanisms, three different payload formats, and zero formal changelog to track what changes between updates.

**VCF 9.0.** Your DevOps team installs the VCF SDK from PyPI. They authenticate once using the unified identity model. They call vCenter, NSX, and SDDC Manager APIs through a single, consistent SDK with OpenAPI 3.0 specifications and VI JSON support. When VCF 9.0.1 is released, they consult the API changelog to understand exactly what changed. They update their integration in an afternoon.

### Cost Governance: Tracking Infrastructure Spend

**VCF 5.2.** Finance asks for a breakdown of infrastructure costs by business unit. You export utilization data from Aria Operations, manually correlate it with hardware purchase costs and licensing fees in a spreadsheet, estimate cost allocations based on resource consumption ratios, and deliver a report three weeks later. The report is approximate, difficult to reproduce, and outdated by the time it is delivered.

**VCF 9.0.** You open the cost management dashboard in VCF Operations. Chargeback reports are generated automatically, broken down by organization, project, and deployment. Budget compliance is tracked in real time, with alerts when consumption approaches thresholds. You export the report as a PDF and deliver it to finance the same day it is requested. The data is precise, reproducible, and current.

---

## Looking Ahead

VCF 9 is not just a product release; it is a statement of strategic intent. By centering the platform on operations and consumption, Broadcom is signaling that VMware Cloud Foundation is no longer merely infrastructure software. It is a private cloud operating system---one that competes with public cloud not on price alone but on the completeness of its operational model, the depth of its governance capabilities, and the quality of its developer experience.

The features discussed in this chapter---fleet management, VPC networking, global deduplication, immutable snapshots, NVMe memory tiering, Enhanced Data Path networking, BOM-driven lifecycle management, the unified SDK, embedded cost control---are not isolated improvements. They are interconnected pieces of a coherent platform vision. Each feature makes the others more valuable. VPC networking becomes more powerful when governed by VCFA budget constraints. Global deduplication becomes more impactful when surfaced through VCF Operations' cost dashboards. The unified SDK becomes more useful when the API changelog makes API evolution predictable.

For practitioners preparing to deploy or migrate to VCF 9, the message is clear: this is the release that justifies the platform's ambition. The subsequent chapters of this book will take you through every deployment pathway, configuration workflow, and operational procedure in detail. But the conceptual foundation laid in this chapter---the philosophy shift, the hierarchy, the terminology, the feature depth---is essential context for everything that follows.

Welcome to VCF 9. The private cloud has grown up.
