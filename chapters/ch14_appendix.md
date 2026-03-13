# Chapter 14: Reference and Appendix

This chapter serves as the definitive reference companion for VMware Cloud Foundation 9. Whether you need a quick definition during a design session, the exact build number for a support case, or the right PowerCLI cmdlet for an automation script, the material that follows is organized for rapid lookup. Keep it bookmarked, keep it printed, keep it close.

---

## VCF 9 Glossary of Terms

The following glossary covers the essential terminology used throughout VMware Cloud Foundation 9.0 and this book. Terms are listed alphabetically with concise definitions.

**Affinity Rule** — A DRS policy that constrains virtual machine placement, either keeping VMs together on the same host (affinity) or separating them across hosts (anti-affinity) to satisfy availability or licensing requirements.

**Aria Operations** — See VCF Operations. The former name for the monitoring and analytics platform, now unified under the VCF Operations brand in VCF 9.

**Aria Suite Lifecycle Manager** — See VCF Operations Lifecycle Manager. The former lifecycle management tool for Aria products, rebranded and folded into VCF Operations management in VCF 9.

**Auto Deploy** — A PXE-based provisioning mechanism for ESXi hosts, deprecated in VCF 9.0 in favor of VCF Installer workflows and image-based lifecycle management.

**Availability Zone (AZ)** — A fault-isolation boundary within a VCF deployment, typically mapped to a physical rack, row, or data center to protect workloads from localized failures.

**Baselines (vLCM)** — The legacy vSphere Update Manager approach to patching ESXi hosts using baseline groups. Removed in VCF 9.0; replaced entirely by vSphere Lifecycle Manager image-based workflows.

**Bill of Materials (BOM)** — The validated, tested matrix of software component versions shipped with each VCF release. All components in a BOM are guaranteed interoperable.

**Brownfield Deployment** — The process of converting an existing vSphere environment into a managed VCF instance, as opposed to a greenfield (net-new) deployment.

**Bring-Up** — The initial automated deployment process that creates the management domain, including the first vCenter, NSX Manager cluster, SDDC Manager, and supporting services.

**Certificate Authority (CA)** — The trusted entity that signs TLS certificates for VCF management components. VCF supports both the embedded VMware Certificate Authority (VMCA) and external enterprise CAs.

**Cloud Builder** — The deployment appliance used in VCF 5.x and earlier to orchestrate the initial bring-up of a management domain. Replaced by the VCF Installer in VCF 9.0.

**Cluster** — A logical grouping of ESXi hosts within a workload domain that share common compute, storage, and networking resources. The fundamental unit of capacity in VCF.

**Cluster Image** — The desired-state software specification for all hosts in a vSphere cluster, including ESXi version, drivers, and firmware. Managed through vSphere Lifecycle Manager.

**Compliance** — The state where a host or cluster matches its desired software image or configuration profile. Non-compliant resources require remediation.

**Configuration Profile** — A cluster-level configuration specification that replaces the deprecated Host Profiles capability, allowing administrators to define and enforce ESXi host settings declaratively.

**Core-Based Licensing** — The licensing model for VCF 9, where entitlement is calculated per physical CPU core rather than per socket or per VM.

**Datastore** — A logical storage container visible to ESXi hosts. In VCF 9, datastores are typically vSAN, VMFS on Fibre Channel, or NFS. vVols datastores are deprecated.

**Desired State Configuration** — The operational model in VCF 9 where infrastructure is defined as a target specification and the platform continuously reconciles actual state toward that target.

**Distributed Resource Scheduler (DRS)** — The vSphere feature that automatically balances compute workloads across hosts in a cluster by migrating VMs via vMotion.

**Distributed Switch (VDS)** — A centrally managed virtual switch that spans all hosts in a cluster, providing consistent networking policy and simplifying network operations.

**Domain** — See Workload Domain.

**Edge Cluster** — A group of NSX Edge nodes that provide north-south routing, NAT, VPN, and load balancing services at the boundary between overlay and physical networks.

**Edge Transport Node** — An NSX Edge virtual appliance or bare-metal node that participates in the NSX data plane, handling traffic between logical and physical networks.

**Enhanced Linked Mode (ELM)** — The former capability allowing multiple vCenter Server instances to share a common SSO domain for unified management. Deprecated in VCF 9.0; replaced by VCF Operations grouping.

**ESXi** — The bare-metal hypervisor that runs directly on server hardware, providing the compute virtualization layer in VCF. Version 9.0.0.0 ships with VCF 9.0.

**Fault Domain** — A logical grouping of hosts within a vSAN cluster that maps to a physical failure boundary such as a rack, ensuring data availability when an entire domain fails.

**Fibre Channel** — A high-speed network protocol used for storage area networking. VCF 9 supports Fibre Channel as a supplementary storage option alongside vSAN.

**Fleet** — The highest-level organizational construct in VCF 9. A fleet encompasses all VCF instances managed by a single set of fleet-level management components (VCF Operations and VCF Automation).

**Fleet Management** — The centralized operational layer in VCF 9 that provides unified lifecycle management, monitoring, and automation across all VCF instances in a fleet.

**Geneve** — The network encapsulation protocol used by NSX to create overlay networks. Geneve tunnels transport Layer 2 frames across a Layer 3 physical network.

**Greenfield Deployment** — A net-new VCF installation where no prior vSphere infrastructure exists. The VCF Installer creates the entire stack from bare metal.

**Hardening** — The process of applying security configurations to VCF components to reduce attack surface, guided by the VCF STIG documentation and security baselines.

**Hardware Compatibility List (HCL)** — The Broadcom-maintained list of validated server, storage, and network hardware for use with VCF. Critical for supportability.

**High Availability (HA)** — The vSphere capability that automatically restarts virtual machines on surviving hosts when a host failure is detected.

**Host Transport Node** — An ESXi host prepared for NSX overlay networking by installing NSX kernel modules and configuring transport node profiles.

**Identity Federation** — The recommended authentication model in VCF 9 that integrates vCenter SSO with external identity providers (such as Okta, Azure AD, or ADFS) using SAML or OIDC, replacing the deprecated IWA.

**Image-Based Lifecycle** — The VCF 9 approach to host patching and upgrades where a complete software image (ESXi plus drivers and firmware) is defined, compared against running hosts, and remediated as a unit.

**Integrated Windows Authentication (IWA)** — A legacy SSO authentication mechanism removed in VCF 9.0. Administrators must migrate to LDAPS or identity federation before upgrading.

**JSON Specification** — The declarative input format used by SDDC Manager and the VCF Installer to define deployment parameters for domains, clusters, and hosts.

**Kubernetes** — The container orchestration platform integrated into VCF through vSphere with Tanzu (Supervisor clusters), enabling both VM and container workloads on the same infrastructure.

**Lifecycle Management** — The coordinated process of patching, upgrading, and maintaining all VCF components (ESXi, vCenter, NSX, SDDC Manager, VCF Operations) through a validated, sequenced workflow.

**Logical Segment** — An NSX overlay network segment that provides Layer 2 connectivity to workloads independent of the physical network topology.

**Management Domain** — The first and foundational domain in a VCF instance. It hosts all management components (vCenter, NSX Manager, SDDC Manager) and fleet-level services (VCF Operations, VCF Automation). It does not run tenant workloads.

**Management Plane** — The collective set of management services (SDDC Manager, vCenter, NSX Manager) that control and configure the VCF instance.

**Multi-Instance** — A VCF 9 deployment model where multiple VCF instances are grouped under a single fleet for centralized management while maintaining independent lifecycle and failure domains.

**Network I/O Control (NIOC)** — A VDS feature that classifies and prioritizes network traffic types (vMotion, vSAN, VM, management) to ensure bandwidth guarantees during contention.

**Network Pool** — A collection of VLANs reserved by SDDC Manager for assigning to workload domains during deployment, covering vMotion, vSAN, and host overlay transport traffic.

**NFS Datastore** — A network-attached storage datastore using the Network File System protocol, supported as supplementary storage in VCF 9 workload domains.

**NSX** — The network virtualization and security platform in VCF 9, providing overlay networking, micro-segmentation, distributed firewalling, load balancing, and VPN services. Version 9.0.0.0 ships with VCF 9.0.

**NSX Manager** — The centralized management appliance (deployed as a three-node cluster for production) that provides the API, UI, and control plane for NSX.

**NSX Policy Mode** — The single supported API and UI mode in NSX 9.0 for configuring networking and security resources. The legacy Manager mode and Manager API have been removed.

**Overlay Network** — A virtual network created by NSX that operates independently of the physical network, using Geneve encapsulation to provide tenant isolation and workload mobility.

**Per-Core Licensing** — See Core-Based Licensing.

**Policy API** — The declarative, intent-based NSX API that replaced the imperative Manager API. All new NSX configurations in VCF 9 must use the Policy API.

**PowerCLI** — See VCF PowerCLI.

**Precheck** — An automated validation step performed by SDDC Manager or VCF Operations before lifecycle operations (upgrades, expansions, domain deployments) to identify and surface potential issues.

**Principal Storage** — The primary storage type assigned to a workload domain at creation time. In most VCF deployments, this is vSAN.

**Private Cloud** — The end-state operational model delivered by VCF 9, where infrastructure is consumed as a service with self-service provisioning, metering, and policy-driven governance.

**Provider Portal** — The VCF Automation administrative interface used by infrastructure operators (providers) to configure organizations, resource allocation, and service offerings.

**SDDC Manager** — The lifecycle and operations management appliance at the heart of each VCF instance. It orchestrates domain deployments, host commissioning, lifecycle updates, and certificate management.

**Segment** — See Logical Segment.

**Service Account** — A non-interactive user account used by VCF components for inter-service communication, subject to credential rotation policies.

**Single Sign-On (SSO)** — The authentication service embedded in vCenter Server that provides centralized identity management for all VCF management components.

**Stretched Cluster** — A vSAN cluster that spans two physical sites with a witness host at a third site, providing active-active workload placement and automated failover for site-level resilience.

**Subscription License** — The VCF 9 license model that uses subscription-based license files instead of the legacy 25-character license keys. License files are applied through VCF Operations.

**Supervisor Cluster** — A vSphere cluster enabled with Kubernetes capabilities through the Supervisor, allowing operators to run both VMs and Kubernetes pods on the same infrastructure.

**Tanzu Kubernetes Grid (TKG)** — The Kubernetes distribution integrated with vSphere Supervisor that provisions conformant Kubernetes clusters for application developers.

**Tenant** — An organizational entity in VCF Automation that consumes infrastructure resources through self-service portals, isolated from other tenants by policy and network boundaries.

**Tier-0 Gateway** — The NSX logical router that provides north-south connectivity between the overlay network and the physical network. Each workload domain typically has its own Tier-0 gateway.

**Tier-1 Gateway** — An NSX logical router connected to a Tier-0 gateway that provides routing and service insertion for individual network segments within a workload domain.

**Transit Gateway** — A new VCF 9 networking construct that simplifies connectivity between workload domains and external networks by centralizing routing policy.

**Transport Node Profile** — An NSX configuration template that defines overlay transport settings (TEP pool, uplink profile, transport zone membership) applied to ESXi hosts when they are prepared for NSX.

**Transport Zone** — An NSX construct that defines the span of an overlay or VLAN-backed network. Hosts in the same transport zone can communicate over the associated logical segments.

**Unified Appliance** — The consolidated NSX deployment model in VCF 9 where NSX Manager, controller, and central control plane functions run in a single appliance type.

**Upgrade Precheck** — See Precheck.

**VCF Automation** — The fleet-level automation and self-service platform in VCF 9, formerly known as vRealize Automation / Aria Automation. Provides multi-tenancy, catalog-based provisioning, and infrastructure-as-code capabilities.

**VCF Installer** — The new deployment tool in VCF 9 that replaces Cloud Builder. It handles greenfield bring-up, brownfield conversion, and initial management domain creation.

**VCF Instance** — A discrete VCF deployment comprising one management domain and zero or more workload domains, all managed by a single SDDC Manager. Multiple instances can be grouped into a fleet.

**VCF Operations** — The fleet-level monitoring, alerting, capacity management, and compliance platform in VCF 9. Replaces the former Aria Operations / vRealize Operations product line.

**VCF Operations for Logs** — The centralized log management component that collects, indexes, and analyzes logs from all VCF components for troubleshooting and audit purposes.

**VCF Operations for Networks** — The network monitoring and analytics component that provides flow analysis, topology visualization, and network troubleshooting for NSX-managed environments.

**VCF Operations Lifecycle Manager** — The component responsible for deploying, upgrading, and managing the lifecycle of VCF Operations components themselves.

**VCF PowerCLI** — The PowerShell-based command-line automation toolkit for VCF 9, formerly VMware PowerCLI. Includes modules for vSphere, NSX, SDDC Manager, VCF Operations, and VCF Automation management.

**VCF SDK** — The software development kit providing Java and Python libraries for programmatic interaction with VCF APIs, including auto-generated client bindings for SDDC Manager and VCF Operations.

**Virtual Distributed Switch** — See Distributed Switch (VDS).

**Virtual Private Cloud (VPC)** — A tenant-facing networking construct in VCF 9 that provides isolated network environments with self-service subnet creation and security policy management.

**vMotion** — The live migration technology that moves running virtual machines between hosts without downtime, used by DRS for load balancing and by maintenance mode operations.

**VMFS** — Virtual Machine File System, a clustered file system used on Fibre Channel and iSCSI storage for hosting VM files. Supported as supplementary storage in VCF 9.

**vSAN** — The hyperconverged storage platform integrated into ESXi that aggregates local disks across cluster hosts into a shared datastore. All-flash architectures are required in VCF 9; hybrid vSAN is no longer supported.

**vSAN ESA (Express Storage Architecture)** — The next-generation vSAN storage architecture optimized for NVMe devices, offering simplified configuration, improved performance, and storage efficiency.

**vSAN OSA (Original Storage Architecture)** — The traditional vSAN architecture using cache and capacity disk groups. Still supported in VCF 9 but ESA is the recommended path forward.

**vSphere Clustering Service (vCLS)** — Agent VMs formerly required to support DRS operations. Deprecated in VCF 9.0 and recommended to be placed in retreat mode. DRS and HA continue to function without vCLS VMs.

**vSphere Configuration Profiles** — The replacement for the deprecated Host Profiles, providing cluster-level declarative configuration management for ESXi hosts.

**vSphere Foundation (VVF)** — The entry-level VMware platform offering that provides core vSphere and vSAN capabilities without the full VCF management stack. Shares the same ESXi and vCenter versions as VCF 9.

**vSphere Lifecycle Manager (vLCM)** — The image-based lifecycle management tool embedded in vCenter that defines, validates, and remediates ESXi host software across clusters.

**vSphere Pods** — Lightweight VM-based containers that run directly on the ESXi hypervisor through the Supervisor, providing strong isolation with near-native container performance.

**vSphere Trust Authority** — A security feature for attestation of ESXi host integrity. Removed in VCF 9.0.

**Witness Host** — A lightweight ESXi appliance deployed at a third site in stretched cluster configurations to serve as the tiebreaker for vSAN quorum decisions.

**Workload Domain** — A logical unit of application-ready infrastructure comprising one or more vSphere clusters managed by a dedicated vCenter and NSX Manager pair. Workload domains provide lifecycle isolation, security boundaries, and independent scaling.

**Workload Management** — The vSphere capability that enables Supervisor clusters and Kubernetes workload support on VCF infrastructure.

---

## VCF 9.0 Bill of Materials — Component Version Reference

The following table documents the validated component versions shipped with VCF 9.0.0.0 (GA, June 2025). All components in this BOM have been tested for interoperability. Always verify against the official Broadcom release notes for the most current patch levels.

| Component | Version | Build Number |
|---|---|---|
| VCF Installer | 9.0.0.0 | 24781669 |
| SDDC Manager | 9.0.0.0 | 24703748 |
| ESXi | 9.0.0.0 | 24755229 |
| vCenter Server | 9.0.0.0 | 24755230 |
| NSX | 9.0.0.0 | 24733065 |
| vSAN | 9.0.0.0 | (integrated with ESXi 9.0) |
| VCF Automation | 9.0.0.0 | 24701403 |
| VCF Operations | 9.0.0.0 | 24695812 |
| VCF Operations Lifecycle Manager | 9.0.0.0 | 24695816 |
| VCF Operations Cloud Proxy | 9.0.0.0 | 24695833 |
| VCF Operations for Logs | 9.0.0.0 | (included in VCF Operations bundle) |
| VCF Operations for Networks | 9.0.0.0 | (included in VCF Operations bundle) |
| VCF PowerCLI | 9.0.0 | 24798382 |

**Subsequent releases:**

- **VCF 9.0.1.0** (September 2025): Incremental update with patched ESXi, vCenter, and NSX builds, plus VCF Operations enhancements.
- **VCF 9.0.2.0** (January 2026): Maintenance release with Fleet Manager improvements, lifecycle updates, and additional brownfield conversion capabilities.

Note that VCF 9 adopts unified versioning across all components. The individual product version numbers (such as "vSphere 9.0 U1" or "NSX 4.2") that existed in prior releases are eliminated. Every component simply carries the VCF release version.

---

## Certification Quick Reference Card

VMware Cloud Foundation certifications were restructured under Broadcom to align with the VCF-centric product strategy. The following table summarizes the current certification landscape.

### Foundation Level

| Certification | Exam Code | Focus |
|---|---|---|
| VCP-VCF Administrator | 2V0-11.25 | Day-to-day VCF operations, domain management, lifecycle, troubleshooting |
| VCP-VCF Architect | 2V0-13.25 | VCF solution design, sizing, deployment model selection |

### Advanced Level (VCAP)

| Certification | Exam Code | Focus |
|---|---|---|
| VCAP-VCF Administration | 3V0-11.26 | Advanced VCF administration, complex lifecycle scenarios, multi-domain operations |
| VCAP-VCF Design | 3V0-12.26 | Enterprise VCF architecture, multi-site design, capacity planning |
| VCAP-VCF Automation | 3V0-13.26 | VCF Automation, self-service operations, infrastructure-as-code, Kubernetes integration |

### Preparation Resources

- **Official exam guides**: Available at broadcom.com/support/education/vmware/certification
- **VMware {code} VCP-VCF Study Group**: Peer-led study sessions and shared resources
- **Hands-on Labs**: Free lab environments for practice (see Essential Resources below)
- **VMUG Advantage**: Includes 365-day evaluation licenses and exam discounts

### Certification Tips

1. VCF 9 exams emphasize fleet management, VCF Operations, and VCF Automation concepts that did not exist in prior vSphere-only certifications.
2. Hands-on experience with SDDC Manager, the VCF Installer, and VCF PowerCLI is essential.
3. Understand the full lifecycle: greenfield deployment, brownfield conversion, domain expansion, patching, and upgrade sequencing.
4. NSX Policy mode is the only testable NSX configuration model. Legacy Manager mode knowledge is no longer assessed.

---

## Essential Resource Links

The following resources represent the most valuable references for VCF 9 practitioners. Each link is annotated with its primary use case.

### Official Broadcom Documentation

- **VCF 9 Technical Documentation (TechDocs)** — techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0.html
  The authoritative source for all VCF 9 documentation: planning guides, deployment procedures, operations manuals, and release notes.

- **VCF 9.0 Release Notes** — techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes.html
  BOM details, what's new, known issues, and product support notes for every VCF 9.x release.

- **Configuration Maximums Tool** — configmax.broadcom.com
  Interactive tool for looking up supported limits for vCenter, ESXi, vSAN, NSX, and other components. Filter by product and VCF 9.0 release to find specific maximums for cluster sizes, VM counts, network scale, and storage limits.

- **VMware Compatibility Guide** — interopmatrix.broadcom.com
  Hardware and software interoperability verification. Essential before procurement and upgrade planning.

- **Broadcom Knowledge Base** — knowledge.broadcom.com
  Searchable repository of KBs, known issues, workarounds, and resolution procedures. The first stop for troubleshooting.

### Developer and Automation Resources

- **Broadcom Developer Portal** — developer.broadcom.com
  API documentation, SDK downloads, and OpenAPI specifications for SDDC Manager, VCF Operations, VCF Automation, and NSX.

- **VCF API Reference** — developer.broadcom.com/xapis/vmware-cloud-foundation-api/latest/
  Complete SDDC Manager REST API documentation with request/response examples.

- **VCF PowerCLI Portal** — developer.broadcom.com/powercli
  Module documentation, download links, and getting-started guides for VCF PowerCLI 9.0.

- **VCF SDK (Java and Python)** — developer.broadcom.com/sdks/vcf-api-specification/latest/
  Auto-generated client libraries for programmatic VCF management.

- **VCF API Changelog** — techdocs.broadcom.com (Release Notes > What's New > CLI, API, SDK)
  Consolidated summary of API additions, removals, and deprecations across VCF releases.

### Learning and Community

- **VMware Hands-on Labs (HOL)** — labs.hol.vmware.com
  Free, browser-based lab environments for guided VCF 9 experiences. Key labs include:
  - HOL-2610-01-VCF-L: VCF 9.0 Fundamentals (Installer, VPCs, Transit Gateways)
  - HOL-2610-02-VCF-L: VCF 9.0 Automation (tenant setup, Kubernetes deployments)
  - HOL-2610-03-VCF-L: VCF 9.0 Operations (health monitoring, chargeback)

- **William Lam's Blog** — williamlam.com
  The most comprehensive community resource for VCF lab deployments, nested ESXi automation, and undocumented tips. Essential reading:
  - Ultimate Lab Resource for VCF 9.0 (williamlam.com/2025/06/ultimate-lab-resource-for-vcf-9-0.html)
  - Automated VCF 9.0 Lab Deployment Script (williamlam.com/2025/09/automated-vmware-cloud-foundation-vcf-9-0-lab-deployment-script.html)
  - Minimal VCF 9.0 Lab Setup (williamlam.com/2025/07/minimal-vmware-cloud-foundation-vcf-9-0-lab-setup.html)
  - VCF 9 resource hub (williamlam.com/vmware-cloud-foundation-9)

- **William Lam's GitHub** — github.com/lamw
  Automation scripts for nested VCF deployments, including vcf-automated-lab-deployment and vcf-9x-in-box repositories.

- **VMware User Group (VMUG)** — vmug.com
  Local and virtual community events, UserCon conferences, and VMUG Advantage membership for evaluation licenses and certification discounts.

- **vExpert Program** — vexpert.vmware.com
  Recognition program for community contributors. vExperts receive early access to betas, NFR licenses, and direct channels to product teams.

### Education and Certification

- **Broadcom Education** — broadcom.com/support/education/vmware
  Official training courses, certification exams, and digital badges for VCF 9.

- **VCF STIG Documentation** — techdocs.broadcom.com (search "VCF STIG")
  Security Technical Implementation Guides for hardening VCF environments to DoD and enterprise compliance standards.

---

## VCF PowerCLI Command Cheat Sheet

VCF PowerCLI 9.0 consolidates what was formerly VMware PowerCLI into a VCF-aligned release. The following cmdlets represent the most frequently used commands, organized by operational category.

### Installation and Connection

```powershell
# Install VCF PowerCLI from PowerShell Gallery
Install-Module -Name VCF.PowerCLI -Scope CurrentUser

# Connect to vCenter Server
Connect-VIServer -Server vcenter.lab.local -User administrator@vsphere.local

# Connect to NSX Manager
Connect-NsxServer -Server nsx-mgr.lab.local -User admin

# Connect to SDDC Manager
Connect-VcfSddcManager -Server sddc-mgr.lab.local -User administrator@vsphere.local
```

### Host and Cluster Operations

```powershell
# List all ESXi hosts
Get-VMHost

# Get host hardware information
Get-VMHost | Select-Object Name, ConnectionState, NumCpu, MemoryTotalGB

# Enter maintenance mode
Set-VMHost -VMHost esxi01.lab.local -State Maintenance

# Get cluster details
Get-Cluster

# Get DRS recommendations
Get-DrsRecommendation -Cluster "Cluster01"
```

### Virtual Machine Management

```powershell
# List all VMs
Get-VM

# Create a new VM
New-VM -Name "TestVM" -ResourcePool "Cluster01" -Datastore "vsanDatastore"

# Power operations
Start-VM -VM "TestVM"
Stop-VM -VM "TestVM" -Confirm:$false
Restart-VM -VM "TestVM" -Confirm:$false

# Take a snapshot
New-Snapshot -VM "TestVM" -Name "Pre-Upgrade" -Description "Before patching"

# Get VM guest OS information
Get-VM | Get-VMGuest
```

### Networking

```powershell
# List distributed switches
Get-VDSwitch

# List port groups
Get-VDPortGroup

# Get VM network adapters
Get-VM "TestVM" | Get-NetworkAdapter

# Set network adapter
Get-VM "TestVM" | Get-NetworkAdapter | Set-NetworkAdapter -NetworkName "Production-PG"
```

### Storage and vSAN

```powershell
# List datastores
Get-Datastore

# Get vSAN cluster information
Get-VsanClusterConfiguration -Cluster "Cluster01"

# Get vSAN disk groups
Get-VsanDiskGroup

# Get vSAN health status
Get-VsanHealthSummary -Cluster "Cluster01"

# Get storage policies
Get-SpbmStoragePolicy
```

### SDDC Manager Operations (VMware.Sdk.Vcf.SddcManager)

```powershell
# Discover available SDDC Manager operations
Get-VcfSddcManagerOperation -Method Get
Get-VcfSddcManagerOperation -Method Post
Get-VcfSddcManagerOperation -Method Put

# List workload domains
Invoke-VcfSddcManagerOperation -OperationId GetDomains

# Get host commission status
Invoke-VcfSddcManagerOperation -OperationId GetHosts

# Get lifecycle update bundles
Invoke-VcfSddcManagerOperation -OperationId GetBundles

# Get network pools
Invoke-VcfSddcManagerOperation -OperationId GetNetworkPools

# Run precheck validations
Invoke-VcfSddcManagerOperation -OperationId ValidateDomainCreationSpec
```

### VCF Operations (VMware.Sdk.Vcf.Ops)

```powershell
# Discover available VCF Operations commands
Get-VcfOpsOperation -Method Get

# Get monitored resources
Invoke-VcfOpsOperation -OperationId GetResources

# Get alerts
Invoke-VcfOpsOperation -OperationId GetAlerts

# Get compliance status
Invoke-VcfOpsOperation -OperationId GetComplianceStatus
```

### Certificate Management

```powershell
# Get certificate information for hosts
Get-VMHost | Get-VMHostCertificate

# Get vCenter certificate details
Get-VIServer | Select-Object Name, CertificateInfo
```

### Lifecycle and Patching

```powershell
# Get cluster image compliance
Get-Cluster | Get-ClusterImageCompliance

# Get available ESXi images
Get-EsxImageProfile

# Test cluster compliance
Test-Compliance -Entity (Get-Cluster "Cluster01")
```

---

## Deprecated and Removed Features: VCF 5.x to 9.0

The transition from VCF 5.x to VCF 9.0 represents the most significant architectural shift in VCF history. This section provides a comprehensive reference of everything that changed, was deprecated, or was removed. Plan accordingly before upgrading.

### Terminology Changes

| Former Term (VCF 5.x) | New Term (VCF 9.0) | Notes |
|---|---|---|
| VMware PowerCLI | VCF PowerCLI | Aligned with VCF versioning and release cycle |
| Cloud Builder | VCF Installer | Complete replacement; new appliance and workflow |
| Aria Operations / vRealize Operations | VCF Operations | Integrated as fleet-level service |
| Aria Automation / vRealize Automation | VCF Automation | Integrated as fleet-level service |
| Aria Suite Lifecycle Manager | VCF Operations Lifecycle Manager | Manages VCF Operations component lifecycle |
| Aria Operations for Logs | VCF Operations for Logs | Rebranded under VCF Operations umbrella |
| Aria Operations for Networks | VCF Operations for Networks | Rebranded under VCF Operations umbrella |
| NSX-T Data Center / NSX | NSX (version 9.0) | Simplified naming, unified with VCF versioning |
| vSphere 8.x Update numbering | Unified VCF version (9.0.x.x) | No more independent vSphere version labels |
| License Key (25-character) | Subscription License File | License files replace legacy keys |
| Host Profiles | Configuration Profiles | Cluster-level declarative configuration |
| NSX Manager Mode + Policy Mode | Policy Mode (only) | Manager mode entirely removed |

### Deprecated Features (Present but Planned for Removal)

The following features exist in VCF 9.0 but are officially deprecated and will be removed in a future release. Do not build new dependencies on them.

| Feature | Deprecation Details | Recommended Alternative |
|---|---|---|
| vSphere Virtual Volumes (vVols) | Deprecated in VCF 9.0; planned removal in VCF 9.3 | Migrate to vSAN or VMFS datastores |
| vSphere Clustering Service (vCLS) | Deprecated; recommend retreat mode immediately | DRS and HA operate without vCLS agent VMs |
| vSphere Auto Deploy | Deprecated; will be removed in a future ESXi release | Use VCF Installer and image-based lifecycle |
| Enhanced Linked Mode (ELM) | Deprecated; supported only for upgrade compatibility | Use VCF Operations grouping for multi-vCenter management |
| vSphere Host Profiles | Deprecated in vCenter 9.0 | Use vSphere Configuration Profiles (cluster-level) |

### Removed Features (No Longer Available in VCF 9.0)

The following features have been completely removed. Environments relying on them must migrate before upgrading to VCF 9.0.

| Feature | Details | Migration Path |
|---|---|---|
| NSX Manager Mode / Manager API | Removed entirely; only Policy mode remains | Convert all configurations to NSX Policy API before upgrade |
| Integrated Windows Authentication (IWA) | Removed from vCenter SSO | Migrate to Active Directory over LDAPS or identity federation (SAML/OIDC) |
| vSphere Lifecycle Manager Baselines | Legacy VUM baseline-based patching removed | Transition all clusters to vLCM image-based lifecycle |
| vSphere Trust Authority | Removed in VCF 9.0 | No direct replacement; use TPM-based attestation where required |
| CIM Providers on ESXi | Removed; legacy hardware monitoring protocol | Use native ESXi APIs and vendor-specific management tools |
| SLP (Service Location Protocol) on ESXi | Removed for security hardening | No replacement needed; services discovered through other mechanisms |
| Smart Card Authentication (ESXi) | Removed from ESXi 9.0 | Use identity federation for administrative access |
| RSA SecurID Authentication (ESXi) | Removed from ESXi 9.0 | Use identity federation with MFA |
| Hybrid vSAN (HDD + SSD) | Not supported in VCF 9.0 | Migrate to all-flash vSAN (OSA or ESA) before upgrade |
| Cloud Builder Appliance | Replaced entirely | Use VCF Installer for all deployment operations |
| Independent vSphere/NSX versioning | Eliminated in favor of unified VCF versioning | All components carry the VCF release version number |

### Dropped Hardware Support

| Hardware Type | Details |
|---|---|
| Spinning disk (HDD) for vSAN capacity | All-flash required; no hybrid vSAN support |
| Servers without TPM 2.0 | VCF 9 requires TPM 2.0 for host attestation |
| Legacy NICs without Geneve offload | NSX 9.0 performance features require modern NIC capabilities |
| Non-NVMe boot devices for ESA | vSAN ESA requires NVMe storage devices |

### Upgrade Planning Checklist

Before upgrading from VCF 5.x to VCF 9.0, verify the following:

1. All NSX configurations have been migrated from Manager mode to Policy mode.
2. All vLCM clusters use image-based lifecycle (no baseline-managed clusters remain).
3. IWA authentication has been migrated to LDAPS or identity federation.
4. vVols datastores have been evacuated and VMs migrated to vSAN or VMFS.
5. Hybrid vSAN clusters have been converted to all-flash.
6. Host Profiles have been replaced with Configuration Profiles.
7. All servers meet VCF 9.0 HCL requirements, including TPM 2.0.
8. vCLS has been evaluated for retreat mode.
9. CIM-based monitoring tools have been replaced with API-based alternatives.
10. Backup and recovery procedures have been validated for all management components.

---

## Acknowledgments

A work of this scope is never the product of a single mind. The VMware Cloud Foundation ecosystem is built and sustained by an extraordinary community of engineers, architects, operators, and educators whose contributions extend far beyond any single product release.

**William Lam**, Distinguished Platform Engineering Architect at Broadcom, deserves particular recognition. His tireless work documenting nested lab deployments, minimal resource configurations, and automation scripts at williamlam.com has made VCF accessible to thousands of practitioners who lack enterprise-scale hardware. The automated VCF lab deployment scripts, the minimal resource guides, and the continuously updated VCF 9 resource hub represent an unmatched contribution to the community. Every chapter in this book was tested on infrastructure first proven in William's nested lab configurations.

The **VCF Product Management and Engineering teams** at Broadcom built what this book describes. The architectural vision that unified disparate products into a coherent private cloud platform, the fleet management model that finally delivers on the promise of infrastructure at scale, and the relentless focus on lifecycle simplification reflect years of listening to customer feedback and making hard trade-offs. The deprecation decisions cataloged in this chapter were not made lightly; they were made to move the platform forward.

The **vExpert community** and **VMware User Groups (VMUG)** worldwide provide the connective tissue between product teams and practitioners. The blog posts, conference sessions, study groups, and candid feedback loops that characterize this community are what keep VMware technology grounded in operational reality. The VCP-VCF study groups, the VMUG UserCon presentations, and the countless late-night Slack discussions about upgrade sequencing and NSX Policy migration all shaped the guidance in this book.

The technical reviewers, community bloggers, and content creators whose work informed the research for this book — including the authors at vInfrastructure Blog, vNinja.net, My Cloudy World, Digital Thought Disruption, and dozens of others — represent the best of the VMware ecosystem. Their willingness to document what they learn, including the failures and workarounds, makes the entire community stronger.

The author's work as a Technical Account Manager at VMware by Broadcom provided daily exposure to customer deployments, product teams, and the real-world challenges that shaped every chapter. Special thanks to the customers and colleagues who asked hard questions — this book is the answer.

---

*VMware Cloud Foundation 9: The Definitive Guide*
*Pranav Patel*
*Technical Account Manager, VMware by Broadcom*
