# Chapter 11 — Lifecycle Management

Lifecycle management is, without exaggeration, the single most consequential operational discipline in any VMware Cloud Foundation deployment. You can architect the most elegant private cloud on Earth, but if you cannot patch it predictably, upgrade it safely, and expand it on demand, you have built a monument to technical debt. VCF 9 represents the most significant leap forward in lifecycle management that VMware has ever delivered, and in this chapter we will dissect every facet of it: the Bill of Materials model that underpins the entire release structure, the new support timelines that give enterprises breathing room, the mechanics of patching and upgrading, the pre-check framework that keeps you out of trouble, the path from VCF 5.x to 9.0, the convergence workflow for existing vSphere environments, and the operational best practices that separate a well-run fleet from a house of cards.

I have spent the better part of a decade helping enterprise customers through major VMware upgrades in my role as a Technical Account Manager. What VCF 9 delivers in lifecycle management is not incremental improvement. It is a structural reimagining of how private cloud infrastructure is maintained, and every administrator, architect, and operations lead needs to understand it deeply.

---

## The Bill of Materials Model

### What the BOM Is

At the heart of every VCF release sits the Bill of Materials, or BOM. The BOM is a curated, validated manifest of every software component that ships as part of a given VCF version. It enumerates the exact build numbers for vCenter Server, ESXi, NSX, VCF Operations, VCF Automation, VCF Installer, and every other constituent product. When VMware publishes VCF 9.0.0.0, the BOM tells you precisely which version of each component has been tested, certified, and declared interoperable with every other component in the stack.

This is transformative for one straightforward reason: it eliminates the interoperability guessing game. In the pre-VCF world, administrators had to cross-reference the VMware Product Interoperability Matrix, check hardware compatibility guides for each individual product, validate driver and firmware combinations independently, and hope that the resulting cocktail of versions would behave correctly in production. The BOM collapses all of that validation into a single, authoritative artifact. If a component version appears in the BOM, it has been tested against everything else in that same BOM. Full stop.

### Why It Is Transformative

The BOM model does more than simplify compatibility checking. It fundamentally changes how lifecycle operations are planned and executed. Consider the following implications:

**Deterministic upgrades.** When you upgrade from VCF 9.0.0.0 to 9.0.1.0, you are moving from one validated BOM to another. Every component transition has been tested as a unit. You are not blazing a trail; you are following a paved road.

**Selective component patching.** Starting with VCF 5.2 and carried forward into VCF 9, the BOM supports component-level flexibility. You can pick and choose which components to apply based on the impact to your environment and operational costs. If you encounter a critical vCenter defect but your ESXi hosts are running flawlessly, you can patch vCenter alone without touching the hypervisor layer. The BOM defines what is available; you decide what to apply.

**Auditability and compliance.** Regulators and auditors love determinism. When you can point to a single BOM and say "this is the validated state of our infrastructure," you have a compliance artifact that is far more compelling than a spreadsheet of individually tracked product versions.

**Simplified support engagements.** When you open a support case with Broadcom, the first question is always "what versions are you running?" With the BOM, the answer is a single VCF version number. The support engineer immediately knows the exact build of every component in your environment. This reduces diagnostic overhead significantly.

### Where to Find the BOM

The authoritative BOM for every VCF release is published in the Broadcom Technical Documentation portal at `techdocs.broadcom.com`. Navigate to the VCF 9.0 documentation tree and look under Release Notes for the "Bill of Materials" section. Each maintenance release (9.0.1.0, 9.0.2.0, and so on) publishes its own updated BOM reflecting the component versions included in that release. You can also retrieve BOM information programmatically through the VCF API, which is useful for automated compliance checks and drift detection in large fleets.

---

## Release Structure and Support Timelines

### Major, Minor, and Maintenance Releases

VCF 9 introduces a release cadence that is more deliberate and more predictable than anything VMware has offered before. The structure breaks down into three tiers:

**Major releases** (e.g., VCF 9.0) are delivered approximately every three years. A major release introduces significant architectural changes, new capabilities, and may include breaking changes that require careful planning. The jump from VCF 5.x to VCF 9.0 is a major release transition.

**Minor releases** (e.g., VCF 9.1, 9.2, 9.3) are delivered approximately every nine months after a major release. Each major release cycle includes four minor releases. Minor releases deliver meaningful new functionality, hardware enablement, and platform enhancements while maintaining backward compatibility within the major version family.

**Maintenance releases** (e.g., VCF 9.0.1.0, 9.0.2.0) are delivered between minor releases and focus on bug fixes, security patches, hardware enablement updates such as new server and CPU support, driver updates, guest OS support, and backward-compatible features that are limited in scope. The explicit design goal of maintenance releases is to minimize operational impact. They are the bread and butter of ongoing lifecycle operations.

### Support Timelines: The 27-Month and 45-Month Windows

Here is where the new model becomes concrete. The support timelines for VCF 9.x releases follow a deliberately asymmetric pattern:

| Release | General Support Duration | Classification |
|---------|--------------------------|----------------|
| VCF 9.0 | 27 months | Standard |
| VCF 9.1 | 27 months | Standard |
| VCF 9.2 | 27 months | Standard |
| VCF 9.3 | 45 months | Long-Term Support (LTS) |

The first three minor releases in a major version cycle each receive 27 months of general support from their respective release dates. The fourth and final minor release, VCF 9.3, is designated as the Long-Term Support release and receives 45 months of general support. This creates a natural upgrade planning horizon: organizations that want the longest possible support window on a single version should target the LTS release.

### The 6+1 Support Model Explained

VMware has moved from the previous 5+2 support model to a new 6+1 model. Here is what this means in practice:

**Six years of general support** for a major release family. From the date that VCF 9.0 ships, the entire 9.x family (9.0 through 9.3) is covered for six years of general support. This does not mean every individual minor release gets six years; it means the major version family as a whole is supported for that duration, with the LTS release (9.3) anchoring the tail end of the window.

**Plus one year of Extended Support.** After the general support period concludes, organizations can purchase an additional twelve months of Extended Support. This provides continued access to critical security patches and break-fix support, giving organizations up to seven years of total coverage for a major VCF version family.

The strategic significance of this shift cannot be overstated. Under the previous model, customers often found themselves on upgrade treadmills, forced to migrate to new major versions every 18 to 24 months simply to remain within support boundaries. The 6+1 model gives enterprises the breathing room to plan modernization on their own schedule, aligning infrastructure upgrades with business priorities rather than arbitrary support deadlines. For heavily regulated industries, healthcare, financial services, government, this is a profound operational improvement.

### Release Cadence Summary

Putting it all together, the cadence looks like this:

- **Year 0:** VCF 9.0 ships (major release)
- **Month ~9:** VCF 9.1 ships (minor release)
- **Month ~18:** VCF 9.2 ships (minor release)
- **Month ~27:** VCF 9.3 ships (minor release, LTS designation)
- **Throughout:** Maintenance releases (9.0.1, 9.0.2, 9.1.1, etc.) ship between minor releases
- **Year ~3:** VCF 10.0 ships (next major release)
- **Year 6:** General support ends for VCF 9.x
- **Year 7:** Extended support ends for VCF 9.x (if purchased)

This gives every organization a clear, multi-year planning horizon with no surprises.

---

## Types of Lifecycle Operations

VCF 9 supports a spectrum of lifecycle operations, each with different scope, risk profiles, and orchestration requirements. Understanding the distinctions is essential for planning.

### Component-Level Patching

The most granular lifecycle operation. Component-level patching allows you to update a single component, such as vCenter or ESXi, without touching the rest of the stack. This is the operation you use when a critical CVE drops for one product and you need to remediate quickly without the overhead of a full maintenance release. In VCF 9, patches within a maintenance release can be applied in any order. The ordering constraint that applies to major and minor upgrades does not apply here, giving you maximum flexibility for targeted remediation.

### Maintenance Releases

Maintenance releases update the BOM across multiple components simultaneously. When you move from VCF 9.0.0.0 to 9.0.1.0, you are applying a curated set of updates that have been tested together. The upgrade sequence for maintenance releases requires that you update management components that operate at the fleet level first, specifically the VCF Operations Fleet Management appliance, followed by the VCF Operations instance, and then proceed with the remaining components in your preferred order.

### Minor Upgrades

Moving from VCF 9.0 to 9.1 is a minor upgrade. These deliver new functionality and platform capabilities. Minor upgrades follow a strict sequencing requirement: VCF Operations components first, then SDDC Manager, then NSX, then vCenter, and finally ESXi hosts. The ordering matters because each component upgrade may introduce schema changes, API updates, or configuration modifications that downstream components depend on.

### Major Upgrades

The most significant lifecycle event. A major upgrade moves you across version families, such as from VCF 5.2 to VCF 9.0. Major upgrades may involve architectural changes, terminology shifts, and the introduction or deprecation of entire components. They require the most thorough planning, testing, and change management of any lifecycle operation. We will cover the VCF 5.x to 9.0 upgrade path in detail later in this chapter.

### Cluster Expansion

Adding hosts to an existing vSphere cluster within a workload domain. This is a scale-out operation that does not change the software version of the environment but does require that new hosts conform to the current BOM. VCF Operations validates host compatibility and orchestrates the addition, including vSphere Lifecycle Manager (vLCM) image application and NSX preparation.

### Workload Domain Creation

Creating a new workload domain within an existing VCF instance. In VCF 9, this operation has moved from SDDC Manager to VCF Operations. The workflow provisions new vCenter instances, configures NSX, and deploys clusters using the current BOM. From a lifecycle perspective, new workload domains are born at the current version, so they do not require immediate patching.

### Fleet Expansion

In VCF 9, fleet expansion refers to adding entirely new VCF instances to an existing fleet managed by VCF Operations Fleet Management. This is the mechanism for multi-site and multi-instance growth. The Fleet Management appliance maintains a unified view of all instances and their lifecycle states, enabling coordinated upgrade planning across geographically distributed infrastructure.

---

## The Pre-Check Framework

### Why Pre-Checks Matter

Every experienced VMware administrator has a story about an upgrade that went sideways because of an overlooked prerequisite: an expired certificate, an incompatible driver, a host in maintenance mode that nobody remembered, a deprecated NSX feature still in active use. The pre-check framework exists to catch these issues before they become outages.

In VCF 9, the pre-check framework has been substantially enhanced and is now a mandatory step in the upgrade workflow. You cannot proceed with a lifecycle operation without first running and reviewing pre-checks.

### Categories of Pre-Checks

Pre-checks are organized into several categories that map to the major areas of upgrade risk:

**Infrastructure readiness.** Validates that compute, storage, and network resources are sufficient for the upgrade. This includes checking for adequate free capacity to support vMotion during rolling host upgrades, verifying datastore space for component installations, and confirming network connectivity between management components.

**Version compatibility.** Confirms that current component versions are eligible for the target version. For example, if you are upgrading to VCF 9.0, the pre-check validates that your VCF Operations deployment is already at version 9.0.0.0 before allowing BOM component upgrades to proceed.

**Configuration compliance.** Checks for configurations that are incompatible with the target version. This includes deprecated features, unsupported hardware, legacy virtual switch configurations, and settings that must be remediated before the upgrade can succeed. For VCF 9, a critical example is the requirement that all ESXi clusters must be converted from baseline management to vSphere Lifecycle Manager (vLCM) images, since baselines are no longer supported.

**Certificate and credential validation.** Verifies that certificates are not expired or approaching expiration and that service account credentials are valid. Certificate issues are among the most common causes of upgrade failures, and the pre-check framework catches them early.

**Component health.** Runs health checks against vCenter, NSX, ESXi hosts, and management appliances to ensure they are in a healthy, operable state before the upgrade begins. The SOS Utility can be leveraged to obtain detailed health check reports of all components involved.

**NSX-specific validation.** Checks for deprecated NSX features that must be removed before upgrading. If the pre-check detects an unsupported feature still in use, it returns an error that blocks the upgrade until the feature is decommissioned. Additionally, registered compute managers must have a service account enabled when upgrading to VCF 9.0; otherwise, a pre-check error is raised.

### Triggering Pre-Checks

Pre-checks can be triggered in two ways:

**Through the UI.** In VCF Operations (or SDDC Manager during the transitional period), navigate to the lifecycle management section, select the target version, and click the pre-check button. The system executes all applicable checks and presents results in the console.

**Through the API.** Pre-checks can be triggered programmatically using the VCF API. Starting with VCF 9.0, VMware publishes OpenAPI specifications for all major components, enabling you to integrate pre-check execution into CI/CD pipelines, scheduled automation workflows, or custom monitoring dashboards. This is particularly valuable for large fleets where manual pre-check execution across dozens of workload domains is impractical.

### Reading Results: Errors vs. Warnings

Pre-check results are classified into two severity levels, and understanding the distinction is critical:

**Errors** are blocking conditions. An error means the upgrade cannot proceed safely and will likely fail if attempted. Errors must be resolved before the lifecycle operation can continue. Examples include expired certificates, incompatible component versions, deprecated features still in active use, and insufficient resources for the upgrade workflow.

**Warnings** are advisory conditions. A warning indicates a situation that may cause issues or represents a best-practice deviation, but it does not strictly prevent the upgrade from succeeding. Warnings should be reviewed carefully and addressed where possible, but they do not block the upgrade workflow. Examples include approaching certificate expiration, non-critical configuration drift, and advisory notices about changed behavior in the target version.

My strong recommendation is to treat warnings with the same urgency as errors whenever possible. A warning that is ignored today often becomes the root cause of a production incident next month. The pre-check framework is giving you advance notice; take advantage of it.

### Automating Pre-Checks at Scale

For organizations managing large VCF fleets, manual pre-check review does not scale. The VCF API enables several automation patterns:

- **Scheduled pre-checks.** Run pre-checks weekly or bi-weekly against all workload domains, even when no upgrade is imminent. This gives you a continuous view of fleet readiness and catches drift early.
- **Integration with monitoring platforms.** Parse pre-check API responses and feed results into your existing monitoring or ITSM platforms. Create alerts for new errors or warnings so that remediation can begin before the next maintenance window.
- **Pre-check gating in change management.** Integrate pre-check execution into your change management workflow. Before a change advisory board approves an upgrade window, require a clean pre-check report as a prerequisite. The OpenAPI specification, available from the GitHub `vmware/vcf-api-specs` repository, makes this integration straightforward with tools like Postman or custom scripts in any programming language.

---

## Upgrading VCF 5.x to 9.0

### The Magnitude of the Transition

Upgrading from VCF 5.x to 9.0 is not a routine maintenance operation. It is a generational platform transition that introduces new architectural patterns, new management constructs, new terminology, and the deprecation of components and workflows that have been foundational to VCF operations for years. Approach this upgrade with the same rigor you would apply to a data center migration.

### Prerequisites

Before you begin the upgrade, the following prerequisites must be satisfied:

1. **Minimum starting version.** Your environment must be running VCF 5.2 or later. If you are on VCF 5.0 or 5.1, you must first upgrade to 5.2.x before the jump to 9.0 is supported.

2. **VCF Operations (formerly Aria Suite) readiness.** Any existing Aria Automation and Aria Operations deployments must be upgraded to version 8.18 before the VCF 9.0 upgrade can proceed. This is a hard prerequisite. VCF Operations is a mandatory component in VCF 9.0. If Aria Operations or Aria Automation is not currently installed, it will need to be installed as part of the upgrade process.

3. **vSphere Lifecycle Manager image mode.** All ESXi clusters must be converted from baseline management to vLCM image-based management. Baselines are no longer supported in VCF 9.0. This conversion should be done well in advance of the upgrade window, as it is a non-trivial operation in environments with diverse hardware configurations.

4. **Temporary IP address for vCenter Reduced Downtime Upgrade.** VCF orchestrates a Reduced Downtime Upgrade (RDU) for vCenter Server, which requires a temporary IP address during the transition. Ensure this IP is allocated and available in the management network before the upgrade begins.

5. **NTP, DNS, and network validation.** Confirm that NTP synchronization is consistent across all management components, DNS forward and reverse lookup records are correct, and network connectivity between all management endpoints is healthy. These seem basic, but they are responsible for a disproportionate share of upgrade failures.

6. **Backup everything.** Take file-level backups of SDDC Manager, vCenter Server, NSX Manager, the VCF Operations appliance, and the Fleet Management appliance. Confirm that backups are valid and restorable. There is no single "rollback button" for a VCF major upgrade; your backups are your safety net.

### Upgrade Sequence

The upgrade proceeds in a strict sequence. Violating this sequence will result in failures:

1. **VCF Operations components.** Upgrade Aria Operations to VCF Operations 9.0 and Aria Automation to VCF Automation 9.0. These must be completed before any other component upgrade.

2. **SDDC Manager.** Upgrade SDDC Manager to the 9.0 version. This is the control plane upgrade that enables the subsequent component upgrades.

3. **NSX.** Upgrade NSX to the 9.0 BOM version. The NSX upgrade includes edge node upgrades and host transport node upgrades, which are performed in a rolling fashion to minimize workload disruption.

4. **vCenter Server.** Upgrade vCenter using the Reduced Downtime Upgrade workflow. The temporary IP address is consumed during this phase.

5. **ESXi hosts.** Upgrade ESXi hosts to the 9.0 BOM version. This is performed as a rolling upgrade across clusters, with VMs automatically vMotioned to other hosts during each host's upgrade cycle. Ensure sufficient cluster capacity to accommodate the temporary reduction in host count.

6. **Repeat for workload domains.** After the management domain is fully upgraded, proceed to upgrade each additional workload domain following the same NSX, vCenter, ESXi sequence.

### Workload Impact

During the upgrade process, workload VMs continue to run. The rolling nature of ESXi host upgrades means that VMs are live-migrated between hosts as each host enters maintenance mode, upgrades, reboots, and rejoins the cluster. The key factors that determine workload impact are:

- **Cluster capacity.** You need sufficient spare capacity to absorb the workload of the host currently being upgraded. A cluster operating at 90 percent utilization will struggle with rolling upgrades.
- **vMotion network performance.** Ensure your vMotion network has adequate bandwidth for the migration traffic generated during rolling upgrades.
- **Application sensitivity.** While vMotion is designed to be transparent, some latency-sensitive applications may experience brief performance variations during migration. Coordinate with application owners for critical workloads.

### Rollback Strategy

Let me be direct: there is no single-click rollback for a VCF major upgrade. The upgrade modifies database schemas, configuration files, and component interdependencies in ways that cannot be trivially reversed. Your rollback strategy is your backup strategy.

The recommended approach is to treat every serially performed upgrade step as a checkpoint. Before each major phase (SDDC Manager, NSX, vCenter, ESXi), validate your backup of the component about to be upgraded. If a failure occurs during a specific phase, you restore that component from its pre-upgrade backup, troubleshoot the root cause, and retry. This is why the backup prerequisite is non-negotiable.

For the Fleet Management appliance specifically, take a snapshot prior to each upgrade attempt. If the upgrade fails, revert to the snapshot, resolve the underlying issue, and retry.

### Terminology Changes on Day One

When the upgrade completes, your environment will look and feel different. Prepare your team for these terminology shifts:

| Pre-9.0 Term | VCF 9.0 Term | Notes |
|---------------|--------------|-------|
| Cloud Builder | VCF Installer | New appliance for deployment and convergence |
| SDDC Manager UI | VCF Operations UI | SDDC Manager UI is deprecated; workflows move to VCF Operations and vSphere Client |
| Aria Operations | VCF Operations | Rebranded and functionally expanded |
| Aria Automation | VCF Automation | Rebranded and integrated |
| VI Workload Domain | Workload Domain | Simplified terminology |
| N/A | Fleet | New concept: all infrastructure managed by VCF Operations |
| N/A | VCF Instance | A single VCF deployment within a fleet |

These are not cosmetic changes. The architectural relationship between components has shifted. SDDC Manager, while still present, is being deprecated in favor of VCF Operations as the primary management interface. Lifecycle management APIs that previously lived at the SDDC Manager endpoint (`/v1/system/precheck`) have moved to VCF Operations. Update your automation scripts, runbooks, and documentation accordingly.

---

## Converging Existing vSphere to VCF 9

### What Convergence Means

Convergence is the process of taking an existing vSphere environment, one that was deployed independently and is not currently managed by VCF, and bringing it under VCF management. This is distinct from a fresh (greenfield) VCF deployment and distinct from an upgrade of an existing VCF deployment. Convergence is how organizations with mature vSphere estates adopt VCF without starting from scratch.

In VCF 9, convergence has become significantly more accessible thanks to two developments: the new VCF Installer appliance with built-in convergence workflows, and the expansion of supported storage types.

### Any-Storage Support

This is one of the most consequential changes in VCF 9. Historically, VCF deployments required vSAN as the principal storage for the management cluster in a greenfield deployment. This was a significant barrier for organizations with existing investments in external storage arrays.

VCF 9 now supports any vSphere-supported storage type as principal storage for converged environments. This includes:

- **vSAN** (as before)
- **iSCSI**
- **NFS v4.1**
- **NVMe over Fabrics** (FC, TCP, and RDMA)
- **Fibre Channel over Ethernet (FCoE)**

The critical nuance is that these additional storage types are available through the convergence and VCF Import workflows, not through greenfield deployment. If you are deploying VCF from scratch, vSAN remains the supported option for the management cluster. But if you are converging an existing vSphere environment that already uses iSCSI or NFS, you can retain that storage configuration and bring it into VCF management.

This single change dramatically expands the addressable market for VCF. Organizations that previously dismissed VCF because of the vSAN requirement can now converge their existing storage investments.

### Convergence vs. Fresh Deploy

The decision between convergence and fresh deployment depends on your starting point:

**Choose convergence when:**
- You have a mature vSphere environment with production workloads that cannot be migrated to a new platform
- Your storage infrastructure uses external arrays that you want to preserve
- You want to minimize disruption by reusing existing vCenter, clusters, and networking configurations
- Time-to-value is critical and you cannot afford a parallel build-and-migrate approach

**Choose fresh deployment when:**
- You are building a net-new environment with no existing workloads
- You want to start with a clean, fully validated configuration
- You are standardizing on vSAN and want the simplest deployment path
- Your existing environment has significant configuration drift or technical debt that would complicate convergence

### The Convergence Workflow

The convergence process follows these high-level steps:

1. **Environment assessment.** Validate that the existing vSphere environment meets VCF 9 convergence requirements. Key items include: vCenter version compatibility, ESXi host version and hardware compatibility, network configuration (Distributed Virtual Switch is required; Standard vSwitch environments must be migrated), and storage configuration validation. Enhanced Linked Mode (ELM) configurations must be remediated prior to convergence.

2. **Deploy the VCF Installer.** The VCF Installer appliance is deployed into the same cluster as the existing vCenter Server. This is the appliance that orchestrates the convergence process.

3. **For non-vSAN storage:** If using alternative storage types (iSCSI, NFS v4.1, NVMe-oF), configure the storage datastore on one of the hosts, deploy the vCenter 9 appliance on that host, configure the vSphere cluster using the pre-configured datastore, and then proceed with the VCF Installer convergence workflow.

4. **Run the VCF Installer convergence wizard.** The wizard walks you through specifying which existing components (vCenter, clusters, networking) should be reused and which new components (NSX, VCF Operations) need to be deployed. The installer validates the configuration, deploys missing components, and registers everything into the VCF management plane.

5. **Post-convergence validation.** After convergence completes, validate that all workload domains are visible in VCF Operations, that lifecycle management is functional, and that the BOM reflects the correct component versions.

The result is a fully managed VCF 9 environment built on top of your existing infrastructure, with all the lifecycle management, fleet management, and operational capabilities of a greenfield deployment.

---

## Operational Best Practices

Having covered the mechanics of lifecycle management, let us turn to the practices that determine whether your lifecycle operations run smoothly or devolve into firefighting.

### Patch Cadence

Establish a regular patch cadence and stick to it. I recommend the following rhythm for most enterprise environments:

- **Monthly:** Review newly published maintenance releases and security advisories. Assess applicability to your environment.
- **Quarterly:** Apply maintenance releases to non-production and staging environments. Execute pre-checks against production.
- **Semi-annually (at minimum):** Apply validated maintenance releases to production. Do not allow production to fall more than two maintenance releases behind the current BOM.

The temptation to defer patching until a "convenient time" is the single greatest threat to infrastructure hygiene. Convenient times do not exist. Schedule the work, protect the maintenance windows, and execute.

### Staging Environments

Maintain at least one non-production VCF environment that mirrors your production configuration as closely as possible. Every lifecycle operation, whether it is a maintenance release, a minor upgrade, or a major version transition, should be executed in staging before production. This is non-negotiable for major upgrades and strongly recommended for maintenance releases.

Your staging environment does not need to match production in scale, but it must match in architecture: same storage type, same NSX topology, same VCF Operations configuration, same authentication integration. The goal is to surface upgrade issues in a context where they can be resolved without workload impact.

### Backup Before Every Lifecycle Operation

Take component-level backups before every lifecycle operation. Not just major upgrades. Every maintenance release. Every patch. The backups should cover:

- SDDC Manager database and configuration
- vCenter Server (file-based backup)
- NSX Manager (backup to external target)
- VCF Operations Fleet Management appliance (snapshot and file-based backup)
- VCF Automation (file-based backup)
- Identity Broker configuration

Validate backup integrity by confirming that the backup completed without errors and that the backup file is accessible from a location outside the component being backed up. A backup stored on the same appliance you are about to upgrade is not a backup; it is a co-located liability.

### ITSM Integration

Integrate VCF lifecycle operations into your IT Service Management framework. This means:

- **Change management.** Every lifecycle operation should go through your change advisory board process. Require a clean pre-check report as a prerequisite for change approval.
- **Incident management.** If a lifecycle operation fails, it should generate an incident ticket automatically. Integrate the VCF API with your ITSM platform to enable this.
- **Configuration management.** After every lifecycle operation, update your CMDB with the new BOM version. The VCF API provides all the information needed to automate this.
- **Release management.** Track upcoming VMware releases and map them to your internal release calendar. Use the release cadence information in this chapter to plan quarters in advance.

### Capacity Planning for Lifecycle Operations

Rolling upgrades consume resources. During an ESXi host upgrade, the cluster loses one host's worth of compute capacity while that host is in maintenance mode. If your clusters are running at high utilization, you may not have sufficient headroom for the rolling upgrade to proceed without workload consolidation issues.

The rule of thumb is to maintain at least N+1 capacity in every cluster at all times, where N is the number of hosts required to run all workloads at acceptable performance levels. For clusters with four or fewer hosts, this is especially critical, as losing one host represents 25 percent or more of total capacity.

Plan storage capacity as well. Maintenance releases and upgrades may require temporary additional storage for staged binaries, component installations, and snapshot chains. Ensure your datastores have adequate free space before initiating lifecycle operations.

### Certificate Calendar

Certificates are the silent killer of lifecycle operations. An expired certificate will fail pre-checks, block upgrades, and potentially cause service outages. Maintain a certificate calendar that tracks:

- The expiration date of every certificate in the VCF environment, including vCenter, NSX, ESXi, VCF Operations, and the identity broker
- Renewal deadlines (set alerts for 90 days, 60 days, and 30 days before expiration)
- Certificate authority (CA) trust chain validity

VCF 9 runs all components with FIPS 140-2 and 140-3 standards enabled by default, which increases the importance of proper certificate management. Rotate certificates proactively, not reactively.

### Documentation Discipline

Maintain a lifecycle operations runbook that is updated after every lifecycle event. The runbook should include:

- **Pre-operation checklist.** A step-by-step list of actions to perform before initiating any lifecycle operation: backups verified, pre-checks clean, change ticket approved, communication sent to stakeholders.
- **Execution procedure.** The specific steps for each type of lifecycle operation, tailored to your environment. Reference the official Broadcom documentation but add your environment-specific details: IP addresses, credentials vault locations, escalation contacts.
- **Post-operation validation.** A checklist of verification steps to confirm the lifecycle operation completed successfully: BOM version confirmed, all services healthy, workloads operational, monitoring alerts cleared.
- **Rollback procedure.** Step-by-step instructions for reverting each component from backup if the lifecycle operation fails. Include estimated rollback times and escalation paths.

Update the runbook after every lifecycle event based on lessons learned. The runbook is a living document, not a shelf decoration.

### Leveraging Fleet Management for Multi-Instance Operations

For organizations managing multiple VCF instances, the Fleet Management capability in VCF Operations is indispensable. Fleet Management provides:

- **Unified visibility** into the lifecycle state of every instance in the fleet
- **Coordinated upgrade planning** across instances, ensuring that management components are updated at the fleet level before instance-level core components
- **Centralized binary management** with support for both connected (online depot) and disconnected (offline bundle) environments
- **Fleet-wide pre-check execution** to assess readiness across all instances simultaneously

Use Fleet Management to establish upgrade waves: upgrade your smallest, least critical instance first, validate for a stabilization period, then proceed to larger and more critical instances. This staged approach limits blast radius and gives you operational experience with each upgrade before applying it to your most important workloads.

### Quick Boot and Live Patch

VCF 9 introduces Quick Boot and Live Patch capabilities to minimize host downtime during ESXi updates. Quick Boot allows ESXi hosts to restart without performing a full hardware POST cycle, reducing reboot times from minutes to seconds. Live Patch applies certain security updates to running ESXi hosts without requiring a reboot at all.

Factor these capabilities into your lifecycle planning. Not every patch will be eligible for Live Patch, but when it is available, it dramatically reduces the maintenance window required for host-level updates.

---

## Conclusion

Lifecycle management in VCF 9 is not a bolt-on feature; it is a core architectural principle. The BOM model provides deterministic, validated upgrades. The 6+1 support model gives enterprises the planning horizon they need. The pre-check framework catches problems before they become outages. The fleet management architecture scales lifecycle operations across multi-instance environments. And the convergence workflow opens VCF to the vast installed base of existing vSphere environments.

But tools alone do not deliver operational excellence. The practices you build around those tools, the patch cadence you commit to, the backups you take religiously, the staging environments you maintain, the runbooks you keep current, are what separate organizations that operate confidently from those that operate in perpetual anxiety.

VCF 9 gives you the best lifecycle management platform that VMware has ever built. The rest is discipline.
