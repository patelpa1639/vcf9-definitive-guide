# VMware Cloud Foundation 9: Platform Research

**Research Date:** March 2026
**Scope:** Comprehensive findings for book chapters on VCF 9 platform changes, architecture, operations, automation, and lifecycle.

---

## 1. VCF 9 What's New — Major Platform Changes

VMware Cloud Foundation 9.0, generally available since June 17, 2025 (build 24755599), represents a fundamental repositioning of the VMware private cloud platform. Where earlier releases treated VCF primarily as a lifecycle manager for vSphere components, VCF 9 reframes the product so that private-cloud operations and private-cloud consumption sit at the center. Operators work from a single policy model, API surface, and lifecycle engine regardless of whether they are troubleshooting capacity or onboarding tenants.

### Core Architectural Innovations

**Memory and Storage Efficiency**
- Advanced NVMe Memory Tiering (now GA) extends DRAM pools using fast flash as a secondary tier, allowing more VMs or containers per host without additional memory investment. NVMe devices can be added locally to an ESXi host and used transparently as tiered memory.
- vSAN Global Deduplication spans entire clusters rather than individual disks, eliminating duplicate blocks across the infrastructure and recovering significant storage capacity at scale.

**Networking Enhancements**
- Enhanced data paths with kernel optimizations and optional DPU offload reduce latency on east-west traffic, particularly benefiting AI inference pipelines and microservice meshes.
- GPU workload mobility has been substantially improved, with enhanced vMotion for GPU-enabled VMs supporting near-zero downtime migrations.

**AI and Performance**
- VCF 9 sets a new benchmark for Private AI workloads, claiming near bare-metal efficiency for inference jobs. Internal benchmarks reference MLPerf Inference v5 results from April 2025.
- VMs, containers, and Kubernetes are positioned as first-class citizens, deployable side-by-side with integrated management.

**Unified API Access**
- Terraform providers and REST endpoints provide programmatic access to the full platform.
- VCF Automation blueprints enable declarative infrastructure provisioning.
- Native CI/CD hooks include integrated Argo CD support.
- Pre-built blueprints cover databases, AI stacks, and Kubernetes clusters.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/06/17/whats-new-in-vmware-cloud-foundation-9-0/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes.html

---

## 2. The Fleet / Instance / Private Cloud Hierarchy

VCF 9 introduces a formal hierarchical model for managing infrastructure at scale. The construct, from top to bottom, is: **Private Cloud > Fleet > Instance > Workload Domain > Cluster**.

### Hierarchy Levels

**Private Cloud (Top Level)**
A VCF Private Cloud is the highest level of management and consumption for the underlying software-defined data center resources. A Private Cloud can contain one or more VCF Fleets.

**Fleet**
A Fleet is the umbrella for shared services and governance. It includes all the infrastructure managed by common instances of VCF Operations and VCF Automation, along with vCenter, NSX Manager, vSphere clusters, workload domains, and other components. There is only one instance of VCF Operations and one instance of VCF Automation per fleet, though multiple VCF Instances can exist within a single fleet.

**Instance**
An Instance is a discrete VCF deployment under the fleet umbrella. Each Instance comprises a management domain plus optional VI (Virtual Infrastructure) workload domains. Instances are the unit of deployment — each gets its own SDDC Manager, vCenter, NSX, and vSAN components for the management domain.

**Workload Domain**
Each workload domain has a dedicated vCenter Server for management. The management domain is special — it hosts the fleet-level services.

**Cluster**
The traditional vSphere cluster of ESXi hosts remains the bottom of the hierarchy.

### Multi-Instance Management

The fleet model enables centralized management of geographically distributed infrastructure. A key enabler is the 300ms maximum supported Round Trip Time (RTT) for fleet-to-instance communication. This means a central VCF Fleet can manage remote VCF Instances across global distances without requiring ultra-low-latency dedicated fiber.

Fleet-level services provide:
- Unified licensing through a single license file per VCF Operations instance
- Centralized SSO and identity management
- Consolidated certificate and password management
- Fleet-wide lifecycle management and patching
- Cost management and chargeback across all instances

### Deployment Models

VCF 9 supports two appliance deployment models:
- **Simple Model:** Single-node appliances, suitable for smaller environments.
- **High Availability Model:** Three-node configurations for NSX Manager, VCF Operations, and VCF Automation. Recommended for production.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/design/vmware-cloud-foundation-concepts/vcf-operations-deployment-models.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/overview-of-vmware-cloud-foundation-9/what-is-vmware-cloud-foundation-and-vmware-vsphere-foundation/vcf-operations-overview/fleet-management.html
- https://digitalthoughtdisruption.com/2026/02/16/vcf-9-0-ga-mental-model-part-1-fleets-instances-domains/
- https://blogs.vmware.com/cloud-foundation/2025/07/28/planning-a-successful-vmware-cloud-foundation-9-0-deployment/

---

## 3. VCF Operations Console — What It Replaced and Capabilities

### What It Replaced

VCF Operations consolidates and replaces multiple previously separate management tools:
- **SDDC Manager UI** — deprecated in VCF 9, to be removed in a future major release. Its workflows have moved to VCF Operations and the vSphere Client.
- **Aria Suite Lifecycle (vRSLCM)** — lifecycle management for Aria/vRealize components is now built into VCF Operations Fleet Management.
- **Aria Operations** — monitoring and analytics capabilities are now integrated as VCF Operations.
- **Fragmented management across multiple consoles** — VCF Operations delivers a single pane of glass.

Note: SDDC Manager is still deployed with VCF 9 as a backend component in each instance, but its UI is deprecated and its APIs for identity configuration are deprecated. The functionality has moved to VCF Operations.

### Core Capabilities

**Fleet Management**
- Unified licensing through single license files (replacing 25-character hex keys)
- Centralized SSO with support for Active Directory, Azure AD, OKTA, Ping, and OAuth 2.0
- Certificate management with non-disruptive updates and automatic renewals
- Password status dashboard with rotation and expiration notifications
- Consolidated lifecycle management with reduced host reboots and automated multi-cluster upgrades

**Health and Diagnostics**
- Single-pane-of-glass to correlate issues across the infrastructure through diagnostic findings
- VM operations monitoring (vMotion, snapshots, vSAN health)
- AI-assisted log correlation for root cause analysis
- Log Assist feature for support bundle generation

**Storage Operations**
- Single console for inventory, configuration, and performance management
- Storage distribution insights showing allocation across clusters and workloads

**Network Operations**
- Integrated network health monitoring with traffic analysis
- Automatic business application discovery and monitoring

**Supervisor and Kubernetes Monitoring**
- Native support for Supervisor clusters and vSphere Kubernetes Service
- CPU, memory, disk, node, pod, and container-level metrics

**Workload Migration Planning**
- Application and network dependency discovery
- Migration waves for phased execution
- Resource utilization insights

**Security Operations (SecOps)**
- Real-time dashboards covering authentication, permissions, and infrastructure security
- Host encryption, compliance status, vSAN encryption, CVE advisories, certificate health, and VM encryption monitoring
- Continuous compliance checking against CIS, DISA STIG, FISMA, HIPAA, ISO, PCI DSS
- New VCF 9 compliance packs: CIS vSphere 8.0, NIST SP 800-171, NIST SP 800-53 R5
- Configuration drift detection with auto-correction capability

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/06/17/modern-infrastructure-operations-vcf-9-0/
- https://my-cloudy-world.com/2025/07/03/vmware-cloud-foundation-9-0-transitioning-from-sddc-manager-to-vcf-operations/
- https://techietech.org/vcf-9-deprecated-redesigned-replaced-features-capabilites/

---

## 4. Quick Start App / VCF Installer for Deployment

### Cloud Builder Is Gone — VCF Installer Takes Over

The VCF Installer appliance replaces the Cloud Builder appliance used in all prior VCF versions. This is one of the most visible changes for anyone who has deployed VCF before.

Key differences from Cloud Builder:
- **No more Excel spreadsheet.** The Deployment Parameters Workbook (an Excel file that had to be meticulously filled out) is eliminated. The VCF Installer provides a UI-driven workflow instead.
- **Does not bundle software binaries.** Unlike Cloud Builder, the VCF Installer is lightweight. Users download binaries from the Broadcom Support Portal or configure private offline depots.
- **Supports reusable JSON deployment specifications.** Configurations can be exported as JSON for repeatable deployments across multiple sites.
- **Built-in convergence and import workflows.** The VCF Installer includes options to converge and import existing vCenter, VCF Operations, and VCF Automation instances without needing separate VCF Import scripts.

### Four Deployment Pathways

VCF 9 supports four distinct starting points for getting onto the platform:

1. **Deploy a New VCF Instance** — Greenfield deployment as part of a new fleet. Requires minimum 4 hosts for the management cluster. Supports vSAN ready nodes (recommended), NFS, or VMFS on FC storage.

2. **Expand an Existing Fleet** — Add new VCF instances to an already-established fleet, extending fleet-level management to additional sites or capacity.

3. **Converge an Existing vCenter Deployment** — Brownfield pathway that repurposes an existing vCenter environment by deploying the VCF Installer into the same cluster. Preserves current infrastructure while enabling full VCF functionality and lifecycle management.

4. **Import a vCenter Deployment** — Integrate existing vCenter environments as workload domains into an existing VCF 9 instance. Supports various storage (vSAN, NFS, VMFS) and networking configurations.

### Planning Tools

- The **VCF Planning and Preparation Workbook** (downloadable from techdocs.broadcom.com) is a dynamic Excel tool that accommodates multiple deployment topologies and guides users through configuration inputs.
- The **VMware Private Cloud Maturity Model Assessment** evaluates five disciplines: vision/strategy, business outcomes, governance/processes, people/tools, and security/operations/compliance.
- Pre-deployment validation tools include the Broadcom Configuration Maximums tool, Broadcom Compatibility Guide, and VMware Ports and Protocols tool.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/07/03/vcf-9-0-deployment-pathways/
- https://blogs.vmware.com/cloud-foundation/2025/07/28/planning-a-successful-vmware-cloud-foundation-9-0-deployment/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/deploying-a-new-vmware-cloud-foundation-or-vmware-vsphere-foundation-private-cloud-.html

---

## 5. VCF Automation (VCFA) — Provider/Org Model and Self-Service Cloud

### What VCF Automation Replaces

VCF Automation (VCFA) supersedes two previously separate VMware products:
- **VMware Aria Automation** (formerly vRealize Automation) — the enterprise automation and self-service platform.
- **VMware Cloud Director (VCD)** — the multi-tenant cloud management platform used primarily by service providers.

VCFA is not a simple rebranding. It is a purpose-built next-generation platform that draws from both predecessors:
- The **provider-facing layer** (Tenant Manager) is built on the VCD codebase, so the UI and APIs are familiar to experienced VCD administrators.
- The **tenant experience** draws heavily from Aria Automation, offering a modernized interface that looks significantly different from traditional VCD.

Note: VCF Automation Pipelines (the CI/CD pipeline feature formerly in Aria Automation) has been deprecated.

### Provider Management

The Provider Management capability allows IT teams and Cloud Service Providers to manage, deliver, and scale cloud services across multiple vCenter and VCF instances. The Provider Management Portal is a dedicated interface for Provider Administrators.

Provider configuration involves:
- **Regions** — Logical groupings of compute, storage, and networking resources tied to vCenter and NSX instances.
- **IP Spaces** — Structured IP address allocation with quotas for external connectivity.
- **Provider Gateways** — Network boundaries leveraging NSX Tier-0 infrastructure.

### Organization Model (Multi-Tenancy)

An Organization is a top-level construct created by the provider administrator to represent a tenant or line of business. Each organization operates within its own secure, isolated boundary, ensuring users can only access services and resources assigned to them.

VCFA introduces two organization types:
1. **Provider Consumption Organizations (PCO)** — Allow providers to share blueprints and workflows across tenant organizations.
2. **Tenant Organizations** — Each customer receives isolated access to their own VMs, networks, storage, and Kubernetes clusters.

Organizations consume capacity from a shared resource pool and subdivide those resources for their application teams, modeled as **Projects** and **Namespaces**.

### Self-Service Capabilities

Application teams can provision resources through:
- Self-service user interfaces
- APIs and CLI
- Rich metrics for monitoring infrastructure performance and health
- Day 2 operations with approvals and cost visibility
- Policy-driven governance

The platform supports:
- VM-based applications
- Kubernetes workloads (via vSphere Kubernetes Service / VKS)
- AI workloads
- Pre-built blueprints and templates
- Terraform provider for infrastructure-as-code automation

### vSphere Supervisor Integration

VCF Automation natively supports vSphere Supervisor, enabling Kubernetes workloads to run directly on ESXi hosts and creating upstream Kubernetes clusters within dedicated namespaces.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/overview-of-vmware-cloud-foundation-9/what-is-vmware-cloud-foundation-and-vmware-vsphere-foundation/vcf-automation-overview.html
- https://vmtechie.blog/2025/07/21/navigating-the-shift-from-vmware-cloud-director-to-vcf-automation-in-vmware-cloud-foundation-9/
- https://fojta.wordpress.com/2025/06/17/quck-guide-to-vcf-automation-for-vcd-administrators/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/organization-management/vcfa-overview.html

---

## 6. Embedded Cost Control — Chargeback and Showback

VCF 9 integrates FinOps principles directly into the platform, providing financial accountability, operational visibility, and business alignment for IT users through VCF Operations.

### Total Cost of Ownership (TCO)

VCF Operations reveals comprehensive infrastructure costs including hardware, software, support, and overhead expenses. The system identifies expense sources across compute, storage, networking, software licensing, and datacenter operations.

### Rate Cards

Providers and enterprises define standardized resource pricing through flexible rate card models. Parameters include:
- Compute (CPU, memory)
- Storage
- Network
- Guest OS type
- Tags and custom adjustments
- Latest VCF licensing models (cores, storage capacity)

Rate cards support chargeback and billing at organizational and regional levels.

### Showback

Showback reports resource consumption without actual billing, helping business units understand IT spending and encouraging responsible usage. IT admins can view, analyze, compare, and show back costs per user, VM, project, or organization.

### Chargeback

Chargeback enables itemized billing for tenants and business units based on actual consumption. Enhanced dashboards show costs across organizations, regions, projects, and namespaces. The system supports modern Infrastructure-as-a-Service billing models for both service providers and enterprise teams.

Features include:
- Real-time cost meters per tenant/business unit
- Invoice-ready consumption reporting
- Billing automation with scheduled invoices or on-demand generation
- Multi-tenancy with detailed breakdowns by projects, charges, and periods

### Cost Analysis and Optimization

- Metric comparisons (cost-to-run versus chargeback price)
- Rapid identification of high-cost areas
- Budget management with alerts at 80-100% thresholds
- What-if modeling for forecasting costs of new workloads or infrastructure
- Identification of cost-reduction opportunities: reclaiming unused VMs, snapshots, or oversized resources
- Quantification of potential and realized savings

### VCF Usage Meter

The Usage Meter is a separate metering and reporting appliance that measures actual consumption of VCF products for subscription billing, chargeback, and compliance reporting.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/09/10/finops-in-vmware-cloud-foundation/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/cost-and-capacity-management.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/design/design-library/vcf-automation-deployment-models(1)/chargeback-and-billing.html

---

## 7. Sovereign Cloud Features and Geo-Fencing

VCF 9 positions itself as the framework for building sovereign cloud infrastructure, addressing data sovereignty requirements that go beyond simple data residency (keeping data in a particular country).

### Four Pillars of Sovereignty in VCF

1. **In-Jurisdiction Operations** — Keeping data and management within legal borders. VCF enables organizations to maintain compute, storage, and networking within a dedicated, in-country footprint as the baseline for operational sovereignty.

2. **Deterministic Data Movement** — Knowing exactly where data goes and why. Data-residency tags and geo-fencing policies are part of every cluster specification, ensuring that workloads remain where they are intended.

3. **Cryptographic Authority** — Maintaining local control over encryption keys rather than trusting a third party. VCF integrates platform encryption with locally controlled Key Management Systems (KMS) and Hardware Security Modules (HSM).

4. **Audit-Grade Evidence** — Retaining proof needed to satisfy the highest levels of regulatory scrutiny. Centralized logging with immutable retention supports compliance verification.

### Technical Implementation

**Micro-Segmentation and VPCs**
VCF enables tightly governed enclaves using micro-segmentation and Virtual Private Clouds (VPCs) to restrict east-west movement and control egress paths.

**Admin Plane Security**
Management domain security features include scheduled password/certificate rotation and PAM-mediated, time-bound sessions for access control.

**Compliance Monitoring**
Continuous compliance checking against regulatory frameworks (CIS, DISA STIG, FISMA, HIPAA, ISO, PCI DSS, NIST SP 800-171, NIST SP 800-53 R5) with automated alerting and auto-correction capabilities.

**Configuration Compliance**
Security Operations Dashboard maps attack surfaces with real-time compliance scores. Configuration drift detection identifies and can auto-correct deviations from defined baselines.

### Global Fleet Management for Sovereignty

The 300ms RTT support for fleet-to-instance communication enables management of remote VCF Instances from a central fleet while keeping data and workloads local. This allows a sovereign model where governance is centralized but data never leaves its jurisdiction.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2026/02/18/private-cloud-adoption-why-true-data-sovereignty-demands-a-proven-framework/
- https://blogs.vmware.com/cloud-foundation/2025/11/18/the-great-cloud-charade-why-data-residency-isnt-data-sovereignty/
- https://blogs.vmware.com/cloud-foundation/2025/06/17/whats-new-in-vmware-cloud-foundation-9-0/
- https://virtuallywired.io/2026/03/08/vcf-9-0-pushes-the-boundaries-of-global-private-cloud/

---

## 8. The 6+1 Support Model and New Release Cadence

### Previous Model: 5+2

Under the prior lifecycle policy, major VCF releases received five years of general support, with the option to purchase up to two additional years of extended support.

### New Model: 6+1

Starting with VCF 9, major releases receive **six years of general support**, with the option to purchase **one additional year** of extended support. This represents one more year of standard support but one fewer year of extended support compared to the old model.

The estimated End of Service date for VCF 9.x is **June 17, 2031** (six years from the GA date).

### New Release Cadence

**Major releases:** Every **three years** (previously every two years).

**Minor releases:** Approximately every **nine months** (previously every six months). This maintains four minor releases per major version (e.g., 9.0, 9.1, 9.2, 9.3).

**Support windows by release type:**
- Initial minor releases receive 27 months of support each.
- The final minor release in a major version receives 45 months of support, providing flexibility for customers planning their upgrade path.
- Extended support (purchasable): up to one additional year beyond standard support.

**Maintenance releases:** Approximately every three months during initial support phases.

**Express patches:** Released per-component as needed.

**Hot patches:** Deployed as necessary for critical issues.

### Rationale

Broadcom stated these changes aim to deliver more predictable release dates, offer longer support periods, and provide more flexibility for customer upgrade schedules. The longer cadence acknowledges that enterprise customers need more time to plan and execute major upgrades.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/07/16/vmware-cloud-foundation-9-ushers-in-new-support-model-and-release-cadence/
- https://vinfrastructure.it/2025/07/vmware-vcf-9-new-support-and-release-model/

---

## 9. Terminology Changes

VCF 9 brings a sweeping set of naming changes as Broadcom unifies the platform identity. The following tables capture the key rebrands, replacements, and deprecations.

### Component Rebranding (Old Name to New Name)

| Old Name | New Name in VCF 9 |
|----------|-------------------|
| Cloud Builder | VCF Installer |
| SDDC Manager (as primary UI) | VCF Operations (SDDC Manager still exists as backend, UI deprecated) |
| Aria Operations | VCF Operations |
| Aria Operations for Logs | VCF Operations for Logs |
| Aria Operations for Networks | VCF Operations for Networks |
| Aria Automation | VCF Automation |
| Aria Suite Lifecycle (vRSLCM) | VCF Operations Fleet Management |
| VMware Cloud Director | VCF Automation (Tenant Manager layer) |
| ESXi | ESX (the "i" is dropped) |
| NSX Security | VMware vDefend |
| NSX Advanced Load Balancer | VMware Avi Load Balancer |
| VMware PowerCLI | VCF PowerCLI |

### Deprecated Features and Their Replacements

| Deprecated Feature | Replacement |
|-------------------|-------------|
| vCenter Enhanced Linked Mode (ELM) | VCF Operations (for multi-vCenter management) |
| vSphere Clustering Service (vCLS) VMs | Embedded ESXi/vCenter services (cluster services now in hypervisor) |
| vSphere Host Profiles | vSphere Configuration Profiles (cluster-level management as code) |
| Update Manager Download Service (UMDS) | VCF Download Tool / vLCM depot management |
| vSphere Virtual Volumes (vVols) | vSAN or VMFS (vVols limited to critical bug fixes only) |
| vSAN OSA Hybrid Mode | All-Flash vSAN only |
| ESXi Host Cache (Swap to SSD) | Memory Tiering with NVMe |
| Integrated Windows Authentication (IWA) | Identity Federation or LDAPS |
| vLCM Baselines | vLCM Images (single cluster image approach) |
| Deployment Parameters Workbook (Excel) | VCF Installer UI or JSON specifications |
| Auto Deploy (PXE-based stateless boot) | vLCM image-based deployment and lifecycle management |
| Aria Automation Pipelines (CI/CD) | Deprecated, no direct in-platform replacement |

### Versioning Convention Change

All user-facing version labels such as "Update 2a" or "U3b" have been eliminated. Starting with VCF 9.0, a unified five-field numeric format (X.Y.Z.AABB) is used across all components:
- X = Major version
- Y = Minor version
- Z = Maintenance release
- AA = Express patch (01xx-99xx)
- BB = Hot patch (xx01-xx99)

**Sources:**
- https://techietech.org/vcf-9-deprecated-redesigned-replaced-features-capabilites/
- https://knowledge.broadcom.com/external/article/410435/unified-vcf-product-releases-versioning.html
- https://blogs.vmware.com/cloud-foundation/2025/07/03/vcf-9-0-deployment-pathways/

---

## 10. The Version Jump from VCF 5.x to VCF 9.0

### Why Skip Versions 6, 7, and 8?

The jump from VCF 5.x directly to VCF 9.0 was a deliberate strategic decision to **unify version numbering across all components** in the VMware Cloud Foundation stack. VCF 9 ships with vSphere 9, ESX 9, vCenter 9, NSX 9, and vSAN 9. By aligning every building block to the same major version number, Broadcom removes historical confusion about which component versions are compatible and presents VCF as a single, cohesive solution.

Prior to VCF 9, the version numbers were disconnected:
- VCF 5.2 shipped with vSphere 8.0 U3, NSX 4.x, etc.
- There was no consistent relationship between the VCF version number and its constituent component versions.

By jumping to version 9, Broadcom established a new baseline where the platform version and all major component versions are aligned. This also signals that VCF 9 is not an incremental update but a platform reset — a fundamentally different architecture with VCF Operations as mandatory, a new deployment model, and a unified management paradigm.

### Unified Versioning Going Forward

From VCF 9.0 onward, all components in the VCF Bill of Materials use the same version convention and follow the same release cadence. The five-field format (X.Y.Z.AABB) applies to ESX, vCenter, vSAN, NSX, VMware Tools, VCF Operations, and other platform components. Some exceptions exist: vSphere Kubernetes Service and Supervisor Services may use semantic versioning 2.0.0 independently, and VMware Tools and Remote Console retain their existing formats during the 9.x cycle.

### Upgrade Paths to VCF 9

- **From VCF 5.x:** Direct upgrade supported. This is the primary upgrade path. All ESXi clusters must be converted from baseline management to vSphere Lifecycle Manager (vLCM) images before the ESXi host upgrade phase, as baselines are no longer supported.
- **From VCF 4.x:** Must first upgrade to VCF 5.x, then to 9.0.
- **From VCF 3.x:** Most complex path — requires 3.x to 4.x (including NSX-V to NSX-T migration), then 4.x to 5.x, then 5.x to 9.0.

### VCF 9.x Releases to Date

- **VCF 9.0** — GA June 17, 2025 (build 24755599)
- **VCF 9.0.1** — GA September 2025
- **VCF 9.0.2** — GA approximately January 2026

**Sources:**
- https://knowledge.broadcom.com/external/article/410435/unified-vcf-product-releases-versioning.html
- https://knowledge.broadcom.com/external/article/314608/correlating-vmware-cloud-foundation-vers.html
- https://blogs.vmware.com/cloud-foundation/2025/09/25/how-to-upgrade-to-vmware-cloud-foundation-9-0/
- https://blogs.vmware.com/cloud-foundation/2025/12/18/upgrading-vmware-cloud-foundation-5-2-to-9-0-the-top-10-questions-answered/

---

## Appendix: Key Reference URLs

### Official VMware/Broadcom Sources
- VCF 9 Launch Blog: https://blogs.vmware.com/cloud-foundation/2025/06/17/whats-new-in-vmware-cloud-foundation-9-0/
- VCF 9 Operations Blog: https://blogs.vmware.com/cloud-foundation/2025/06/17/modern-infrastructure-operations-vcf-9-0/
- VCF 9 Deployment Pathways: https://blogs.vmware.com/cloud-foundation/2025/07/03/vcf-9-0-deployment-pathways/
- VCF 9 Planning Blog: https://blogs.vmware.com/cloud-foundation/2025/07/28/planning-a-successful-vmware-cloud-foundation-9-0-deployment/
- VCF 9 Support Model Blog: https://blogs.vmware.com/cloud-foundation/2025/07/16/vmware-cloud-foundation-9-ushers-in-new-support-model-and-release-cadence/
- VCF 9 FinOps Blog: https://blogs.vmware.com/cloud-foundation/2025/09/10/finops-in-vmware-cloud-foundation/
- VCF 9 for Service Providers: https://blogs.vmware.com/cloudprovider/2025/06/vmware-cloud-foundation-9-0-whats-in-it-for-the-vcsps.html
- VCF 9 Sovereign Cloud Blog: https://blogs.vmware.com/cloud-foundation/2026/02/18/private-cloud-adoption-why-true-data-sovereignty-demands-a-proven-framework/
- VCF 9 vSphere What's New: https://blogs.vmware.com/cloud-foundation/2025/06/23/vsphere-in-vcf-9-0-whats-new/

### Official Broadcom Documentation
- VCF 9.0 Release Notes: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes.html
- VCF 9.0 Overview: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0.html
- VCF Automation Overview: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/overview-of-vmware-cloud-foundation-9/what-is-vmware-cloud-foundation-and-vmware-vsphere-foundation/vcf-automation-overview.html
- Fleet Management: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/overview-of-vmware-cloud-foundation-9/what-is-vmware-cloud-foundation-and-vmware-vsphere-foundation/vcf-operations-overview/fleet-management.html
- Fleet Deployment Models: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/design/vmware-cloud-foundation-concepts/vcf-operations-deployment-models.html
- Unified Versioning: https://knowledge.broadcom.com/external/article/410435/unified-vcf-product-releases-versioning.html
- Cost and Capacity Management: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/cost-and-capacity-management.html
- Chargeback and Billing Design: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/design/design-library/vcf-automation-deployment-models(1)/chargeback-and-billing.html

### Community and Third-Party Analysis
- VCD to VCF Automation Transition: https://vmtechie.blog/2025/07/21/navigating-the-shift-from-vmware-cloud-director-to-vcf-automation-in-vmware-cloud-foundation-9/
- VCF Automation for VCD Admins: https://fojta.wordpress.com/2025/06/17/quck-guide-to-vcf-automation-for-vcd-administrators/
- Deprecated Features List: https://techietech.org/vcf-9-deprecated-redesigned-replaced-features-capabilites/
- SDDC Manager to VCF Operations Transition: https://my-cloudy-world.com/2025/07/03/vmware-cloud-foundation-9-0-transitioning-from-sddc-manager-to-vcf-operations/
- Fleet Mental Models (3-part series): https://digitalthoughtdisruption.com/2026/02/16/vcf-9-0-ga-mental-model-part-1-fleets-instances-domains/
- VCF 9 Architecture Guide (Veeam): https://community.veeam.com/blogs-and-podcasts-57/how-vcf-9-0-architecture-works-a-practical-guide-10776
