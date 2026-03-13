# VCF 9: Storage and Compute Research

This document covers storage and compute enhancements introduced in VMware Cloud Foundation 9.0 and 9.0.1, including vSAN 9, vSphere 9, and ESXi 9 capabilities.

---

## 1. vSAN ESA vs OSA — Deep Comparison

### Architectural Differences

The Original Storage Architecture (OSA) organizes storage into disk groups, each containing a dedicated caching device (typically a fast SSD or NVMe drive) paired with capacity devices. The cache tier handles write buffering and read caching before data lands on capacity drives. The Express Storage Architecture (ESA) eliminates the disk group concept entirely, replacing it with a flat storage pool. Every NVMe device in an ESA host contributes directly to a unified pool — there is no separate cache tier because the architecture assumes all devices are high-performance NVMe flash, making a dedicated caching layer unnecessary.

### Hardware Requirements

ESA originally carried substantially higher hardware minimums than OSA because the architecture was designed to saturate NVMe device performance. The introduction of the ESA-AF-0 ReadyNode profile lowered the entry point significantly:

| Specification | ESA-AF-0 (Entry) | ESA-AF-2 (Standard) | OSA (Hybrid/AF) |
|---|---|---|---|
| CPU Cores | ~8 cores (half of AF-2) | 16+ cores | 8+ cores |
| RAM | ~32 GB (quarter of AF-2) | 128 GB+ | 32 GB+ |
| Storage Devices | 1+ NVMe (1.6 TB+) | 4-6 NVMe | SSD/HDD mix or all-flash |
| Networking | 10 GbE minimum | 25 GbE recommended | 10 GbE minimum |

In November 2025, Broadcom further reduced ESA hardware minimums based on production telemetry from thousands of clusters. For vSAN storage clusters, RAM requirements dropped by up to 67 percent and CPU core counts by up to 33 percent for the largest profiles. HCI clusters saw up to 50 percent RAM reductions for medium and large profiles. The new baseline starts at 16 CPU cores and 128 GB RAM across standardized Small, Medium, and Large tiers. Broadcom recommends 25 Gbps or faster networking for greenfield deployments, with four PCIe Gen 5 or six PCIe Gen 4 NVMe drives as a starting configuration.

### Performance

ESA delivers two to five times the performance of OSA on equivalent hardware. Individual ESA nodes can sustain up to 300,000 IOPS with consistent sub-millisecond latency. VCF 9.0 further boosts ESA performance by approximately 25 percent through traffic separation that enables distinct networking paths for compute and storage resources. ESA also consumes fewer CPU and network resources than OSA for identical workloads because it eliminates the overhead of managing separate cache and capacity tiers.

### Feature Parity

As of VCF 9.0, virtually all feature gaps between ESA and OSA have been closed. ESA supports RAID-1, RAID-5, and RAID-6 erasure coding, stretched clusters, file services (scaled to 500 file shares per cluster), encryption, and compression. Broadcom now positions ESA as the default choice for all new deployments and hardware refreshes, from edge to core.

### Ideal Workloads

OSA remains relevant only for environments with existing hybrid (SSD plus HDD) hardware or where budget constraints prevent NVMe adoption. ESA is suited for every other scenario — AI/ML training requiring sustained high IOPS, VDI deployments benefiting from deduplication, database workloads demanding low latency, and general-purpose virtualization seeking better efficiency.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2023/08/01/esa-for-all-platforms-and-all-workloads-with-esa-af-0/
- https://blogs.vmware.com/cloud-foundation/2025/11/14/driving-down-storage-costs-with-lower-hardware-requirements-for-vsan/
- https://www.starwindsoftware.com/blog/whats-new-in-storage-with-vmware-cloud-foundation-9-0/
- https://blogs.vmware.com/cloud-foundation/2025/06/17/announcing-availability-of-vsan-9-0/

---

## 2. Global Cluster-Wide Deduplication

### How It Works Technically

vSAN ESA in VCF 9.0 introduces software-based deduplication that operates across the entire cluster rather than within individual disk groups (as OSA did). The process is post-process and opportunistic, running in background cycles when CPU utilization is low, which avoids impacting the hot data write path. The technical sequence is:

1. The deduplication engine reads 4 KB blocks from the storage pool and generates a cryptographic hash for each block.
2. It searches for matching hash entries in a cluster-wide deduplication metadata table.
3. When a match is found, the duplicate block is moved to a dedicated deduplication data object.
4. Metadata pointers replace the duplicate blocks, and the original storage space is reclaimed.

Because the deduplication domain spans every host in the cluster, identical blocks stored on different hosts can be deduplicated against each other. This is a substantial improvement over OSA, where deduplication was confined to individual disk groups on a single host, severely limiting the ratio of duplicates that could be identified.

### RPQ / TQR Requirement

Global deduplication shipped in VCF 9.0 Patch 01 under a limited-availability Technical Qualification Request (TQR) program. Customers must contact Broadcom to request enablement. The controlled rollout exists because certain topologies and data services are not yet supported in combination with global deduplication:

- Stretched clusters are not supported in the initial release.
- Data-at-rest encryption cannot be used simultaneously.
- Multi-site topologies are excluded.

Broadcom is focusing initial enablement on customers running single-site vSAN HCI or vSAN storage clusters with 3 to 16 hosts and 25 GbE or faster networking.

### Space Savings

Broadcom reports potential data reduction of up to 8x depending on data characteristics and workload similarity across the cluster. In practical terms, a six-host cluster with 32 cores per host providing 192 TiB of vSAN storage under VCF licensing could store nearly 1.2 PiB of logical data at a 6:1 deduplication ratio. Internal testing indicates the implementation performs on par with or better than deduplication found in many popular dedicated storage arrays. Actual ratios will vary — environments with many similar VMs (such as VDI) will achieve higher ratios than those with diverse, unique data.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/06/19/global-deduplication-in-vsan-esa-for-vmware-cloud-foundation-9-0/
- https://blogs.vmware.com/cloud-foundation/2025/08/20/save-costs-and-scale-efficiently-with-vsan-deduplication-in-vmware-cloud-foundation-9-0/
- https://www.virtualvmx.com/2025/08/key-new-vsan-features-in-vmware-cloud.html
- https://www.yellow-bricks.com/2025/06/18/introducing-vsan-9-0/

---

## 3. Native vSAN-to-vSAN Replication

### Mechanism

VCF 9.0 introduces native asynchronous replication built directly on top of vSAN Data Protection. Rather than relying on third-party replication appliances or array-based replication at the LUN level, vSAN now replicates snapshots natively from one vSAN ESA datastore to another — whether the target is an HCI cluster or a disaggregated storage cluster.

The replication operates at the VM level using scalable, host-based replication. Each host participates in moving changed blocks to the remote site, distributing the replication workload across the cluster rather than funneling everything through a single appliance. Replication targets any vSAN ESA datastore, giving organizations flexibility in their recovery site topology.

### Configuration

Replication is configured through protection groups within the vSAN Data Protection interface or through the VMware Live Site Recovery console. Administrators define:

- Which VMs belong to a protection group.
- The replication frequency, with RPO values configurable from as low as 1 minute up to 24 hours.
- Retention policies determining how many snapshots to keep at the remote site (up to 200 per VM).
- Whether snapshots should be immutable (a simple toggle within protection group settings).

Protection groups are discoverable from both the vSAN Data Protection UI and the VMware Live Site Recovery interface, so administrators can manage replication through whichever console they prefer.

### Advantages Over Array-Based Replication

Because replication works at the individual VM level, organizations replicate only the VMs that need protection — not entire LUNs containing a mix of critical and non-critical workloads. This reduces storage consumption at the remote site and lowers network bandwidth requirements. Failover and failback are simpler because recovery targets individual VMs rather than requiring entire LUN groups to be failed over together.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/06/17/announcing-availability-of-vsan-9-0/
- https://vcdx181.com/2024/11/30/vcf-9-vsan-to-vsan-replication/
- https://www.virtualvmx.com/2025/08/key-new-vsan-features-in-vmware-cloud.html
- https://techietech.org/vcf-9-vsan-replication-with-deep-snapshots-global-deduplication/

---

## 4. Immutable Snapshots (200 per VM)

### Snapshot Architecture

vSAN Data Protection creates crash-consistent snapshots of VMs at regular intervals with minimal performance impact. Unlike traditional vSphere snapshots that chain delta VMDKs and accumulate I/O penalties as the chain deepens, vSAN's native snapshot implementation is designed to maintain performance even with large numbers of snapshots. The 200-snapshot-per-VM limit far exceeds the 32-snapshot limit enforced by traditional vCenter snapshot mechanisms and VADP-based APIs.

Snapshots are organized through protection groups, where administrators define which VMs to protect, the snapshot frequency, and how many snapshots to retain. The system operates on a "set it and forget it" model — once a protection group is configured, snapshots are taken automatically according to the defined schedule.

### Immutability for Ransomware Recovery

Immutability is enabled through a simple checkbox in the protection group configuration. When activated, snapshots become tamper-proof: they cannot be altered, deleted, or encrypted by any user or process, including administrative accounts. This provides a foundational defense against ransomware attacks that attempt to encrypt or destroy backup data after compromising the environment.

The ransomware recovery workflow operates as follows:

1. An organization maintains a rolling set of immutable snapshots across its critical VMs.
2. If ransomware encrypts production data, administrators identify the last known clean snapshot before the infection.
3. VMs can be recovered directly from immutable snapshots through the vSphere Client — including VMs that were deleted by the attacker.
4. Because snapshots are crash-consistent and immutable, the attacker cannot have tampered with the recovery points.

This capability integrates directly with VMware Live Cyber Recovery (VLCR) for more sophisticated cyber resilience workflows, including isolated clean room recovery environments.

### Licensing

vSAN Data Protection, including local snapshot capabilities with immutability, is included in the VCF license at no additional cost. Remote replication requires the VMware Live Recovery entitlement.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/11/04/vsan-data-protection-in-vmware-cloud-foundation-the-solution-you-already-own/
- https://blogs.vmware.com/cloud-foundation/2024/08/02/superior-snapshots-using-vmware-vsan-data-protection/
- https://www.virtualvmx.com/2025/08/key-new-vsan-features-in-vmware-cloud.html

---

## 5. VMware Live Recovery (RPO 1 Minute)

### Architecture

VMware Live Recovery (VLR) is delivered as a unified virtual appliance that consolidates what were previously separate services into a single deployment:

- Enhanced vSphere Replication for site-to-site VM replication.
- vSAN snapshot management for local and remote datastore snapshots.
- Site Recovery automation for orchestrated failover, failback, and testing.

The appliance is deployed per vCenter Server and supports backward compatibility with ESXi/vCenter 8.0 U3b and later. It manages three fundamental vSAN ESA data protection capabilities: local datastore snapshots, remote datastore snapshots, and replication between vSAN ESA datastores.

### 1-Minute RPO Mechanism

The 1-minute RPO is achieved through Enhanced vSphere Replication, which includes automated load balancing and scaling across hosts. Rather than relying on a single replication server, the enhanced replication distributes workload across multiple hosts in the cluster, enabling the throughput needed to capture and transmit changes every 60 seconds. RPO values are configurable from 1 minute to 24 hours, with up to 200 snapshots retained per VM.

### Integration with vSAN Replication

Protection groups configured in the vSAN Data Protection interface are automatically discovered and shared with the VMware Live Site Recovery console. This bidirectional visibility means administrators can define protection in either interface and manage recovery from either location. VLR handles the orchestration layer — automating the sequence of steps needed for failover, including powering off source VMs, registering recovered VMs at the target site, reconfiguring networking, and starting VMs in the correct order.

### Cyber Recovery Capabilities

VLR enables on-premises cyber recovery following the VMware Validated Solution blueprint. Organizations can establish isolated recovery environments using:

- VCF workload domains as clean room environments.
- vSAN ESA storage clusters functioning as cyber data vaults.
- VMware vDefend for network isolation of the clean room.
- VLR orchestration to automate recovery into the isolated environment.

This architecture supports data sovereignty requirements for organizations that cannot store recovery data in public clouds.

### Operational Monitoring

VLR integrates with the VCF Operations console, providing dashboards that display protected VM counts, recovery capability status, and protected storage capacity across cyber recovery, disaster recovery, and replication services.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/07/01/vmware-cloud-foundation-recovery-improvements-with-vmware-live-recovery/
- https://www.vmware.com/docs/vmware-live-recovery-faq
- https://blogs.vmware.com/cloud-foundation/2025/07/08/protecting-vmware-cloud-foundation-9-upgrade-to-enhanced-vsphere-replication/

---

## 6. vSphere 9 Virtual Hardware v22

### Monster VM Limits

Virtual Hardware version 22 raises the maximum VM configuration to 960 vCPUs and 24 TB of RAM. This is a substantial increase over previous generations and targets workloads such as large-scale databases, SAP HANA instances, and AI/ML training jobs that benefit from massive single-VM resource allocations. The 960 vCPU limit corresponds to 960 logical processors with support for 960 cores per socket.

### New CPU Instruction Set Support

Hardware version 22 adds compatibility with the latest processor generations, specifically Intel Granite Rapids (Xeon 6) and AMD EPYC 5th generation (Turin). Intel Hardware P-State (HWP) management is enabled by default on Sapphire Rapids and later processors, allowing the CPU to manage frequency scaling more efficiently than OS-directed methods.

### Device and Storage Enhancements

- NVMe 1.3c virtual controller support, enabling VMs to present modern NVMe semantics to guest operating systems.
- Enhanced PCI passthrough capabilities for direct device assignment.
- NVMe boot support over Fibre Channel SAN (NVMe/FC) and TCP SAN (NVMe/TCP) using the TP-8012 specification.
- 4K native (4Kn) drive support for VMDKs on VMFS, vSAN, and NFS datastores.

### Security Enhancements

- Virtual Trusted Platform Module (vTPM) updated to support TPM Library Specification Family 2.0 Revision 1.59.
- AMD SEV-SNP and Intel TDX support for confidential computing with hardware-enforced VM memory encryption.
- TLS 1.3 with the NIST_2024 profile enforced on port 443.
- Custom-signed operating system and driver packages can now be used with active Secure Boot enabled.
- Virtual IOMMU support for Enhanced DirectPath I/O, adding DMA isolation to passthrough devices.

### GPU and Accelerator Support

- vGPU device selection policies allow administrators to optimize for either performance or consolidation.
- Automated reconfiguration for Enhanced DirectPath I/O devices.
- vMotion optimizations for GPU-attached VMs that reduce migration downtime by 40 to 60 percent.

### Guest OS Additions

Hardware version 22 adds official support for numerous guest operating systems including RHEL 10, Oracle Linux 10, Debian 13, FreeBSD 15, Red Hat CoreOS, and several regional Linux distributions (Pardus, Miracle Linux, ProLinux, Kylin Linux, FusionOS).

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vsphere.html
- https://blogs.vmware.com/cloud-foundation/2025/06/23/vsphere-in-vcf-9-0-whats-new/
- https://www.virten.net/vmware/virtual-machine-hardware-versions/

---

## 7. NVMe Memory Tiering

### Production-Ready Status

NVMe memory tiering was introduced as a Technical Preview in vSphere 8.0 Update 3 and was promoted to production-ready status in VCF 9.0. The transition from preview to general availability addressed customer concerns around data resiliency, security, and configuration flexibility that existed in the initial implementation.

### How Hot/Cold Page Migration Works

The hypervisor maintains awareness of memory access patterns across all pages in the system. Pages are classified based on access frequency:

- Hot pages are those accessed frequently and remain in DRAM for the lowest possible latency.
- Cold pages are infrequently accessed regions that can tolerate the slightly higher latency of NVMe storage.

The ESXi memory manager continuously monitors page access patterns and migrates pages between tiers. When a cold page suddenly becomes active again, it is promoted back to DRAM. The migration process is transparent to the guest operating system — the VM sees a single, flat memory space regardless of which physical tier backs each page.

### DRAM-to-NVMe Ratios

The default ratio changed from 4:1 (DRAM:NVMe) in the tech preview to 1:1 in VCF 9.0, which doubles the effective memory capacity by default. Administrators can customize this ratio up to 1:4 (one part DRAM to four parts NVMe) for specific workload types. A VDI environment, for example, could quadruple its memory footprint at minimal additional cost because desktop VMs typically have large memory allocations but low active working sets.

### DRS and vMotion Awareness

The memory tiering implementation in VCF 9.0 is fully aware of DRS and vMotion operations. When DRS decides to migrate a VM, the system handles the tiered memory state appropriately during the migration — ensuring pages on the NVMe tier are correctly transferred and placed at the destination host.

### Performance Characteristics

Broadcom's internal testing shows up to 40 percent TCO reduction for most workloads and 25 to 30 percent increased CPU utilization through better VM consolidation. However, NVMe tiering increases CPU overhead by approximately 10 to 20 percent compared to DRAM-only configurations because the hypervisor must actively manage page placement across two tiers.

Ideal candidates for memory tiering are VMs with high consumed memory (above 50 percent of allocation) but low active memory (below 50 percent of total DRAM). Workloads where most of the memory is actively accessed continuously will see less benefit because most pages will remain in the DRAM tier.

### Redundancy and Configuration

Memory tiering supports two or more NVMe devices in a hardware RAID configuration for redundancy in case of device failure. Clusters can be deployed in a mixed configuration where only a subset of hosts have NVMe tiering enabled. Encryption is supported at both the VM and host levels. Over 1,500 NVMe drives are listed in Broadcom's compatibility guide.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/06/19/advanced-memory-tiering-now-available/
- https://lenovopress.lenovo.com/lp2288-implementing-memory-tiering-over-nvme-using-vmware-esxi-90
- https://www.go-euc.com/vmware-esxi-memory-management-reimagined-nvme-tiering-vs-traditional-swap/
- https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/9-0/vsphere-resource-management/memory-tiering-over-nvme/memory-tiering-configuration.html

---

## 8. ESXi ImageDB and Stateless Profile Changes

### Shift from Legacy Lifecycle Management

vSphere 9 completes a multi-generation transition away from legacy host management tools. Several foundational changes affect how ESXi hosts are provisioned, configured, and maintained:

**Baseline-Based Patching Removed:** vSphere Update Manager (VUM) baselines, the traditional mechanism for patching ESXi hosts by comparing installed VIBs against a baseline of desired patches, are no longer the primary method. All clusters and hosts must use image-based lifecycle management through vSphere Lifecycle Manager (vLCM). Organizations still using baseline-managed clusters must convert to vLCM images.

**ESX Image Library:** VCF 9.0 introduces a centralized image authoring capability at the vCenter level. Administrators compose a desired state image — including the ESXi version, firmware, drivers, and vendor add-ons — and vLCM enforces that image across all hosts in a cluster. This replaces the fragmented process of managing individual VIB packages.

**Mixed-Hardware Cluster Support:** vLCM now supports multiple component images within a single desired state definition, enabling clusters with servers from different vendors or with different hardware configurations to share a common lifecycle management policy.

### Stateless ESXi Deprecation

Booting ESXi hosts from the network with in-memory or cached images — a deployment model that maintained no persistent state on the host — is deprecated in ESXi 9.0. This affects organizations that used PXE boot with Auto Deploy to provision diskless servers.

### Auto Deploy and Host Profiles Deprecation

vSphere Auto Deploy, the automated ESXi host deployment system that used PXE boot infrastructure together with Host Profiles, is deprecated in ESXi 9.0 and will be removed in a future 9.x release. Host Profiles, which provided a template mechanism for applying consistent configurations across hosts, are similarly deprecated.

The recommended migration path is:

1. Install ESXi on local disks (or use scripted installations for automation).
2. Use vLCM images to manage host software lifecycle.
3. Use vSphere Configuration Profiles (the replacement for Host Profiles) to manage host configurations at the cluster level.

vSphere Configuration Profiles in VCF 9.0 now integrate NSX configuration management as well, providing a unified mechanism for both hypervisor and network virtualization configuration.

### Live Patching Expansion

vSphere 9.0 extends live patching capabilities to cover vmkernel components and NSX transport node updates without requiring hosts to enter maintenance mode. This reduces VM disruption during routine patching operations. Parallel lifecycle management operations are also supported, allowing administrators to multi-select clusters for simultaneous updates from the vCenter interface.

**Sources:**
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vsphere.html
- https://blogs.vmware.com/cloud-foundation/2025/06/23/vsphere-in-vcf-9-0-whats-new/
- https://knowledge.broadcom.com/external/article/322186/managing-esxi-host-lifecycle-operations.html

---

## 9. CPU Support Changes

### Discontinued Processor Families (Installation Blocked)

The following CPU families are discontinued in VCF 9.x, meaning the ESXi/VCF installer will refuse to proceed on servers using these processors:

**Intel (Discontinued):**
- Xeon D-1500 Series (Broadwell-DE)
- Xeon E3-1200-V5 and E3-1500-V5 Series (Skylake-S)
- Xeon E5-2600-V4 and E5-1600-V4 Series (Broadwell-EP)
- Xeon E5-4600-V4 Series (Broadwell-EX)
- Xeon E7-8800/4800-V4 Series (Broadwell-EX)
- Xeon E3-1200-V6 Series (Kabylake-S)
- Xeon Platinum 8100, Gold 6100/5100, Silver 4100, Bronze 3100 (Skylake-SP)
- Xeon D-2100 Series (Skylake-D)
- Xeon W-2100 Series (Skylake-W)

Pre-Haswell processors (Sandy Bridge, Ivy Bridge) were already dropped in earlier vSphere 8.0 updates. Haswell and Broadwell desktop/mobile variants were similarly removed previously.

### Deprecated Processor Families (Warning but Allowed)

The following CPU families trigger installer warnings but are still supported for now. They will likely be discontinued in a future VCF 9.x or 10.x release:

**AMD (Deprecated):**
- EPYC 7001 Series — Naples/Zen architecture
- EPYC 7002/7Fx2 Series — Rome/Zen 2 architecture

**Intel (Deprecated):**
- Xeon Platinum 8200 Series (Cascade Lake-SP)
- Xeon Gold 6200/5200 Series (Cascade Lake-SP/Refresh)
- Xeon Silver 4200, Bronze 3200 Series (Cascade Lake-SP/Refresh)
- Xeon E-2100 Series
- Xeon E-2200 Series (both 4/6-core and 8-core variants)
- Atom C3000 Series

### Supported Processor Families

Fully supported processors in VCF 9.0 include:

- Intel Ice Lake-SP (3rd gen Xeon Scalable) and later: Sapphire Rapids, Emerald Rapids, Granite Rapids
- AMD EPYC 7003 (Milan/Zen 3) and later: Genoa (Zen 4), Turin (Zen 5)

Hardware version 22 specifically adds support for Intel Granite Rapids and AMD EPYC 5th generation (Turin) instruction sets.

**Sources:**
- https://knowledge.broadcom.com/external/article/318697/cpu-support-deprecation-and-discontinuat.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vsphere.html
- https://vcloud-lab.com/entries/esxi-installation-and-configuration/fix-cpu-support-error-installing-vmware-esxi-9-on-unsupported-cpus

---

## 10. Expanded Principal Storage Options in VCF 9.0.1

### What Changed

Prior to VCF 9.0, the only storage option available during greenfield (fresh) deployments of the management domain was vSAN. Organizations using external storage arrays had to work around this limitation. VCF 9.0 and especially 9.0.1 expanded this significantly.

### Greenfield Deployment Support (Automated Workflows)

The following storage types are now supported as principal storage for the management workload domain during automated greenfield deployments:

- **vSAN** (both OSA and ESA) — remains the default and recommended option.
- **NFSv3** — enables organizations with NAS infrastructure to deploy VCF without vSAN.
- **VMFS on Fibre Channel** — supports organizations with existing FC SAN investments.

### Converged/Import Deployment Support

For organizations that bring an existing vSphere environment into VCF using the import/converge workflow, any vSphere 9-supported storage platform can serve as principal storage:

- iSCSI
- NFS v4.1
- FCoE (Fibre Channel over Ethernet)
- NVMe over Fabrics — both FC-NVMe and NVMe/TCP transports, as well as NVMe/RDMA

### Primary Datastore Priority

When a VCF cluster contains multiple datastore types, VCF determines which one serves as the primary datastore using a defined priority order:

1. vSAN (highest priority)
2. NFS v3
3. VMFS
4. NFS v4.1
5. iSCSI (lowest priority)

### Practical Implications

This expansion removes one of the most significant barriers to VCF adoption for organizations committed to external storage arrays. FC and NFS customers no longer need to purchase vSAN licenses and NVMe drives for the management domain if they prefer to use their existing storage infrastructure. However, vSAN remains Broadcom's recommended option for new deployments due to its tight integration with the VCF automation stack.

Fibre Channel storage is documented as both principal and supplemental storage for management and workload domains, with validated design guidance available in the VCF design library. NFS storage similarly supports both principal and supplemental roles across all domain types.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/11/11/vmware-cloud-foundation-9-now-ready-for-all-storage/
- https://knowledge.broadcom.com/external/article/416270
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/design/design-library/storage-models(1)/fibre-channel-storage.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/design/design-library/storage-models(1)/nfs-storage.html

---

## Additional References

- virtualvmx.com vSAN VCF 9 post: https://www.virtualvmx.com/2025/08/key-new-vsan-features-in-vmware-cloud.html
- VMware/Broadcom official vSAN 9.0 announcement: https://blogs.vmware.com/cloud-foundation/2025/06/17/announcing-availability-of-vsan-9-0/
- vSphere 9 what's new documentation: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vsphere.html
- vSAN what's new documentation: https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vsan.html
- VMware Live Recovery FAQ: https://www.vmware.com/docs/vmware-live-recovery-faq
- vSAN FAQ: https://www.vmware.com/docs/vmw-vsan-faqs
