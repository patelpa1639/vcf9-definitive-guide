# VCF 9 Lifecycle Operations and Day-2 Management

## Research Document for VMware Cloud Foundation 9 Technical Book

---

## 1. Bill of Materials (BOM) Model

### What the BOM Is

The Bill of Materials in VMware Cloud Foundation is a curated manifest of all software components and their validated version numbers that ship together in a given VCF release. Every VCF release -- whether major, minor, or maintenance -- publishes a BOM that lists the exact builds of ESXi, vCenter, NSX, SDDC Manager, VCF Operations, VCF Automation, and every other bundled component. The BOM is the authoritative source of truth for which component versions have been tested and certified to work together.

### How the BOM Ensures Compatibility

Because all components listed in a BOM have been subjected to integrated testing by Broadcom engineering, deploying the BOM versions together eliminates the risk of version-mismatch failures. Third-party ecosystem products (OEM driver add-ons, ESXi device drivers, UI plugins) maintain their own dependencies at the component level, but VCF core and management components are guaranteed interoperable when they match the published BOM.

### VCF 9.0 GA BOM (Selected Components)

| Component | Version |
|---|---|
| VCF Installer | 9.0.2.0 |
| VMware ESX | 9.0.0.0 |
| VMware vCenter | 9.0.0.0 |
| VMware NSX | 9.0.0.0 |
| SDDC Manager | 9.0.0.0 |
| VCF Operations | 9.0.0.0 |
| VCF Operations Orchestrator | 9.0.0.0 |
| VCF Operations for Logs | 9.0.0.0 |
| VCF Operations for Networks | 9.0.0.0 |
| VCF Automation | 9.0.0.0 |
| VCF Identity Broker | 9.0.0.0 |
| VMware vSphere Supervisor | 9.0.0.0 |
| VCF Operations Fleet Management | 9.0.0.0 |
| VCF Operations HCX | 9.0.0.0 |
| Kubernetes Backup and Recovery Service | 1.8.0 |
| VMware vSphere Kubernetes Service | 3.3.1 |
| VMware Remote Console | 13.0.0.0 |
| VMware Tools Async Release | 13.0.0.0 |

### Where to Find the BOM

The official BOM for each release is published on the Broadcom TechDocs portal under the corresponding release notes. For VCF 9.0 the canonical location is the "VMware Cloud Foundation Bill of Materials" page within the 9.0 release notes section.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/vmware-cloud-foundation-bill-of-materials.html
- https://www.aroracloud.com/blog/upgrading-vmware-cloud-foundation-leverage-the-flexible-bill-of-materials-bom-for-seamless-patch-management/

---

## 2. VCF Maintenance Releases -- 9.0.1 and 9.0.2

### Nature of Maintenance Releases

Maintenance releases sit between minor releases in the VCF versioning scheme. They deliver bug and security fixes, hardware enablement changes (new server, CPU, device, and driver support), guest OS updates, and backward-compatible features. They are intentionally limited in scope to reduce operational risk.

### VCF 9.0.1 (Released September 29, 2025)

VCF 9.0.1 was the first maintenance release for the 9.0 line. Key highlights include:

- **Convergence enhancement:** VCF Installer gained the ability to converge non-VCF deployments running NSX 4.1.0.2 or later into a management domain, widening the brownfield adoption path.
- **NSX certificate pre-check:** NSX 9.0.1 introduced an upgrade precheck that validates transport-node SSL certificates are neither expired nor within 90 days of expiration, preventing upgrade failures caused by stale certificates.
- **Lab and PoC improvements:** Several enhancements targeted proof-of-concept and lab environments, reducing the minimum hardware footprint.
- **Bypass options for lab use:** VCF 9.0.1 added the ability to bypass the vSAN ESA HCL check and the host-commission 10 GbE NIC check, making it easier to deploy in non-production settings.
- **Component updates:** Includes updated builds of vCenter, ESXi, vSAN, NSX, VCF Installer, VCF Operations, and VCF Automation, each with their own bug fixes documented in component-specific release notes.

### VCF 9.0.2 (Released January 20, 2026)

VCF 9.0.2 continued the stability and supportability focus:

- **VCF Operations SMTP OAuth 2.0:** The SMTP notification plug-in now supports OAuth 2.0 authentication when sending email notifications through Microsoft 365, eliminating the need for less-secure app-specific passwords.
- **Hardened lifecycle workflows:** Stronger pre-check validation and improved state consistency compared to earlier 9.0.x builds, making 9.0.2 a recommended baseline for production environments.
- **Component updates:** Updated builds across the same seven core and management components as 9.0.1.
- **Selective patching preserved:** Administrators can continue to pick and choose which component updates to apply based on relevance to their environment.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-9-0-1-release-notes.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-9-0-2-release-notes.html
- https://vinfrastructure.it/2025/09/vmware-cloud-foundation-9-0-1-ga/
- https://gibsonvirt.com/2026/01/23/vcf-9-0-2-upgrade-patching-process/
- https://williamlam.com/2025/09/enhancement-in-vcf-9-0-1-to-bypass-vsan-esa-hcl-host-commission-10gbe-nic-check.html

---

## 3. Component-Level Patching

### How It Differs from Previous VCF Lifecycle Management

In earlier VCF versions (4.x and early 5.x), lifecycle updates were largely monolithic -- when a new BOM was released, administrators were expected to apply all component updates together. VCF 9 introduces a fundamentally different approach.

### Selective Component Updates

Starting with VCF 9, maintenance releases and patch releases allow administrators to select individual components for updating. If a critical vCenter bug needs to be fixed immediately, there is no requirement to simultaneously patch ESXi or NSX. This per-component granularity reduces maintenance-window scope and operational risk.

### Release Type Distinctions

VCF 9 defines four release types with different patching behaviors:

1. **Major releases** (e.g., 9.0.0.0 to 10.0.0.0): Full-stack synchronized updates; all components must be updated. May contain breaking changes.
2. **Minor releases** (e.g., 9.0 to 9.1): Feature additions within a major version; fully synchronized across all components.
3. **Maintenance releases** (e.g., 9.0.1.0): Scheduled, cumulative updates focused on stability. Component-selective -- administrators choose which components to apply.
4. **Patch releases** (e.g., 9.0.1.0100): Time-sensitive security or critical-fix releases. Component-selective and not bound to a full BOM update.

### Live Patching

VCF 9 dramatically expands the scope of live patching for ESXi hosts. The Lifecycle Manager automatically determines whether a given patch is live-patchable or requires a traditional reboot workflow. Components eligible for live patching include:

- The vmkernel
- User-space daemons
- NSX data-plane components on the host
- The virtual machine execution runtime (vmx)

Broadcom states that roughly 80 percent of ESXi host fixes in VCF 9.0 can be installed with no VM downtime. During live patching, hosts enter a partial maintenance mode. For patches targeting the vmx runtime, VMs undergo a fast-suspend-resume (FSR) operation. In VCF 9.0, FSR performance for vGPU-enabled VMs was significantly improved, allowing AI/ML workloads to be live-patched with minimal disruption.

Certain updates still require traditional reboots -- driver updates, hardware-dependent modules, storage controller firmware, and NIC firmware fall into this category. VMs using Fault Tolerance, DirectPath I/O, or other unsupported configurations cannot participate in FSR.

### Retirement of Update Manager

VCF 9.0 retires the legacy vSphere Update Manager in favor of the more capable vSphere Lifecycle Manager (vLCM). vLCM enforces image-based configuration management, detects configuration drift, and can interact with hardware managers to patch system firmware.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/lifecycle-management/lifecycle-management-in-vmware-cloud-foundation.html
- https://www.vrcloud24x7.com/2025/12/live-patching-in-vmware-cloud_10.html
- https://blogs.vmware.com/cloud-foundation/2025/07/15/live-patch-gets-even-better-in-vsphere-with-vmware-cloud-foundation-9-0/
- https://williamlam.com/2025/07/applying-1st-esx-live-patch-using-vcf-9-0-operations.html
- https://securityboulevard.com/2025/08/vcf-9-0-live-patching-ends-maintenance-window-dread/

---

## 4. Upgrading VCF 5.x to 9.0

### Supported Upgrade Paths

- VCF 5.0 and later can upgrade directly to VCF 9.0 (skip-level upgrade is supported).
- VCF 4.x environments must first upgrade to VCF 5.2 before proceeding to 9.0.
- Both the VCF Operations console and the SDDC Manager UI can be used to orchestrate the upgrade.

### Prerequisites

1. **Environmental review:** Validate existing software versions and component configurations. Review the Planning and Preparation Workbook from Broadcom documentation.
2. **Hardware compatibility:** Verify that all hosts and devices appear on the VCF compatibility list; the precheck will flag unsupported hardware.
3. **Aria component versions:** If Aria Automation or Aria Operations are deployed, they must be at version 8.18 before the VCF 9.0 upgrade can proceed. This may require intermediate upgrades.
4. **VCF Operations mandatory:** VCF Operations is a mandatory component in VCF 9.0. If it was not previously deployed, it must be installed during or immediately after the upgrade.
5. **Aria Operations for Logs:** There is no in-place upgrade path from VMware Aria Operations for Logs 8.x to 9.0. A fresh installation is required.
6. **Temporary IP addresses:** Temporary IPs are needed for vCenter servers during the upgrade.
7. **Certificate and password validity:** All certificates and service-account passwords must be valid and not near expiration.
8. **Run SDDC Manager prechecks:** Execute the upgrade prerequisite check to surface and resolve any blocking issues before starting.

### Upgrade Sequence

The upgrade follows a strict ordering of roughly 20 phases:

**Phase 1 -- Management Components:**
1. VCF Download Tool (VCFDT)
2. VMware Aria Suite Lifecycle (must be at 8.18 Patch 2 minimum)
3. VCF Operations and Fleet Management appliance
4. VCF Automation, VCF Operations for Networks, VCF Operations for Logs
5. Additional management tools (SRM, HCX, AVI, etc.)

**Phase 2 -- Core VCF Components (Management Domain first):**
1. SDDC Manager (select version 9.0 in Lifecycle Management, run precheck, then apply)
2. NSX (build upgrade plan, validate with precheck, execute)
3. vCenter (uses reduced-downtime upgrade strategy)
4. ESXi hosts (migrate from vSphere Lifecycle Manager Baselines to Images)
5. vSAN (on-disk format, VMFS, file services)

**Phase 3 -- Post-Upgrade Finalization:**
1. Deploy VCF Identity Broker (requires VCF Operations, SDDC Manager, and management-domain vCenter all at 9.0)
2. Upgrade vSphere Distributed Switches
3. Update vSAN filesystem versions
4. Configure licensing
5. Upgrade VI workload domains
6. Update third-party components

### Workload Impact

- Management-domain upgrades affect management services temporarily. vCenter undergoes a reduced-downtime upgrade.
- ESXi host upgrades can leverage live patching for compatible patches, but some updates require hosts to enter maintenance mode with VM migration (vMotion).
- VI workload domains can be upgraded independently and at a later time, giving administrators control over when tenant workloads are affected.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/09/25/how-to-upgrade-to-vmware-cloud-foundation-9-0/
- https://blogs.vmware.com/cloud-foundation/2025/12/18/upgrading-vmware-cloud-foundation-5-2-to-9-0-the-top-10-questions-answered/
- https://knowledge.broadcom.com/external/article/390634/update-sequence-for-vcf-90-and-compatibl.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/lifecycle-management/lifecycle-management-of-vcf-core-components/upgrade-workload-domains-to-vcf-5-2.html
- https://blog.leaha.co.uk/2025/08/14/vcf-9-ultimate-upgrade-guide/

---

## 5. Converging Existing vSphere Environments to VCF 9

### What Convergence Means

Convergence is the process of taking an existing vSphere deployment -- one that was never built as VCF -- and bringing it under VCF management. This transforms a collection of individually managed silos into a unified private cloud with standardized lifecycle management, identity federation, and operational observability.

### Two Convergence Pathways

**Path 1 -- Convert to a new VCF fleet (VCF Installer):**
Used when no VCF instance exists yet. The VCF Installer is used to specify deployment information for the existing environment, and any missing VCF software components (SDDC Manager, NSX, VCF Operations, etc.) are automatically deployed and configured. This path creates a brand-new VCF fleet with the converged environment as its management domain.

**Path 2 -- Import into an existing VCF fleet (VCF Operations):**
Used when a VCF instance already exists and the administrator wants to add a vSphere environment as an additional VI workload domain. The import is performed through VCF Operations and includes comprehensive validation of component configurations.

### Supported Convergence Scenarios

Five officially supported scenarios describe what existing components can be present:

1. vCenter instance and ESXi hosts only
2. vCenter, ESXi, and VMware Aria Operations
3. vCenter, ESXi, Aria Suite Lifecycle, and Aria Operations
4. vCenter, ESXi, Aria Suite Lifecycle, and Aria Automation
5. vCenter, ESXi, Aria Suite Lifecycle, Aria Operations, and Aria Automation

In VCF 9.0.1 and later, environments running NSX 4.1.0.2 or newer can also be converged.

### Any-Storage Support

A major enhancement in VCF 9 is support for storage types beyond vSAN during convergence. While greenfield VCF deployments still require vSAN as principal storage, converging an existing vSphere environment allows the following storage types to be brought in as principal storage:

- vSAN (ESA and OSA)
- iSCSI
- NFS v4.1
- NVMe over Fabrics (FC or TCP)
- Fibre Channel

This dramatically broadens the addressable market for VCF by accommodating organizations that have invested in external storage arrays.

### Validation During Convergence

The convergence process includes comprehensive pre-checks covering hardware requirements, network configurations, switch versions, and default settings that must match VCF recommended configurations. Administrators may need to remediate configuration gaps before the import can proceed.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2026/02/05/how-to-converge-a-vmware-vsphere-environment-to-vmware-cloud-foundation-9-0/
- https://blogs.vmware.com/cloud-foundation/2025/11/11/vmware-cloud-foundation-9-now-ready-for-all-storage/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/converging-your-existing-vsphere-infrastructure-to-a-vcf-or-vvf-platform-.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/converging-your-existing-vsphere-infrastructure-to-a-vcf-or-vvf-platform-/supported-scenarios-to-converge-to-vcf.html
- https://cormachogan.com/2025/08/21/support-for-iscsi-in-vmware-cloud-foundation-9-0/

---

## 6. Pre-Check Framework

### Purpose

The pre-check framework validates that the VCF environment is ready for an upgrade, deployment, or operational change. Running prechecks before making changes is a strongly recommended best practice that catches issues when they are easy to fix rather than mid-upgrade.

### How to Trigger Pre-Checks

Pre-checks can be triggered in several ways:

- **SDDC Manager UI:** Navigate to Lifecycle Management, select the target version, and click the precheck button. When an upgrade plan is created, a precheck runs automatically for the entire system and for each individual component.
- **VCF Operations Console:** For management-component upgrades orchestrated through VCF Operations.
- **SoS Utility (CLI):** SSH into the SDDC Manager appliance and run SoS health-check commands for on-demand validation of connectivity, compute, storage, databases, workload domains, and network services.

### Pre-Check Categories

Pre-checks span multiple categories:

- **Hardware compatibility:** Validates hosts and devices against the VCF compatibility list.
- **Software prerequisites:** Verifies that upgrade bundles are downloaded, temporary IP addresses are available, certificates and passwords are valid, and the vSAN hardware compatibility database is up to date.
- **NSX readiness:** Validates transport-node SSL certificate expiration (introduced in 9.0.1 -- flags certs expiring within 90 days).
- **VCF Operations version:** SDDC Manager 9.0 includes a precheck that blocks core-component upgrades if VCF Operations is not yet at version 9.0.
- **Network validation:** Checks link speeds (e.g., the 10 GbE NIC check for management interfaces in greenfield deployments).
- **Connectivity:** Validates that all required management endpoints are reachable.

### Reading Results -- Error vs. Warning

Pre-check results use a traffic-light severity model:

| Severity | Meaning |
|---|---|
| **Error (Red / Critical)** | A blocking issue that must be resolved before the operation can proceed. The upgrade or deployment will not start until all errors are cleared. |
| **Warning (Yellow)** | A non-blocking issue that warrants attention. The operation can proceed, but the administrator should evaluate whether remediation is advisable. |
| **Pass (Green)** | The check passed with no issues. |

Warnings can be silenced if the administrator has confirmed they are false positives (for example, lab environments that intentionally do not meet production hardware requirements). Silenced prechecks do not block upgrades, but administrators should exercise caution and only silence alerts for known-safe conditions.

When errors are detected, SDDC Manager provides detailed instructions on how to resolve each issue, often linking to relevant Broadcom Knowledge Base articles.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/upgrading-cloud-foundation/upgrade-the-management-domain-to-vmware-cloud-foundation-5-2/perform-upgrade-precheck.html
- https://knowledge.broadcom.com/external/article/382585/sddc-manager-performs-prechecks-to-valid.html
- https://williamlam.com/2025/06/disable-10gbe-nic-pre-check-in-the-vcf-9-0-installer.html
- https://williamlam.com/2025/09/enhancement-in-vcf-9-0-1-to-bypass-vsan-esa-hcl-host-commission-10gbe-nic-check.html

---

## 7. Fleet-Wide Upgrade Orchestration

### The Fleet Model

VCF 9 introduced the concept of a fleet -- a collection of one or more VCF instances managed by a single set of fleet-level management components. A fleet has exactly one instance of VCF Operations and one instance of VCF Automation, which provide centralized oversight for all workload domains across all instances.

### Shift from SDDC Manager to Fleet Manager

In VCF 5.x and earlier, SDDC Manager was the primary orchestration point for lifecycle operations, and Aria Suite Lifecycle Manager (ASLCM) handled management-component updates. In VCF 9, lifecycle ownership shifts substantially to Fleet Manager, which is integrated into VCF Operations. ASLCM becomes a secondary tool, though it must be upgraded to at least 8.18 Patch 6 during the transition period.

### How Fleet-Wide Orchestration Works

Fleet Manager provides a single pane of glass for:

- **Lifecycle management:** Orchestrates patching and upgrades across all VCF instances in the fleet. The fleet management appliance is updated first, followed by VCF Operations instances, then remaining management components (Logs, Automation, Identity Broker, Networks).
- **Health monitoring:** Fleet-wide health dashboards aggregate the status of every instance and workload domain.
- **Certificate management:** Centralized certificate issuance, tracking, and auto-renewal across the fleet.
- **Identity federation:** Single sign-on configuration propagated across instances.
- **License management:** Unified license tracking and compliance.

### Update Sequence Across a Fleet

When performing a maintenance or minor release update across the fleet:

**Management components (fleet-level, updated once for the whole fleet):**
1. VCF Operations Fleet Management appliance
2. VCF Operations instance
3. VCF Operations for Logs, VCF Automation, VCF Identity Broker, VCF Operations for Networks

**Core components (per-instance, per-domain):**
1. SDDC Manager
2. NSX
3. vCenter
4. ESXi hosts
5. vSAN

Each VCF instance's management domain is upgraded first, followed by its VI workload domains. Dependency awareness is built into the workflow so that components are not updated out of order.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/design/vmware-cloud-foundation-concepts/vcf-operations-deployment-models.html
- https://dmware.nl/2026/01/vmware-cloud-foundation-9-0-2-vcf-9-x-upgrade-lifecycle-fleet-manager-explained/
- https://blogs.vmware.com/cloud-foundation/2025/07/28/planning-a-successful-vmware-cloud-foundation-9-0-deployment/

---

## 8. Log Locations for VCF Components

### VCF Installer

| Log | Path |
|---|---|
| Domain Manager (bringup) | `/var/log/vmware/vcf/domainmanager/domainmanager.log` (on the VCF Installer appliance) |
| ESXi vSAN prep, DNS config, VM network port group creation | Same domainmanager.log on the Installer appliance |

### SDDC Manager

| Log | Path |
|---|---|
| Domain Manager operational logs | `/var/log/vmware/vcf/domainmanager/domainmanager.log` |
| SoS health-check output | `/var/log/vmware/vcf/sddc-support/<timestamp>/` |
| API logs | Collected via SoS with the `API-LOGS` option |

### VCF Fleet Management

| Log | Path |
|---|---|
| Lifecycle Manager (vRLCM) log | `/var/log/vrlcm/vmware_vrlcm.log` |
| Fleet management general logs | `/var/log/vrlcm/` |
| VCF Adapter and inventory sync | `/var/log/vrlcm/` |

### VCF Operations

| Log | Path |
|---|---|
| Support logs (UI) | Administration > Control Panel > Support Logs > Collector and adapters > VMwareAdapter |
| Support bundle generation | Administration > Control Panel > Support Bundle |

### vCenter Server

| Log | Path |
|---|---|
| vpxd (core vCenter service) | `/var/log/vmware/vpxd/vpxd.log` |
| vCenter general logs | `/var/log/vmware/` |
| Support bundle | Generated via GenerateLogBundles_Task API or UI |

### ESXi

| Log | Path |
|---|---|
| vmkernel log | `/var/log/vmkernel.log` |
| hostd | `/var/log/hostd.log` |
| vpxa (vCenter agent) | `/var/log/vpxa.log` |
| Support bundle | Generated from DCUI or via esxcli |

### NSX Manager

| Log | Path |
|---|---|
| NSX support bundle | Generated with DEFAULT filter (5-day age limit, excludes core dumps) |
| Syslog | `/var/log/syslog` |
| Proton service | `/var/log/proton/nsxapi.log` |

### NSX Edge

| Log | Path |
|---|---|
| Data-plane logs | `/var/log/syslog` |
| Edge support bundle | Collected via NSX Manager or CLI |

### vSAN

| Log | Path |
|---|---|
| vSAN health service | Logs accessible through vCenter vSAN Health UI or `/var/log/vmware/vsan-health/` |
| vSAN object logs | Integrated into ESXi vmkernel and vobd logs |

### VCF Automation (VCFA)

| Log | Path |
|---|---|
| VCFA deployment logs | `/var/log/vrlcm/` on the Fleet Management appliance |
| VCFA application logs | Accessible through the VCF Automation UI or on the VCFA appliance under `/var/log/vmware/` |

### SoS Utility Collection Options

The SoS utility on SDDC Manager can collect logs for the following categories: `HEALTH-CHECK`, `SDDC-MANAGER`, `NSX_MANAGER`, `API-LOGS`, `ESX`, `VMS_SCREENSHOT`, `VCENTER-SERVER`, and `VCF-SUMMARY`.

**Sources:**
- https://www.virtualbug.in/post/exploring-log-locations-in-vcf-9-where-to-find-what
- https://www.viquarcloud.com/post/vmware-cloud-foundation-9-installation-troubleshooting-know-where-to-look
- https://knowledge.broadcom.com/external/article/402476/collecting-vcf-installer-logs.html
- https://vcdx181.com/2026/02/13/vcf-9-day-2-installing-vcf-operations-for-logs/

---

## 9. Observability Workbench

### What It Is

The Observability Workbench is a diagnostic and analysis feature within VCF Operations that provides a unified interface for investigating infrastructure and application issues. It brings together logs, metrics, and network flow data into a single console, enabling operators to correlate events across different VCF components.

### Core Capabilities

**Logs Search:**
Operators can parse, modify, process, and analyze logs collected from VCF components using an advanced set of predefined search fields. Log formatting has been standardized across components, improving consistency and enabling automated analysis.

**Metrics Engine:**
Provides rapid object-metric searches with auto-completion and the ability to compose multi-metric conditions. This allows operators to quickly narrow down performance anomalies without manually navigating through dozens of dashboards.

**Flow Insights:**
Network analysis that surfaces flow volume, traffic rates, session counts, and TCP round-trip time anomalies. This integrates data from NSX to give visibility into east-west and north-south traffic patterns.

### Integration Points

- **VCF Operations for Logs:** Provides the log ingestion and indexing backend that the Observability Workbench queries.
- **VCF Operations for Networks:** Supplies network flow and topology data.
- **NSX:** Provides transport-node, edge-cluster, and VPC deployment metrics.
- **vSAN:** Cluster health, IOPS, throughput, and latency metrics are surfaced.
- **Kubernetes/Tanzu:** For environments running vSphere Supervisor and TKG, the workbench can correlate Istio service-mesh traffic, Prometheus metrics, and infrastructure health.
- **Diagnostic Findings:** The workbench integrates with the Diagnostics engine, which scans and evaluates signatures to identify active problems and security risks (CVEs, VMSAs).

### Operational Use Cases

- Investigating unresponsive ESXi hosts
- Diagnosing NTP synchronization drift between hosts and vCenter
- Troubleshooting DNS resolution failures (forward and reverse lookup)
- Analyzing VM operation failures (OVF deployment, power operations, vMotion, snapshots)
- Monitoring application-level health (e.g., Oracle database connectivity)
- Capacity planning and cross-tier application topology analysis

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/08/19/how-vmware-cloud-foundation-9-simplifies-troubleshooting/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/overview-of-vmware-cloud-foundation-9/what-is-vmware-cloud-foundation-and-vmware-vsphere-foundation/vcf-operations-overview/workload-monitoring-and-observability.html
- https://blogs.vmware.com/cloud-foundation/2026/02/06/why-vcf-9-0-improves-it-operations-and-management/

---

## 10. SupportAssist and Log Assist Bundles

### Log Assist Overview

Log Assist is a feature in VCF Operations that streamlines the process of generating log bundles and attaching them to Broadcom support cases. It replaces the previous manual process of collecting logs, compressing them, and uploading via FTP.

### How to Collect Log Bundles

1. Navigate to the Diagnostics Findings tab in VCF Operations (also accessible via Administration > Control Panel > Log Assist).
2. Select the inventory objects for which logs are needed.
3. Log Assist performs connectivity checks before generation:
   - Validates environment connectivity to the assigned collector
   - Confirms collector connectivity to selected components
   - Verifies environment access to the Broadcom Support Portal
   - Checks that integration permissions are sufficient
4. Generate the bundle. Each unified collector supports up to five parallel support bundles simultaneously; additional requests are queued automatically.
5. Upload the bundle directly to the Broadcom support case -- no external FTP site required.

### What Is Included in Log Bundles (by Component)

| Component | Bundle Contents |
|---|---|
| VCF Operations | LITE support bundle containing 24 hours of history |
| vCenter | Standard bundle generated via GenerateLogBundles_Task |
| ESXi | Standard bundle from DCUI |
| SDDC Manager | API logs and Manager logs (manifests excluded) |
| NSX | DEFAULT filter with a 5-day age limit; core dumps excluded |
| VCF Operations Fleet | API logs and Fleet logs with summary report enabled |

### Diagnostic Findings Attachment

Log Assist automatically enriches bundles with property-based and log-based diagnostic findings that are active at the time of generation. Administrators can also manually add findings from the Refresh Findings workflow before uploading.

### Practical Considerations

- Upload speed depends on uplink bandwidth between the collector and the Broadcom Support Portal.
- No external FTP clients or staging areas are required.
- The direct-transfer mechanism is encrypted, providing a secure channel for potentially sensitive log data.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/infrastructure-operations/using-vcf-operations-diagnostics/use-log-assist-to-accelerate-your-support-cases.html
- https://www.brockpeterson.com/post/vcf-operations-9-log-assist
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/infrastructure-operations/-configuring-administration-settings/create-a-support-bundle.html
- https://knowledge.broadcom.com/external/article/408042/generate-a-support-bundle-for-vcf-fleet.html

---

## 11. Certificate Management

### Overview

VCF 9 significantly improves certificate management by centralizing it within VCF Operations, supporting multiple certificate authorities, and introducing automatic certificate renewal. Certificates can be viewed, managed, and renewed for all VCF components from a single console.

### Supported Certificate Authorities

VCF 9 supports three categories of certificate authority:

**1. Microsoft Active Directory Certificate Services (Microsoft CA):**
Organizations can integrate with their existing Microsoft CA infrastructure to issue signed certificates for all management components (VCF Operations, VCF Automation, vCenter Server, NSX Manager, SDDC Manager) and infrastructure components. Certificates issued by a Microsoft CA are automatically renewed before expiration.

**2. VMware-Native Certificate Authorities:**
- **Fleet Management appliance CA:** The new Fleet Management appliance acts as a certificate authority for VCF management components. If left at its default configuration, management components use this CA and benefit from auto-renewal.
- **VMCA (vCenter Embedded CA):** Automatically renews certificates for infrastructure components and ESXi hosts.
- **SDDC Manager embedded OpenSSL CA:** Can issue certificates for infrastructure components (but not management components). Supports auto-renewal.

**3. External / Custom CA:**
For organizations that prefer to use their own PKI, VCF supports generating a Certificate Signing Request (CSR), having it signed by an external CA, importing the signed certificate, and replacing the existing certificate on the target component. External CA certificates are not auto-renewed; administrators must manage the renewal lifecycle manually.

### Automatic Certificate Renewal

Auto-renewal is one of the headline features of VCF 9 certificate management:

- Activated via a toggle in the VCF Operations UI.
- Covers management components, infrastructure components, and ESXi host certificates.
- Works with Microsoft CA and all VMware-native CAs.
- Non-disruptive certificate replacement workflow for key components like vCenter Server minimizes impact to management interfaces during renewal.
- VCF Operations includes alerts for upcoming certificate expirations, providing visibility even when auto-renewal is active.

### Important Considerations

- When using VMware-native CAs, administrators must add the applicable CA certificates to client systems and configure them as trusted to avoid browser security warnings.
- Certificate Authority configuration is performed separately for fleet-level management components and instance-level infrastructure components.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/06/19/automatic-certificate-renewal-in-vcf-9/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/fleet-management/certificate-management-9-0.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/fleet-management/certificate-management-9-0/configure-a-certificate-authority_9-0.html
- https://community.veeam.com/blogs-and-podcasts-57/vcf-9-0-certificate-management-improvement-10791
- https://datareload.com/vcf-certificate-authority-integration/

---

## 12. Common Operational Issues and Resolutions

### Infrastructure Health Issues

**Unresponsive ESXi hosts:**
VCF Operations Diagnostics detects hosts that have stopped responding to management traffic. Resolution typically involves checking host hardware health (iLO/iDRAC), verifying management network connectivity, and restarting the hostd or vpxa services if the host is physically healthy.

**NTP synchronization drift:**
The diagnostics engine flags NTP drift between ESXi hosts and vCenter. This can cause certificate validation failures, authentication errors, and log-correlation issues. Resolution involves verifying NTP server configuration on all hosts and the vCenter appliance, then restarting the NTP daemon.

**DNS resolution failures:**
Forward and reverse lookup failures are flagged by prechecks and ongoing health monitoring. Resolution requires correcting DNS records and ensuring all management components can resolve both forward and reverse entries for every VCF endpoint.

### Upgrade and Lifecycle Issues

**Upgrade bundle validation failures:**
Manifests as "Bundle validation for bundle of type HOST...failed due to error Failed uploading the update/upgrade patch files to VUM." Typically resolved by verifying network connectivity between SDDC Manager and the ESXi hosts, ensuring sufficient disk space, and re-downloading the bundle.

**VCF Operations version precheck failure:**
SDDC Manager 9.0 blocks core-component upgrades if VCF Operations is below 9.0. Resolution is to upgrade VCF Operations first (or deploy it if absent).

**NSX version false-positive warning:**
Under certain conditions, the precheck falsely flags the NSX version as unsupported when upgrading to VCF Automation 9.0. This is a known issue and the warning can be silenced after confirming the NSX version is actually supported.

**Expired transport-node SSL certificates:**
Introduced as a precheck in VCF 9.0.1. If transport-node certificates are expired or within 90 days of expiration, the precheck blocks the upgrade. Resolution is to renew the certificates before proceeding.

### Storage Issues

**vSAN NFS server memory leak (UDP):**
A known issue in VCF 9.0 where the NFS server leaks memory when clients use the UDP protocol. The workaround is to configure NFS clients to use TCP instead of UDP.

### Application and Workload Issues

**VM operation failures:**
OVF deployment, power operations, vMotion, and snapshot operations can fail due to underlying infrastructure problems. The Diagnostics engine in VCF Operations provides root-cause analysis and links to relevant KB articles.

**vSphere Lifecycle Manager compliance check failure with DPUs:**
On ESXi hosts with Data Processing Units (DPUs), vLCM compliance checks can fail when images contain removed components. Workaround is to restart the vmware-vlcm service on the affected host.

### Networking Issues

**NSX edge deployment failures:**
Can occur during initial deployment or scale-out operations. Workaround involves restarting nsx-proxy on both the ESXi host and the edge node.

**VPC creation failures with Avi Load Balancer:**
When DHCP is not configured in supervisors using NSX VPC with Avi Load Balancer, VPC creation fails. Resolution is to configure DHCP in the service profile before creating VPCs.

### Troubleshooting Tools Available

- **VCF Operations Diagnostics:** Centralized platform that monitors operational status and suggests remediations through KB articles with supporting performance graphs.
- **SoS Utility:** CLI-based health checks for connectivity, compute, storage, database, workload domains, and networks. Uses green/yellow/red severity indicators.
- **Log Assist:** Generates and uploads diagnostic bundles to Broadcom support.
- **Observability Workbench:** Correlates logs, metrics, and flows for deep-dive analysis.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/08/19/how-vmware-cloud-foundation-9-simplifies-troubleshooting/
- https://knowledge.broadcom.com/external/article/415800/vmware-cloud-foundation-vcf-known-issues.html
- https://blogs.vmware.com/cloud-foundation/2026/01/21/diagnostics-for-vmware-cloud-foundation-operations-newest-findings-2/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/deployment/deploying-a-new-vmware-cloud-foundation-or-vmware-vsphere-foundation-private-cloud-/troubleshooting-vmware-cloud-foundation-or-vmware-vsphere-foundation-deployments.html

---

## 13. VCF 9 Support Lifecycle Timelines

### New Support Model -- 6+1

Broadcom replaced the previous 5+2 support model (five years general support plus two optional years of extended support) with a 6+1 model. Under the new model, major releases receive six years of support from their release date, with the option to purchase one additional year of extended support.

### Release Cadence

VCF 9 follows a three-year major release cycle. Within each major release, four minor versions are planned (9.0, 9.1, 9.2, 9.3), released approximately every nine months.

### Support Durations for Minor Releases

The support duration varies based on whether a minor release is an interim release or the designated Long-Term Support (LTS) release:

| Release | Type | General Support Duration |
|---|---|---|
| VCF 9.0 | Standard | 27 months |
| VCF 9.1 | Standard | 27 months |
| VCF 9.2 | Standard | 27 months |
| VCF 9.3 | Long-Term Support (LTS) | 45 months |

### Rationale

The tiered approach provides flexibility:

- Organizations that want to stay current can adopt each minor release as it ships, benefiting from new features.
- Organizations that prefer stability can skip interim releases and wait for the LTS release (9.3), which receives nearly four years of support.
- The 27-month window for standard releases ensures that customers have sufficient time to test and adopt the next minor release before the previous one exits support.

### Key Dates

- VCF 9.0 GA: June 2025
- Estimated End of Service for the VCF 9.x line: June 17, 2031

### Extended Support

Customers can purchase up to one additional year of extended support beyond the general-support end date for their minor release. This provides a safety net for organizations that encounter delays in their upgrade planning.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/07/16/vmware-cloud-foundation-9-ushers-in-new-support-model-and-release-cadence/
- https://endoflife.date/vmware-cloud-foundation
- https://www.vmware.com/docs/vmware-cloud-foundation-9-0-general-faqs
- https://www.sdxcentral.com/news/broadcom-revamps-vcf-support-plans/

---

*Document compiled from official Broadcom/VMware documentation, release notes, and verified community sources. All content written in original language based on research findings.*
