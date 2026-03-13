# Chapter 3: Architecture

## The Full-Stack SDDC Vision

VMware Cloud Foundation 9 represents the most ambitious architectural reset in VMware's private cloud history. Where previous releases evolved incrementally---adding a component here, bolting on an integration there---VCF 9 takes a fundamentally different approach. Every building block has been aligned to a single version number. vSphere 9, ESX 9, NSX 9, vSAN 9, VCF Operations 9, VCF Automation 9---the entire stack ships as one cohesive platform. This is not cosmetic rebranding. It is an architectural statement: the Software-Defined Data Center is no longer an assembly of loosely coupled products. It is a single product with a single lifecycle.

The full-stack vision of VCF 9 means that compute, storage, networking, security, and automation are designed, tested, and released together. When you deploy a VCF Instance, you are deploying a validated, interoperable stack where every API contract between components has been verified in combination. This eliminates the interoperability matrices that plagued earlier generations, where administrators had to cross-reference NSX compatibility with a specific vCenter build, or determine whether a vSAN release supported a particular ESXi patch level. In VCF 9, the answer is always the same: if it shipped together, it works together.

This architectural philosophy extends to licensing. Instead of the eleven separate license keys that customers managed in previous generations, VCF 9 consolidates to just two: "VMware Cloud Foundation (cores)" and "VMware vSAN (TiBs)." The licensing simplification is not a minor operational convenience---it reflects the deeper truth that VCF is a platform, not a bundle.

Security is baked into this full-stack approach as well. All VCF 9 components---vCenter, ESX, and NSX---run in FIPS 140-2 and FIPS 140-3 enabled mode by default, and this mode cannot be deactivated. Security is not an optional overlay; it is a foundational property of the platform.

Understanding the architecture of VCF 9 is prerequisite to every design decision that follows. Whether you are sizing a management domain, choosing a storage architecture, or planning a multi-region fleet, the architectural constructs described in this chapter form the grammar of every conversation you will have with your engineering teams, your leadership, and your VMware technical account manager.

---

## The Three-Tier Hierarchy: Private Cloud, Fleet, and Instance

VCF 9 introduces a formal organizational hierarchy that fundamentally changes how enterprises think about their private cloud estates. This hierarchy---Private Cloud at the top, Fleet in the middle, and Instance at the bottom---provides the scaffolding for governance, lifecycle management, and operational consistency at any scale.

### Private Cloud

The Private Cloud is the highest-level abstraction. It represents your entire VMware-based infrastructure estate, regardless of how many data centers, geographies, or business units it spans. Think of it as the organizational boundary within which all policy, strategy, and architectural standards originate. A global financial services firm with data centers in New York, London, Singapore, and Sydney has one Private Cloud. A regional healthcare provider with a single data center and a disaster recovery site also has one Private Cloud.

The Private Cloud construct is deliberately abstract. It does not map to a specific appliance, API endpoint, or management console. Rather, it provides the conceptual container within which Fleets and Instances are organized. When your CTO asks, "How large is our private cloud?"---the answer encompasses every Fleet beneath it.

### Fleet

A Fleet is where operational reality begins. A VCF Fleet is a logical grouping of one or more VCF Instances that share a single set of fleet-level management components: one VCF Operations deployment and one VCF Automation deployment. The Fleet is the unit of centralized lifecycle management, identity federation, certificate authority, licensing, and health monitoring.

This is an architectural decision with profound operational implications. Consider a multinational manufacturer with three regional data centers. If all three VCF Instances are placed in a single Fleet, they share one VCF Operations console for lifecycle management, one VCF Automation instance for self-service provisioning, and one unified identity provider configuration. Patching, upgrades, compliance reporting, and capacity planning all flow through a single pane of glass. The trade-off is that the Fleet management components must be reachable from every Instance in the Fleet, and any disruption to the Fleet management layer affects visibility and lifecycle operations across all Instances.

Alternatively, the manufacturer could create two Fleets---one for North America and Europe, another for Asia-Pacific---each with its own VCF Operations and VCF Automation. This provides operational isolation: the Asia-Pacific team can execute lifecycle operations independently, with separate change windows and separate identity configurations. The cost is duplication of management infrastructure and the loss of a single unified view.

The headline capability in VCF 9.0 that makes large-scale Fleet architectures practical is the increase of the maximum supported Round Trip Time (RTT) for fleet-to-instance communication to 300 milliseconds. In previous releases, the latency constraints meant that Fleet management components needed to be relatively close---within the same metropolitan area or connected by low-latency dedicated fiber. With 300ms RTT support, a Fleet can span continents. A VCF Operations instance in Frankfurt can manage VCF Instances in Singapore, provided the network path stays within the 300ms envelope. This is a transformative change for globally distributed enterprises.

However, within a single VCF Instance, the latency requirements remain tighter. The VCF Operations Collector requires a maximum of 50ms RTT to core management components such as vCenter, NSX Manager, and SDDC Manager. This constraint shapes where you place components and how you segment your Instances.

### Instance

A VCF Instance is the operational unit where workloads actually run. Each Instance contains its own management domain, one or more workload domains, and an SDDC Manager appliance that orchestrates domain-level operations. An Instance maps to a single physical or logical site---a data center, a colocation facility, or a campus.

The hierarchy you should internalize is: **Private Cloud > Fleet > Instance > Domain > Cluster**. Every design conversation should begin by establishing where in this hierarchy a decision is being made.

### Real-World Design Implications

Consider a large enterprise with the following requirements: a primary production site, a disaster recovery site 500 kilometers away, and a series of edge locations for retail point-of-sale systems. The architecture might look like this:

- **One Private Cloud** encompassing the entire estate.
- **One Fleet** managing the primary and DR sites (both within 50ms RTT of the Fleet management components).
- **Two Instances**: one at the primary site, one at the DR site, each with its own management domain, workload domains, and SDDC Manager.
- **Edge locations** handled as VCF Edge deployments (single-host or two-host), managed from the primary Fleet.

This topology gives centralized lifecycle management for the core infrastructure while allowing each site to operate independently at the Instance level. If the WAN link between sites fails, each Instance continues to run workloads; only fleet-level operations (lifecycle, automation, centralized monitoring) are interrupted until connectivity is restored.

A different enterprise---say a cloud service provider hosting multiple tenants---might choose a different pattern: one Fleet per major customer, providing complete operational isolation. Each Fleet has its own VCF Operations and VCF Automation, ensuring that one tenant's lifecycle operations never interfere with another's.

---

## VCF Instance Anatomy

Every VCF Instance is built from three fundamental constructs: the Management Domain, one or more Workload Domains, and NSX Edge Clusters. Understanding how these relate to each other is essential to sound architectural design.

### The Management Domain

The Management Domain is the first domain created during VCF deployment. It is the foundation upon which everything else is built. The Management Domain hosts all of the infrastructure management components for the Instance: SDDC Manager, the management vCenter Server, the management NSX Manager cluster, and---if the Fleet management components are co-located---VCF Operations and VCF Automation appliances.

The Management Domain is not optional, and it is not a place for tenant workloads. It is infrastructure for infrastructure. Every VCF Instance has exactly one Management Domain, and it must be treated with the same care as the control plane of a Kubernetes cluster. If the Management Domain becomes unavailable, you lose the ability to manage (though not necessarily to run) the workloads in your Workload Domains.

In VCF 9, the Management Domain requires a minimum of four ESX hosts when using vSAN as principal storage, or a minimum of two ESX hosts when using external storage (Fibre Channel VMFS or NFS). This two-host minimum with external storage is a significant change from earlier releases that required three or four hosts regardless of storage type, and it opens up VCF to smaller environments that could not previously justify the management overhead.

The management vCenter Server manages all ESX hosts within the Management Domain and provides the UI through which many day-two operations are now performed. In VCF 9, a significant number of operational tasks---network pool creation, NSX Edge deployment, host commissioning---have shifted from the SDDC Manager UI to the vCenter UI, reflecting a broader architectural trend toward consolidating the operational surface area.

### Workload Domains

Workload Domains are where your applications run. Each Workload Domain is an isolated unit of compute, storage, and networking. A Workload Domain includes its own vCenter Server instance (or shares one with other domains in specific configurations), one or more vSphere clusters of ESX hosts, and either a dedicated or shared NSX Manager cluster.

The isolation properties of Workload Domains are the key architectural benefit. Each domain has its own pool of storage, its own network configuration, and its own capacity allocation. This enables multi-tenancy at the infrastructure level: different business units, different compliance zones, or different application tiers can each occupy their own Workload Domain with distinct security policies, storage characteristics, and operational boundaries.

A Workload Domain can contain multiple vSphere clusters, allowing you to scale capacity by adding clusters within a domain rather than creating new domains. The decision of when to add a cluster versus when to create a new domain is a common design question. The general guidance is: add a cluster when you need more capacity within the same operational and security boundary; create a new domain when you need isolation---different NSX configurations, different storage policies, different administrative boundaries, or different compliance requirements.

VCF 9 supports up to 24 Workload Domains per Instance (plus the Management Domain, for a total of 25 domains per SDDC Manager). This is a generous limit that few organizations approach, but it is important to understand for capacity planning in service provider or large enterprise scenarios.

### NSX Edge Clusters: Shared vs. Dedicated

NSX Edge Clusters provide centralized networking services---north-south routing, load balancing, NAT, VPN, and gateway firewall---that cannot be distributed to the hypervisor layer. Every Workload Domain that requires external connectivity needs access to an NSX Edge Cluster.

The architectural decision between shared and dedicated NSX Edge Clusters is one of the most consequential networking choices in a VCF design.

**Shared Edge Clusters** allow multiple Workload Domains to share the same NSX Edge nodes, provided those domains share the same NSX Manager cluster. The Edge nodes run on a cluster that may also host workload VMs. This approach minimizes hardware consumption and is appropriate for environments where Workload Domains have modest north-south traffic requirements, where strict performance isolation is not required, and where simplifying management is a priority. A shared Edge Cluster is the right choice for most small-to-medium deployments and for non-production environments.

**Dedicated Edge Clusters** place NSX Edge appliances on a vSphere cluster that contains no other workloads. The Edge nodes get predictable, consistent access to CPU, memory, and network bandwidth. This design pattern is appropriate for environments with high north-south throughput requirements---large-scale web applications, data-intensive services, or any workload where Edge performance variability is unacceptable. The cost is additional hardware: a dedicated Edge cluster requires a minimum of two ESX hosts that serve no other purpose.

A common production pattern is to use a shared Edge Cluster for the Management Domain (whose north-south traffic is typically modest) and dedicated Edge Clusters for Workload Domains with demanding networking requirements. This balances cost against performance isolation.

VCF 9 introduces Virtual Private Cloud (VPC) support within vCenter, allowing administrators to create VPCs with private or publicly advertised subnets directly from the vCenter UI. This new construct sits atop the NSX Edge infrastructure and provides a familiar cloud-like networking model for application teams, while the infrastructure team retains control over the underlying Edge topology.

---

## Component Architecture and Relationships

Understanding how VCF's components relate to each other is essential for troubleshooting, capacity planning, and architectural decision-making. Each component has a specific role, and the connections between them define the operational behavior of the platform.

### VCF Installer

The VCF Installer is the entry point for every new VCF deployment. It is a purpose-built appliance that downloads and manages the binaries required to deploy the VCF platform and then orchestrates the deployment and configuration of all initial components: vCenter Server, NSX Manager, VCF Operations, VCF Automation, and SDDC Manager. The Installer handles the chicken-and-egg problem inherent in deploying a management platform: you need infrastructure to deploy infrastructure, and the Installer provides that bootstrap layer.

The Installer offers two deployment modes---Simple and High Availability---which determine the scale and resilience of the initial deployment. Once the initial deployment is complete, the Installer's role diminishes; ongoing lifecycle management is handled by VCF Operations and SDDC Manager.

### VCF Operations

VCF Operations is the fleet-level control plane for lifecycle management, monitoring, health assessment, licensing, certificate management, and identity federation. It replaces and extends the role that SDDC Manager played in previous VCF releases. Where SDDC Manager operated at the Instance level, VCF Operations operates at the Fleet level, providing a single management surface for all Instances in the Fleet.

VCF Operations is responsible for downloading software bundles, orchestrating upgrades across the fleet, monitoring the health of all VCF components, and providing the primary administrative UI for day-two operations. It includes an integrated Fleet Manager that coordinates lifecycle operations across Instances, ensuring that upgrades are applied consistently and in the correct order.

When deployed in High Availability mode, VCF Operations runs as a three-node cluster, providing resilience against the failure of any single node. The sizing of VCF Operations depends on the number of objects (hosts, VMs, clusters, domains) under management. Broadcom publishes sizing guidelines that scale from Extra Small (up to 700 objects) to Extra Large (up to 100,000 objects).

### VCF Automation

VCF Automation provides self-service cloud provisioning, governance, and multi-cloud management capabilities. It enables IT teams to define cloud templates, create approval workflows, and expose infrastructure-as-a-service catalogs to application teams and tenants. VCF Automation is the evolution of what was previously known as VMware Aria Automation (and before that, vRealize Automation).

VCF Automation 9 introduces two types of organizations: VM-Apps-Organization and All-Apps-Organization. The VM-Apps-Organization is functionally similar to the Aria Automation 8.x model and is the right choice for organizations focused on virtual machine-based workloads. The All-Apps-Organization is an entirely new architecture built on Kubernetes APIs, designed for organizations that need to manage both VM and container workloads through a unified automation framework.

Like VCF Operations, VCF Automation operates at the Fleet level. A single VCF Automation instance serves all VCF Instances within the Fleet, providing a consistent self-service experience regardless of which Instance hosts the underlying workloads.

### vCenter Server 9

vCenter Server 9 is the management plane for vSphere compute resources within each domain. Each domain (Management or Workload) has its own vCenter Server instance, which manages the ESX hosts and clusters in that domain. In VCF 9, vCenter's role has expanded significantly: network pool management, NSX Edge deployment, host lifecycle operations, and ESX image management have all moved into the vCenter UI.

A major architectural change in vCenter 9 is the removal of Enhanced Linked Mode (ELM). In previous releases, ELM allowed multiple vCenter instances to share a Single Sign-On domain and present a unified inventory. VCF 9 eliminates this in favor of the Fleet management model provided by VCF Operations. Identity federation is now handled at the Fleet level, and cross-Instance visibility is provided through VCF Operations rather than through vCenter-to-vCenter linking.

vCenter 9 also introduces the ESX Image Library, which allows administrators to create and manage ESX images at the vCenter level rather than per-cluster, and supports parallel host lifecycle operations across multiple clusters simultaneously.

### ESX 9

ESX 9 (the hypervisor formerly known as ESXi) is the compute foundation. Key architectural changes in ESX 9 include expanded Live Patch coverage---both user-space live patching and NSX transport node live patching can now be applied without an ESX reboot. This dramatically reduces the maintenance window impact for routine patching.

ESX 9 introduces NVMe memory tiering, where fast NVMe devices serve as a second, lower-cost memory tier. Compute-intensive workloads keep their hot working sets in DRAM while cold pages are transparently moved to NVMe. This capability is particularly valuable for memory-hungry workloads like in-memory databases and large-scale analytics.

The NSX configuration for ESX hosts is now integrated with vSphere Config Profiles, allowing unified configuration management for ESX, VDS, and NSX at the cluster level from a single management interface.

### NSX 9

NSX 9 provides the network virtualization layer. All overlay networking, microsegmentation, distributed firewalling, load balancing, and VPN services are delivered through NSX. In VCF 9, NSX integrates exclusively with the native vSphere Distributed Switch (VDS)---support for the standalone N-VDS has been removed entirely. This convergence simplifies the networking stack and eliminates a historical source of configuration complexity.

The NSX Manager cluster (either single-node or three-node) serves as the control plane for NSX within a domain or across multiple domains that share the same NSX Manager. NSX Edge nodes provide the centralized services (routing, NAT, load balancing) that cannot be distributed to the hypervisor.

### vSAN 9

vSAN 9 provides hyperconverged storage. The major architectural direction in vSAN 9 is the Express Storage Architecture (ESA), which is designed from the ground up for NVMe flash storage. vSAN ESA delivers two to five times better performance on the same hardware compared to the Original Storage Architecture (OSA), with up to four times better compression ratios. Global deduplication in ESA is policy-based, giving administrators granular control over where deduplication is applied.

vSAN 9 also introduces a unified storage dashboard that provides real-time visibility into vSAN, SAN, and NAS environments from a single console, reflecting the reality that most enterprises use a mix of storage technologies.

### How They Interconnect

The component relationships form a layered architecture. At the base, ESX 9 provides compute and hosts the vSAN 9 storage layer. NSX 9 provides networking that spans across ESX hosts, with its control plane (NSX Manager) running as VMs on the Management Domain. vCenter 9 manages the ESX hosts and their configurations within each domain. SDDC Manager orchestrates domain-level operations and communicates with vCenter and NSX Manager. VCF Operations sits above all of this, providing fleet-wide lifecycle management, monitoring, and governance. VCF Automation consumes the infrastructure exposed by VCF Operations and vCenter to deliver self-service capabilities to end users.

Data flows upward (telemetry, health, inventory) and commands flow downward (deployments, upgrades, configuration changes). Understanding this layered communication model is essential when troubleshooting: if VCF Operations cannot reach a vCenter, the problem might be in the network path, in the VCF Operations Collector, or in the vCenter appliance itself---and the troubleshooting approach differs for each.

---

## Deployment Modes

VCF 9 supports multiple deployment modes that accommodate environments ranging from enterprise production to lab experimentation.

### Simple (Single-Node) Deployment

The Simple deployment mode deploys a single instance of each management appliance: one vCenter Server, one SDDC Manager, one NSX Manager, one VCF Operations node, and one VCF Automation node. This results in approximately seven appliances for the core management stack.

Simple mode is appropriate for non-production environments, proofs of concept, labs, and development environments. It provides the full functional surface of VCF 9 while minimizing resource consumption. The trade-off is the absence of high availability: if any single management appliance fails, the corresponding management function is unavailable until the appliance is recovered.

### High Availability (Three-Node) Deployment

The HA deployment mode deploys three instances of each clusterable management component: three NSX Manager nodes, three VCF Operations nodes, and three VCF Automation nodes. This results in approximately thirteen management appliances. HA mode is the recommended and supported configuration for production environments. It provides resilience against the failure of any single appliance for each management function.

The resource requirements for HA mode are substantial. Thirteen management appliances, each requiring CPU, memory, and storage, consume a significant portion of the Management Domain's capacity. This is why the Management Domain is typically sized with four or more ESX hosts in production: the management overhead must be absorbed before any capacity is available for other purposes.

### Flexible Combinations

While the VCF Installer UI presents Simple and HA as two discrete options, the underlying platform supports mixed configurations. You can deploy NSX Manager in HA (three nodes) while keeping VCF Operations in Simple mode (one node), or vice versa. This flexibility allows you to right-size the resilience of each component based on your specific requirements and constraints. For example, an organization might deploy NSX Manager in HA mode (because a networking control plane outage affects all workloads) while keeping VCF Automation in Simple mode (because a temporary loss of self-service provisioning is tolerable).

### Two-Node Management Domain with External Storage

For environments with external storage (Fibre Channel VMFS or NFS), the Management Domain can be deployed with as few as two ESX hosts. This dramatically reduces the entry cost for VCF adoption, particularly for organizations that have existing SAN or NAS investments and do not want to deploy vSAN for the management layer. The two-node configuration still supports HA at the vSphere level (vSphere HA can protect management VMs), though the failure of one host leaves no headroom for maintenance.

### Minimum Lab Footprint

For learning and experimentation, VCF 9 can be deployed on remarkably modest hardware. William Lam's widely referenced lab configurations demonstrate that VCF 9 can run on a single physical ESX host using nested virtualization, with the ESX hosts that form the VCF Instance running as VMs on the physical host. This is not a supported production configuration, but it makes VCF accessible to architects, engineers, and students who want to explore the platform without enterprise-scale hardware. The minimum resources for a nested lab deployment are approximately 256 GB of RAM and sufficient NVMe or SSD storage to accommodate the management appliances and a small number of workload VMs.

---

## Required VLAN and VMkernel Networking Design

VCF 9 requires a carefully planned Layer 2 and Layer 3 network design. The networking infrastructure must be in place before the VCF Installer runs, and mistakes in network design are among the most common causes of deployment failure. The following VLANs and VMkernel networks are required.

### Management Network

The management VLAN carries ESX host management traffic, vCenter management traffic, SDDC Manager communications, NSX Manager API traffic, and all inter-appliance communication for the management stack. Every ESX host has a VMkernel port on this VLAN. This is typically a routed network with access to DNS, NTP, Active Directory, and other infrastructure services. An MTU of 1500 is sufficient for management traffic, though many designs standardize on 9000 across all VLANs for consistency.

### vMotion Network

The vMotion VLAN carries live migration traffic between ESX hosts. vMotion is latency-sensitive and bandwidth-intensive, particularly during large-scale migrations or DRS rebalancing events. Each ESX host has a VMkernel port on this VLAN. IP addresses are assigned automatically from a network pool defined in vCenter. An MTU of 9000 (jumbo frames) is strongly recommended and is effectively required for acceptable vMotion performance in production.

### vSAN Network

If vSAN is used as principal storage, a dedicated VLAN carries all vSAN inter-host traffic---data replication, resyncing, and metadata exchange. vSAN traffic is both latency-sensitive and throughput-intensive; in an ESA deployment with NVMe drives, the network can be the bottleneck if not properly sized. Each ESX host has a VMkernel port on this VLAN. An MTU of 9000 is required. For vSAN ESA deployments, 25 GbE is the recommended minimum per host; 100 GbE is preferred for large clusters or performance-sensitive workloads.

### NFS Network (If Applicable)

When NFS is used as principal storage, a dedicated VLAN carries NFS traffic between ESX hosts and the NFS storage array. This VLAN should be isolated from other traffic types to ensure consistent storage performance. MTU 9000 is recommended.

### NSX Host Overlay (Host TEP) Network

The NSX overlay VLAN carries encapsulated tenant network traffic between ESX hosts. Each ESX host has a Tunnel Endpoint (TEP) interface on this VLAN. The minimum MTU for the overlay VLAN is 1600 (to accommodate the Geneve encapsulation header), but an MTU of 9000 is strongly recommended to avoid fragmentation and maximize throughput. This MTU must be supported end-to-end through every physical switch and router in the path.

IP addresses for host TEPs can be assigned via DHCP or from a static IP pool. In production deployments, static IP pools are generally preferred for predictability and troubleshooting simplicity.

### NSX Edge Overlay (Edge TEP) Network

NSX Edge nodes have their own TEP interfaces, which must be on a separate VLAN from the host TEPs. This separation is architecturally required when the Edge TEP VLAN and host TEP VLAN need to be routable to each other via an external router. The Edge TEP VLAN has the same MTU requirements as the host TEP VLAN (minimum 1600, recommended 9000).

### Edge Uplink Networks

NSX Edge nodes connect to the physical network through uplink interfaces. These uplinks carry north-south traffic between the overlay network and the physical network. Typically, two uplink VLANs are configured for redundancy, connecting to separate Top-of-Rack switches or router interfaces. These VLANs carry routed traffic and must be configured with appropriate BGP or static routing to the upstream network infrastructure.

### Design Principles

Several principles apply across all VCF networking:

- **Jumbo frames end-to-end.** Configure MTU 9000 on all VLANs (management, vMotion, vSAN, overlay, Edge TEP, Edge uplink) and verify jumbo frame support on every physical switch, router, and interconnect in the path. A single device that does not support jumbo frames will silently drop oversized frames, causing intermittent and difficult-to-diagnose failures.
- **No VLAN overlap.** Each traffic type must have its own dedicated VLAN. Sharing VLANs between traffic types (for example, combining vMotion and vSAN on the same VLAN) is not supported and will cause deployment validation failures.
- **DNS and NTP first.** Forward and reverse DNS resolution must be functional for all management appliances before deployment begins. NTP must be synchronized across all hosts and appliances. Clock skew and DNS failures are the two most common root causes of deployment and lifecycle operation failures.
- **Physical switch configuration.** All VLANs must be trunked to the physical ports connected to ESX hosts. Port channel or LACP configurations must match the VDS uplink teaming policy. Mismatched teaming configurations are a frequent source of intermittent connectivity issues.

---

## Storage Architecture Decision Framework

VCF 9 supports four principal storage types: vSAN ESA, vSAN OSA, Fibre Channel VMFS, and NFS v3. The choice of storage architecture affects host requirements, network design, operational procedures, and cost. The following framework helps guide this decision.

### vSAN Express Storage Architecture (ESA)

vSAN ESA is the strategic direction for hyperconverged storage in VCF. It is designed from the ground up for NVMe flash storage and modern server hardware. ESA delivers two to five times better performance compared to OSA on equivalent hardware, with up to four times better data reduction (compression and deduplication). ESA's global deduplication is policy-based, giving administrators control over where deduplication is applied and where it is not.

**When to choose ESA:**
- All new greenfield deployments where NVMe-compatible hardware is available.
- Environments that prioritize storage performance and efficiency.
- Deployments where minimizing the host count for a given workload capacity is important (ESA's better data reduction means fewer hosts for the same effective capacity).
- vSAN stretched cluster deployments, where ESA's ability to compress data before cross-site replication reduces WAN bandwidth requirements.

**Minimum requirements:** Three ESX hosts with NVMe storage devices on the vSAN Hardware Compatibility List.

### vSAN Original Storage Architecture (OSA)

vSAN OSA is the legacy hyperconverged storage architecture that supports both SSD and HDD disk groups. It remains supported in VCF 9 for environments with existing OSA-compatible hardware or where NVMe hardware is not yet available.

**When to choose OSA:**
- Brownfield environments with existing OSA disk groups that are being upgraded to VCF 9.
- Environments where NVMe hardware is not available or not cost-justified.
- Note that all new deployments should prefer ESA when hardware permits. OSA is a maintenance-mode architecture; future innovation will focus on ESA.

**Minimum requirements:** Three ESX hosts with compatible SSD and/or HDD devices configured in disk groups.

### Fibre Channel VMFS

VCF 9 introduced support for Fibre Channel VMFS as principal storage for both the Management Domain and Workload Domains during greenfield deployments. This is a landmark change that opens VCF to organizations with significant SAN investments.

**When to choose Fibre Channel:**
- Organizations with existing Fibre Channel SAN infrastructure (arrays, fabric switches, expertise) that want to leverage those investments.
- Environments that require the two-host minimum for the Management Domain (FC allows a two-host management cluster, reducing entry cost).
- Workloads that require specific storage array features (snapshots, replication, deduplication) provided by the external array.
- Environments where separating compute from storage is an architectural requirement (for example, when compute and storage refresh cycles are independent).

**Considerations:** Fibre Channel requires additional infrastructure (SAN fabric, zoning, array management) that is outside VCF's lifecycle management. The storage array is managed independently, and VCF has no visibility into array-level operations.

### NFS v3

NFS v3 is supported as principal storage, providing a network-attached storage option that avoids the need for Fibre Channel infrastructure.

**When to choose NFS:**
- Organizations with existing NFS-capable storage arrays (NetApp, Dell, etc.).
- Environments that want external storage without the complexity of Fibre Channel fabric.
- Small deployments where the two-host Management Domain minimum is desired but Fibre Channel infrastructure is not available.
- Environments where storage administration is handled by a separate team with NAS expertise.

**Considerations:** NFS traffic should be on a dedicated VLAN with MTU 9000. NFS performance is dependent on the network path between ESX hosts and the NFS array; network congestion or misconfiguration directly impacts storage performance.

### Decision Summary

| Criterion | vSAN ESA | vSAN OSA | Fibre Channel | NFS v3 |
|---|---|---|---|---|
| Minimum hosts (management) | 3 | 3 | 2 | 2 |
| Hardware requirement | NVMe | SSD/HDD | FC HBA + SAN | NIC + NAS |
| Performance tier | Highest | Moderate | Array-dependent | Array-dependent |
| Data reduction | Best (4x) | Good | Array-dependent | Array-dependent |
| Lifecycle integration | Full | Full | Storage external | Storage external |
| Strategic direction | Primary | Maintenance | Supported | Supported |
| Network dependency | vSAN VLAN | vSAN VLAN | FC fabric | NFS VLAN |

For most new deployments, vSAN ESA is the recommended choice. It provides the highest performance, the best data reduction, and full lifecycle integration with VCF. External storage (FC or NFS) is the right choice when existing infrastructure investments justify it, when the two-host management minimum is needed, or when specific array features are required.

---

## Design Maximums and Scalability Limits

Every architecture must be designed within the supported limits of the platform. VMware publishes configuration maximums through the Configuration Maximums tool at configmax.broadcom.com, which provides the tested, recommended, and fully supported limits for every VMware product. The following are the key scalability boundaries that shape VCF 9 designs.

### VCF Instance Limits

- **Domains per Instance:** 25 total (1 Management Domain + up to 24 Workload Domains).
- **Concurrent cluster creation operations per Instance:** 10 (applicable per single domain or across multiple Workload Domains).
- **Hosts per cluster:** Up to 96 hosts per vSphere cluster (subject to storage type---vSAN clusters have their own maximums).
- **Clusters per domain:** Multiple clusters per domain are supported, bounded by the vCenter maximum of 15 clusters per vCenter instance for VCF-managed environments.

### vSAN Limits

- **Hosts per vSAN cluster:** Up to 96 hosts per vSAN ESA cluster; up to 64 hosts per vSAN OSA cluster.
- **vSAN stretched cluster:** Supported with ESA or OSA, with specific host count requirements per site.
- **vSAN file shares:** Up to 500 file shares per cluster, with up to 100 SMB shares.

### NSX Limits

- **NSX Manager cluster:** 1 or 3 nodes (Simple or HA).
- **Transport zones, logical switches, and security groups:** Consult configmax.broadcom.com for the specific release; NSX 9 supports up to 10,000 security groups and 10,000 IPSets in a 10-node Extra Large cluster deployment of VCF Operations for Networks.

### VCF Operations Sizing

VCF Operations sizing determines how many objects (hosts, VMs, clusters, datastores, switches) can be managed:

| Node Size | Maximum Objects |
|---|---|
| Extra Small | 700 |
| Small | 10,000 |
| Medium | 30,000 |
| Large | 44,000 |
| Extra Large | 100,000 |

### Fleet-Level Limits

- **Fleet-to-Instance RTT:** Maximum 300ms.
- **Intra-Instance RTT (Collector to management components):** Maximum 50ms.
- **Instances per Fleet:** Consult current Broadcom documentation for the supported maximum; architectural discussions frequently reference deployments with tens of Instances per Fleet.

### Using configmax.broadcom.com

The Configuration Maximums tool is interactive and version-specific. Always select the exact VCF release (9.0, 9.0.1, 9.0.2, etc.) when checking limits, as maximums can change between point releases. The tool covers vCenter, ESX, NSX, vSAN, and VCF-specific limits. Bookmark it. Consult it during every design review. Never rely on limits memorized from a previous release.

---

## VMware Validated Solutions (VVS)

VMware Validated Solutions are prescriptive, tested, and documented architectures that extend the core VCF platform to address specific use cases. They are not products to be purchased separately; they are architectural blueprints---detailed design guides, implementation procedures, and operational runbooks---that show you how to combine VCF with additional VMware capabilities to solve real-world problems.

### What VVS Provides

Each Validated Solution includes four layers of guidance:

1. **Design:** Detailed design decisions with rationale, covering component placement, sizing, networking, and integration points.
2. **Implementation:** Step-by-step procedures for deploying the solution on a VCF platform.
3. **Configuration:** Post-deployment configuration, including policies, alerts, dashboards, and integrations.
4. **Operations:** Ongoing operational procedures, including monitoring, troubleshooting, and lifecycle management.

The solutions are tested against specific VCF releases and are updated as new VCF versions ship. For VCF 9, the Validated Solutions have been updated to reflect the new Fleet/Instance hierarchy, the VCF Operations and VCF Automation components, and the architectural changes in vCenter 9, NSX 9, and vSAN 9.

### Available Validated Solutions for VCF 9

The following Validated Solutions are available for VCF 9, organized by category:

**Identity and Security**

- **Identity and Access Management for VMware Cloud Foundation.** Provides detailed design and implementation guidance for integrating Active Directory as an identity provider and authentication source across the VCF platform. This is a foundational solution that most production deployments should implement.
- **Lateral Security for VMware Cloud Foundation with VMware vDefend.** Addresses microsegmentation and east-west security using VMware's vDefend capabilities, enabling zero-trust networking within and across Workload Domains.

**Monitoring and Operations**

- **Health Reporting and Monitoring for VMware Cloud Foundation.** Provides guidance on monitoring the operational state of your VCF environment through custom dashboards, alerts, and notifications. This solution helps you build proactive monitoring that catches issues before they become outages.
- **Intelligent Operations Management for VMware Cloud Foundation.** Delivers a centralized monitoring and alerting platform for proactive management of system failures across the entire VCF estate.
- **Intelligent Logging and Analytics for VMware Cloud Foundation.** Provides scalable log management with dashboards for analyzing log data from all VCF components. Essential for troubleshooting, compliance, and security forensics.
- **Intelligent Network Visibility for VMware Cloud Foundation.** Addresses network analysis and optimization, helping you build and maintain a highly available and secure network infrastructure.

**Disaster Recovery and Resilience**

- **Site Protection and Disaster Recovery for VMware Cloud Foundation.** Provides guidance for configuring disaster recovery of SDDC management components and workloads using VMware Live Recovery across dual-Instance deployments. This solution covers the protection of VCF Automation, VCF Operations, and fleet management components.
- **On-Premises Ransomware Recovery for VMware Cloud Foundation.** Addresses the recovery of business-critical workloads within an Isolated Recovery Environment following a ransomware event. Given the current threat landscape, this solution has moved from "nice to have" to "essential" for most enterprises.

**Workloads and Developer Infrastructure**

- **Developer Ready Infrastructure for VMware Cloud Foundation.** Provides design, implementation, and operational guidance for a Workload Domain configured to run container workloads using vSphere with Tanzu (Supervisor clusters). This is the starting point for organizations adopting Kubernetes on VCF.
- **Private AI Ready Infrastructure for VMware Cloud Foundation.** Delivers guidance for building enterprise private AI GPU-enabled infrastructure, enabling data scientists and ML engineers to leverage GPU resources within VCF Workload Domains.

**Automation and Networking**

- **Private Cloud Automation for VMware Cloud Foundation.** Provides guidance on using VCF Automation for cloud provisioning and self-service, including template design, approval workflows, and governance policies.
- **Advanced Load Balancing for VMware Cloud Foundation.** Covers the deployment and configuration of NSX Advanced Load Balancer (formerly Avi Networks) as the load balancing solution for workloads running on VCF.

### How to Use VVS

The Validated Solutions are published on Broadcom's TechDocs portal at techdocs.broadcom.com and on the VMware Validated Solutions GitHub repository (github.com/vmware/validated-solutions-for-cloud-foundation). The GitHub repository includes automation scripts, PowerShell modules, and configuration templates that accelerate implementation.

The recommended approach is to:

1. **Start with the design guide** for each solution you plan to implement. Understand the architectural decisions and their rationale before touching any configuration.
2. **Map the design to your environment.** The Validated Solutions are written for a reference architecture; your environment will differ. Identify where you need to adapt the design (different VLAN IDs, different IP ranges, different naming conventions) while preserving the architectural intent.
3. **Follow the implementation guide sequentially.** The steps are ordered for a reason; skipping ahead or reordering steps frequently leads to configuration errors that are difficult to diagnose.
4. **Implement the operational procedures.** The value of a Validated Solution is not just in the initial deployment---it is in the ongoing operational practices that keep the solution healthy over time.

Validated Solutions are not optional extras. They represent VMware's accumulated expertise in deploying these capabilities on VCF at scale. Ignoring them in favor of ad hoc implementation is a false economy that typically results in longer deployment times, more support tickets, and architectures that are harder to upgrade.

---

## Bringing It All Together

The architecture of VCF 9 is designed around a simple but powerful principle: structure at every level. The Private Cloud/Fleet/Instance hierarchy provides organizational structure. The Management Domain/Workload Domain/Edge Cluster model provides operational structure. The component architecture---VCF Installer, VCF Operations, VCF Automation, vCenter, ESX, NSX, vSAN---provides functional structure. The deployment modes provide flexibility structure. The VLAN and VMkernel design provides network structure. The storage decision framework provides infrastructure structure. The configuration maximums provide boundary structure. And the Validated Solutions provide implementation structure.

As a VMware Technical Account Manager, I have seen architectures succeed when they respect these structures and fail when they ignore them. The organizations that invest time in understanding the hierarchy, designing their VLANs before opening the Installer, choosing their storage architecture based on workload requirements rather than habit, and implementing Validated Solutions rather than improvising---these are the organizations that run VCF successfully at scale for years.

The architecture described in this chapter is the foundation for everything that follows in this book. Subsequent chapters will build on these concepts as we explore networking in depth, storage design, lifecycle management, automation, and operational best practices. Return to this chapter when you need to ground a specific design decision in the broader architectural context.

VCF 9 is the most capable and the most opinionated private cloud platform VMware has ever shipped. Understanding its architecture is not optional---it is the prerequisite for every decision you will make.
