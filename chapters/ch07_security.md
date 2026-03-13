# Chapter 7 --- Security

## The Threat Landscape VCF 9 Was Built For

Every major release of VMware Cloud Foundation has been shaped by the threats that keep infrastructure architects and CISOs up at night. VCF 9 is no exception. When Broadcom's engineering teams sat down to define the security posture for this generation of the platform, three forces dominated the conversation: ransomware, regulatory pressure, and supply-chain attacks. Understanding these forces is essential before we examine the specific controls VCF 9 delivers, because architecture decisions that look like implementation details on the surface---automatic certificate rotation, immutable snapshots, TPM-rooted attestation---are in fact direct responses to real-world adversary behavior.

### Ransomware

Ransomware has evolved from opportunistic desktop encryption into a professionalized, multi-stage campaign model. Modern ransomware operators conduct weeks or months of reconnaissance inside a target network before detonating payloads. They specifically target hypervisors and backup infrastructure because encrypting the virtualization layer multiplies the blast radius by orders of magnitude. A single compromised ESXi host can take hundreds of workloads offline in seconds. VCF 9 confronts this reality with defense-in-depth controls that span the network layer (micro-segmentation), the storage layer (encryption and immutable snapshots), the identity layer (phishing-resistant authentication), and the operations layer (continuous compliance monitoring and anomaly detection). The goal is not merely to prevent initial access---no platform can guarantee that---but to limit lateral movement, detect compromise early, and ensure that a known-good recovery point is always available.

### Regulatory Pressure

The regulatory landscape has tightened considerably in recent years. PCI-DSS v4.0 raised the bar on network segmentation, encryption key management, and continuous monitoring. HIPAA enforcement actions increasingly scrutinize whether covered entities have implemented technical safeguards at the infrastructure level, not just the application level. FedRAMP continues to expand the scope of cloud services that must meet NIST SP 800-53 controls, and the Department of Defense's DISA STIGs remain among the most prescriptive security baselines in the world. VCF 9 ships with compliance kits, pre-built audit mappings, and Ansible-driven remediation playbooks that allow organizations to demonstrate adherence to these frameworks without building everything from scratch. The platform does not merely claim compliance; it provides auditable evidence.

### Supply-Chain Attacks

The SolarWinds incident and subsequent supply-chain compromises demonstrated that even digitally signed, vendor-distributed software can be weaponized. VCF 9 addresses this through Secure Boot enforcement, TPM 2.0 attestation of ESXi host integrity, and vSphere Trust Authority, which gates access to encryption keys on successful hardware-rooted attestation. The platform's lifecycle management pipeline validates component signatures before applying updates, and the new Fleet Management appliance serves as a controlled distribution point for patches and configuration baselines. Trust, in VCF 9, is not assumed; it is continuously verified.

---

## Identity and Access Management

Identity is the new perimeter. Every breach post-mortem reinforces this point: if an attacker obtains valid credentials with excessive privileges, network firewalls and endpoint agents are largely irrelevant. VCF 9 delivers a substantially overhauled identity stack that brings modern authentication standards to private cloud infrastructure.

### VCF Identity Broker

At the center of the VCF 9 identity architecture is the Identity Broker, a dedicated authentication service that provides a centralized single sign-on (SSO) experience across all VCF components---vCenter Server, SDDC Manager, VCF Operations, VCF Automation, and NSX Manager. Prior releases relied on the embedded vCenter SSO domain backed by an Active Directory LDAP source, which worked but created a number of operational headaches: each vCenter instance maintained its own identity configuration, federation with modern identity providers required custom integration work, and multi-factor authentication (MFA) support was inconsistent.

The Identity Broker in VCF 9 supports three authentication protocols:

- **SAML 2.0**: The well-established XML-based protocol in which the identity provider issues digitally signed assertions. SAML remains the protocol of choice for organizations that have invested heavily in ADFS or similar federation services.
- **OpenID Connect (OIDC)**: A modern, JSON-based protocol built on OAuth 2.0. OIDC is the preferred path for organizations using Okta, Azure AD (Entra ID), Ping Identity, or Keycloak, and it integrates cleanly with MFA solutions and conditional access policies.
- **LDAP**: Retained for backward compatibility and for environments where Active Directory serves as both the directory and the authentication source.

The broker can be deployed in two modes. The **Embedded Model** runs the broker service within the vCenter appliance itself, which is appropriate for smaller deployments or environments where high availability of the identity layer is managed at the infrastructure level. The **Appliance Model** deploys the broker as an independent three-node virtual appliance cluster, providing dedicated high availability for authentication services. The appliance model is the recommended deployment for production environments with multiple workload domains, because it decouples the identity plane from any single vCenter instance and survives vCenter maintenance windows without impacting user authentication to other VCF components.

### Supported Identity Providers

VCF 9 has been validated with the following identity providers: Microsoft Active Directory Federation Services (ADFS), Microsoft Entra ID (Azure AD), Okta, Ping Identity, and Keycloak. Any SAML 2.0 or OIDC-compliant provider can be configured, but these five have documented integration guides and are covered by VMware Global Support.

### vCenter SSO and Domain Architecture

VCF 9 consolidates SSO domains across linked vCenter instances within a VCF deployment. All vCenter servers in a VCF instance share a single SSO domain, which simplifies permission management and eliminates the need for Enhanced Linked Mode (ELM) configurations that were fragile in earlier releases. The SSO domain still maintains a local identity source (vsphere.local) for break-glass administrative access, but day-to-day operations should authenticate through the Identity Broker against the external IdP.

### Role-Based Access Control Across VCF Components

VCF 9 enforces role-based access control (RBAC) across every management surface. The RBAC model is hierarchical, and roles are defined at the component level with inheritance rules that flow from the VCF platform layer down to individual workload domains.

**SDDC Manager** defines platform-level roles: ADMIN (full platform management), OPERATOR (day-to-day operations without lifecycle or configuration authority), and VIEWER (read-only monitoring). These roles govern who can commission hosts, create workload domains, trigger updates, and manage certificates.

**vCenter Server** uses the traditional vSphere privilege model, with roles assigned at the vCenter, datacenter, cluster, resource pool, or individual VM level. VCF 9 ships with a refined CloudAdmin role that provides the privileges necessary to create and manage workloads---virtual machines, resource pools, datastores, and networks---without granting access to the underlying management infrastructure (hosts, management VMs, and distributed switches managed by SDDC Manager). A CloudGlobalAdmin role extends CloudAdmin with global privileges for content libraries, tagging, and storage profiles.

**NSX** enforces its own RBAC through integration with the same identity source. Roles include Enterprise Admin, Network Engineer, Security Engineer, Security Operator, Network Operator, and Auditor. For VCF deployments, the recommended practice is to map Active Directory security groups to NSX roles, ensuring that role assignments are managed centrally and auditable through the directory.

**VCF Operations** (formerly Aria Operations) and **VCF Automation** (formerly Aria Automation) each expose project-based and organization-based role hierarchies that control who can view dashboards, define policies, deploy blueprints, and approve service requests.

The overarching principle is least privilege: every human and service account should hold the minimum set of permissions required for its function. VCF 9's consolidated identity model makes this practical at scale, because role assignments propagate consistently across components from a single source of truth.

---

## Network Security

Network security in VCF 9 is delivered primarily through NSX, which provides both the overlay networking fabric and the full suite of security services. The platform's network security posture is built on three pillars: micro-segmentation via the Distributed Firewall, perimeter protection via the Gateway Firewall, and threat detection via the Intrusion Detection and Prevention System (IDPS).

### Distributed Firewall and Micro-Segmentation

The NSX Distributed Firewall (DFW) operates in the kernel of every ESXi hypervisor, enforcing security policy at the virtual network interface (vNIC) level of each workload. This architecture has a critical advantage over traditional network firewalls: traffic between two VMs on the same host never traverses a physical network, so a perimeter firewall has no visibility into it. The DFW inspects this east-west traffic inline, at line rate, without requiring hairpinning through an external appliance.

Micro-segmentation is the practice of using the DFW to enforce least-privilege network access between individual workloads or groups of workloads. In a micro-segmented environment, the default stance is deny-all: no workload can communicate with any other workload unless a rule explicitly permits it. This dramatically limits the blast radius of a compromised VM, because lateral movement---the technique ransomware and advanced persistent threats rely on---is blocked by default.

### Security Groups and Tagging Strategy

The effectiveness of micro-segmentation depends entirely on how workloads are grouped and how policies reference those groups. VCF 9, through NSX, supports dynamic security groups based on tags, VM attributes (name, OS, cluster membership), and Active Directory group membership. Tags are the foundation of a scalable segmentation strategy.

A well-designed tagging taxonomy typically includes the following dimensions:

- **Environment**: production, staging, development, disaster-recovery
- **Application**: ERP, CRM, web-frontend, database-tier
- **Tier**: web, application, database, middleware
- **Compliance Zone**: PCI, HIPAA, general
- **Owner**: business-unit or cost-center identifier

NSX supports up to 30 tags per virtual machine, which is more than sufficient for most taxonomies. Tags should be applied at provisioning time through VCF Automation blueprints or Ansible playbooks, not manually, to ensure consistency and prevent drift.

Dynamic groups consume these tags to build membership criteria. For example, a group named "PCI-Database-Production" might include all VMs tagged with Environment:production, Compliance:PCI, and Tier:database. DFW rules then reference these groups rather than IP addresses, which means policies remain valid as workloads are provisioned, migrated, or decommissioned. This is a fundamental shift from traditional firewall management, where rules accumulate over years and become impossible to audit.

### Gateway Firewall

The Gateway Firewall operates at the Tier-0 and Tier-1 gateway level, providing north-south traffic inspection at the boundary between the NSX overlay and the physical network. In VCF 9, the Gateway Firewall supports stateful L4 filtering and can be combined with NSX Advanced Threat Prevention (ATP) for Layer 7 inspection, URL filtering, and malware analysis on ingress and egress traffic. For environments that require a physical firewall appliance for regulatory or organizational reasons, VCF supports service insertion, routing north-south traffic through a Palo Alto, Fortinet, or Check Point virtual appliance before or after Gateway Firewall processing.

### Intrusion Detection and Prevention System (IDPS)

NSX IDPS is available on both the DFW (east-west) and the Gateway Firewall (north-south). It uses signature-based detection with a curated signature set that is updated regularly, augmented by behavior-based anomaly detection in the Advanced Threat Prevention tier. IDPS policies can be applied per security group, allowing high-sensitivity workloads (PCI cardholder data environments, for example) to receive deeper inspection without imposing performance overhead on general-purpose workloads.

With VCF 9, vDefend extends lateral security to Virtual Private Cloud (VPC) constructs, enabling per-VPC micro-segmentation and IDPS policies. This is particularly relevant for multi-tenant environments or organizations that delegate network management to application teams while retaining centralized security policy governance.

---

## Data Encryption

Data encryption in VCF 9 is comprehensive, covering data at rest, data in transit, and data during replication. The platform provides multiple key management options to suit environments ranging from small private clouds to classified government networks.

### vSAN Data-at-Rest Encryption

vSAN encrypts all data written to disk using AES-256 in XTS mode, the industry-standard cipher for storage encryption. Encryption is enabled at the vSAN cluster level and is transparent to guest operating systems and applications---no agent or driver is required inside the VM.

The encryption architecture uses a two-tier key hierarchy:

1. **Data Encryption Keys (DEKs)**: Each disk group (or, in vSAN ESA, each disk) receives a unique, randomly generated DEK. Data is encrypted and decrypted using this key at the disk I/O level.
2. **Key Encryption Keys (KEKs)**: The KEK encrypts (wraps) the DEKs. The KEK is obtained from a key management system and is never stored on disk. When an ESXi host boots, it retrieves the KEK from the key provider and uses it to unwrap the DEKs stored on its local disks.

This two-tier model enables key rotation without re-encrypting all data: rotating the KEK re-wraps the existing DEKs, which is a lightweight metadata operation rather than a full disk rewrite.

### Native Key Provider vs. External KMS

VCF 9 supports two key management approaches:

**vSphere Native Key Provider (NKP)**: Introduced in vSphere 7.0 Update 2, the NKP generates and manages keys entirely within vCenter Server, eliminating the dependency on an external key management server. vCenter generates the KEK and pushes it to ESXi hosts; the hosts then generate their own DEKs. NKP is backed up automatically as part of vCenter backup, and it can be restored to a new vCenter instance if the original is lost. NKP is appropriate for organizations that need encryption at rest but do not have regulatory requirements mandating a FIPS 140-2 Level 2 or Level 3 certified key store. It substantially lowers the barrier to enabling encryption, because there is no external infrastructure to deploy, license, or maintain.

**External Key Management Server (KMS)**: For organizations subject to PCI-DSS, HIPAA, FedRAMP, or DoD requirements, an external KMS that supports the Key Management Interoperability Protocol (KMIP) 1.1 or later is often mandatory. VCF 9 has been validated with Thales CipherTrust Manager, Entrust KeyControl, Hytrust KeyControl, and IBM Security Guardium Key Lifecycle Manager. The external KMS provides a FIPS-validated hardware security module (HSM) for key storage and supports separation of duties: the storage team manages encrypted disks while the security team manages keys, and neither team can access the other's assets independently.

VCF 9 also introduces a new standard key provider option that uses a "wrapping key" to protect data-at-rest encryption, with the KEK generated and stored by the KMS. This provides an additional layer of key hierarchy for environments that require it.

### VM Encryption

Independent of vSAN encryption, vSphere VM Encryption encrypts an individual virtual machine's VMDK files and configuration. VM Encryption is applied through a storage policy, making it declarative: assign the "VM Encryption" policy to a VM, and vSphere handles the rest. This is particularly useful for workloads that reside on non-vSAN storage (NFS, VMFS on FC/iSCSI) where vSAN encryption is not available, or for environments that require per-VM key management for multi-tenancy.

VM Encryption and vSAN encryption can coexist, providing double encryption for high-sensitivity workloads. In practice, most organizations choose one or the other for a given workload to avoid unnecessary CPU overhead, but the option is available for regulatory environments that mandate layered encryption.

### In-Transit Encryption

VCF 9 encrypts management traffic using TLS 1.3 across all component-to-component communication paths. vSAN in-transit encryption (data traffic between hosts) can be enabled alongside data-at-rest encryption, protecting data as it traverses the network during normal I/O operations, rebuilds, and resyncs. vMotion encryption, enabled by default in VCF 9, protects live migration traffic to prevent eavesdropping on VM memory contents during migration.

### Replication Encryption

For disaster recovery configurations using vSphere Replication or VMware Live Site Recovery, replication traffic between sites is encrypted in transit. When combined with vSAN encryption at the recovery site, data remains encrypted at every stage of its lifecycle---at rest on the source, in transit during replication, and at rest on the target.

---

## Immutable Infrastructure and Ransomware Recovery

VCF 9 takes a defense-in-depth approach to ransomware resilience. No single control is sufficient; the strategy layers prevention, detection, and recovery capabilities so that a failure in any one layer does not result in catastrophic data loss.

### Defense-in-Depth Model

The VCF 9 ransomware defense model operates across five layers:

1. **Identity**: Phishing-resistant authentication (OIDC with MFA) limits credential theft. Least-privilege RBAC prevents a compromised account from reaching hypervisor or storage management interfaces.
2. **Network**: DFW micro-segmentation blocks lateral movement. IDPS detects known attack patterns. Gateway Firewall controls ingress and egress to prevent command-and-control communication.
3. **Host Integrity**: vSphere Trust Authority and Secure Boot ensure that only unmodified, attested ESXi hosts can access encryption keys or run workloads.
4. **Data Protection**: vSAN encryption prevents offline data theft. Immutable snapshots provide recovery points that cannot be deleted or modified, even by an administrator with root access.
5. **Detection and Response**: VCF Operations for Logs aggregates events across the stack, forwards them to SIEM platforms for correlation, and surfaces anomalies through pre-built dashboards.

### Immutable Snapshots

vSAN Data Protection in VCF 9 supports immutable snapshots---point-in-time copies of VM data that cannot be modified or deleted before their retention period expires. Immutability is enforced at the vSAN layer, below the management plane, which means that even a compromised vCenter administrator cannot alter or purge these snapshots.

The rationale for immutability is rooted in attacker behavior. Ransomware operators routinely target backup infrastructure before detonating encryption payloads, because they know that organizations with viable backups are less likely to pay ransoms. By making snapshots immutable, VCF 9 ensures that a known-good recovery point survives even if the management plane is fully compromised.

### Recovery Workflow

A ransomware recovery workflow in VCF 9 typically follows these steps:

1. **Containment**: Isolate affected workload domains using NSX DFW emergency rules that sever network connectivity to compromised segments while preserving management access.
2. **Assessment**: Use VCF Operations for Logs and the SIEM to determine the timeline of compromise and identify the last known-good snapshot.
3. **Validation**: Restore candidate snapshots into an isolated recovery environment (an air-gapped network segment or a dedicated recovery domain). Run behavioral analysis and antivirus scans against the restored workloads to confirm they are free of dormant malware.
4. **Recovery**: Once validated, promote the clean snapshots to production. vSAN Data Protection handles the snapshot-to-live-VM promotion natively.
5. **Hardening**: Remediate the attack vector (patch the exploited vulnerability, rotate compromised credentials, tighten segmentation rules) before reconnecting recovered workloads to the production network.

### Backup Ecosystem Compatibility

Immutable snapshots are not a replacement for a comprehensive backup strategy. VCF 9 integrates with leading backup solutions---Veeam Backup & Replication, Cohesity DataProtect, Dell PowerProtect Data Manager, Commvault, and Veritas NetBackup---through the vSphere Storage APIs for Data Protection (VADP). These solutions provide long-term retention, offsite replication, and granular file-level recovery that complement the rapid VM-level recovery provided by vSAN snapshots. The recommended architecture follows the 3-2-1 rule: three copies of data, on two different media types, with one copy offsite and air-gapped.

---

## Compliance Frameworks

VCF 9 ships with compliance kits and pre-built audit mappings for the most commonly encountered regulatory frameworks. These kits do not make an environment compliant by themselves---compliance is an organizational discipline, not a product feature---but they dramatically reduce the effort required to demonstrate technical control implementation during audits.

### NIST SP 800-53 Mapping

The VCF Compliance Kit maps VCF component configurations to NIST SP 800-53 Revision 5 control families, including Access Control (AC), Audit and Accountability (AU), Configuration Management (CM), Identification and Authentication (IA), System and Communications Protection (SC), and System and Information Integrity (SI). For each control, the kit provides the specific VCF configuration that satisfies the control requirement, the audit procedure to validate the configuration, and the evidence artifact that an auditor can review. This mapping serves as the foundation for derived frameworks: since FedRAMP, CMMC, and NIST 800-171 are all based on 800-53 controls, the VCF mapping covers a significant portion of those frameworks as well.

### PCI-DSS v4.0

PCI-DSS v4.0 introduced several requirements that map directly to VCF capabilities. Requirement 1 (network security controls) is addressed by NSX DFW micro-segmentation and Gateway Firewall. Requirement 3 (protect stored account data) is addressed by vSAN encryption and VM encryption with external KMS key management. Requirement 7 (restrict access by business need to know) maps to VCF RBAC and the Identity Broker. Requirement 10 (log and monitor all access) is addressed by VCF Operations for Logs with SIEM forwarding. Requirement 11 (test security regularly) is supported by the DISA STIG Ansible playbooks, which can be run on a scheduled basis to detect configuration drift.

### HIPAA

The HIPAA Security Rule requires administrative, physical, and technical safeguards for electronic protected health information (ePHI). VCF 9 addresses the technical safeguard categories: access control (RBAC, Identity Broker, MFA), audit controls (VCF Operations for Logs, syslog forwarding), integrity controls (vSphere Trust Authority, Secure Boot), and transmission security (TLS 1.3, vSAN in-transit encryption, vMotion encryption). The VCF Compliance Kit includes a HIPAA-specific mapping document that correlates each technical safeguard to the VCF configuration that implements it.

### FedRAMP

FedRAMP authorization requires implementation of NIST 800-53 controls at the Moderate or High baseline, depending on the information classification. VCF 9's 800-53 mapping covers the majority of the technical controls in both baselines. Continuous monitoring---a FedRAMP requirement that trips up many organizations---is addressed by VCF Operations, which can generate monthly security posture reports showing compliance drift, remediation actions, and vulnerability status. For FedRAMP High environments, vSphere Trust Authority with external KMS and FIPS-validated HSMs provides the encryption key management rigor that assessors expect.

### DISA STIG and Ansible Remediation Playbooks

The Defense Information Systems Agency publishes STIGs for vSphere, vCenter, ESXi, vSAN, NSX, and SDDC Manager. VCF 9 consolidates all STIG remediation into a single Ansible playbook that can be executed against an entire VCF instance. Each STIG rule has a corresponding Ansible variable that controls whether the remediation is applied, allowing organizations to tailor the playbook to their specific Risk Management Framework (RMF) authorization package. The playbook is idempotent: running it multiple times produces the same result, and it can be integrated into a CI/CD pipeline for continuous compliance enforcement.

The playbook structure is modular. Each VCF component (ESXi, vCenter, NSX, SDDC Manager) has its own Ansible role with defaults defined in `/defaults/main.yml`. Operators can override individual rule execution by setting the corresponding variable to `false` in a host-specific or group-specific variable file. This design respects the reality that not every STIG rule is applicable to every environment, and that some rules require compensating controls rather than direct implementation.

VMware publishes the hardening content and playbooks in the public `vmware/dod-compliance-and-automation` GitHub repository, providing full transparency into the remediation logic and allowing DoD customers to review, modify, and contribute back to the automation.

---

## Certificate Management at Scale

Certificate management is one of those operational domains that infrastructure teams tend to ignore until something expires and causes an outage. VCF 9 takes a proactive stance, providing multiple certificate authority options and, critically, automatic certificate renewal that eliminates the most common cause of certificate-related outages.

### Certificate Authority Options

VCF 9 supports four certificate authority configurations:

1. **Fleet Management CA**: The Fleet Management appliance, which orchestrates deployment and lifecycle management of VCF 9 components, also serves as a certificate authority for the components it manages. This is the default configuration, and it covers VCF Operations, VCF Automation, NSX Manager, and SDDC Manager. If left unchanged, components use this CA and receive automatic certificate renewal before expiration.

2. **VMware Certificate Authority (VMCA)**: The embedded CA within vCenter Server manages certificates for vSphere infrastructure components (ESXi hosts, vCenter services, and the Platform Services Controller). VMCA can operate in two modes: as a root CA that issues certificates directly, or as a subordinate CA that chains to an enterprise root CA. The subordinate CA mode is recommended for production environments, because it integrates vSphere certificates into the organization's existing PKI trust hierarchy.

3. **Microsoft Active Directory Certificate Services (ADCS)**: VCF 9 supports issuing certificates from an enterprise Microsoft CA for all management components. This is the preferred option for organizations with a mature Microsoft PKI that want all certificates to chain to a single enterprise root.

4. **OpenSSL CA**: SDDC Manager includes an embedded OpenSSL CA that can issue certificates for infrastructure components. This option supports auto-renewal and is useful for lab or development environments where a full enterprise PKI is not justified.

### Automatic Certificate Renewal

VCF 9 introduces automatic certificate renewal, enabled through a toggle in the VCF Operations UI. When enabled, the platform monitors certificate expiration dates across all managed components and initiates renewal before expiration. The renewal process uses non-disruptive certificate replacement for key components like vCenter Server, which means the management interfaces remain available during certificate rotation. This is a significant improvement over earlier releases, where certificate replacement for vCenter required a brief service interruption.

### Custom CA Integration and Geo-Fencing

For multinational organizations with region-specific PKI requirements, VCF 9 supports assigning different certificate authorities to different workload domains. A European workload domain can chain to a CA hosted in the EU, while a US workload domain chains to a US-hosted CA. This geo-fencing of certificates satisfies data sovereignty requirements in jurisdictions that mandate cryptographic key residency within national borders. The configuration is managed through SDDC Manager, which maintains the CA-to-domain mapping and ensures that certificate operations use the correct CA for each domain.

---

## Audit Logging

Comprehensive audit logging is a non-negotiable requirement for any enterprise infrastructure platform. VCF 9 provides centralized log collection, structured log forwarding, and pre-built integrations with Security Information and Event Management (SIEM) platforms.

### VCF Operations for Logs

VCF Operations for Logs (the successor to vRealize Log Insight and Aria Operations for Logs) is the centralized log aggregation service in VCF 9. It collects logs from every VCF component---ESXi hosts, vCenter Server, NSX, SDDC Manager, VCF Operations, VCF Automation---and indexes them for search, correlation, and alerting.

### Log Sources

The following log sources are collected by default in a VCF 9 deployment:

- **ESXi**: Authentication events, shell access, permission changes, VM power operations, storage events, and hardware alerts.
- **vCenter Server**: Login/logout events, privilege escalation, role assignments, VM lifecycle operations (create, clone, delete, snapshot, migrate), configuration changes, and task and event logs.
- **NSX**: Firewall rule hits (allow and deny), DFW policy changes, security group membership changes, IDPS alerts, and API calls.
- **SDDC Manager**: Host commissioning, workload domain creation, lifecycle update operations, certificate operations, and user management events.
- **VCF Operations**: Dashboard access, alert acknowledgments, policy changes, and report generation.
- **VCF Automation**: Blueprint deployments, catalog requests, approval workflow actions, and cloud account configuration changes.

### SIEM Integration

VCF Operations for Logs supports forwarding to external SIEM platforms via syslog (UDP, TCP, and TLS-encrypted TCP). The recommended configuration uses TLS-encrypted syslog to ensure in-transit protection of log data. VCF Operations for Logs can forward raw events or pre-processed alerts, depending on the organization's SIEM architecture. Common SIEM targets include Splunk, IBM QRadar, Microsoft Sentinel, Elastic Security, and CrowdStrike Falcon LogScale.

For organizations that prefer a pull-based integration model, VCF Operations for Logs exposes a REST API that SIEM platforms can poll for events. This is useful in environments where the SIEM team manages the integration and prefers not to rely on push-based forwarding.

The key to effective SIEM integration is ensuring that the correct events are forwarded at the correct verbosity. Forwarding every debug-level log from every ESXi host will overwhelm most SIEM platforms and inflate licensing costs. The recommended approach is to forward authentication events, authorization events (permission changes, role assignments), configuration changes, security policy modifications, and IDPS alerts. Informational and debug logs should be retained locally in VCF Operations for Logs, where they are available for troubleshooting but do not consume SIEM capacity.

### Retention for Compliance

Different compliance frameworks mandate different log retention periods. PCI-DSS requires at least 12 months of audit log history, with the most recent three months immediately available for analysis. HIPAA requires six years of retention for audit logs related to ePHI access. FedRAMP requires retention aligned with the organization's records management policy, typically three to five years. VCF Operations for Logs supports configurable retention policies at the log source and log type level, allowing organizations to apply different retention periods to different log categories. For long-term archival, logs can be forwarded to object storage (S3-compatible or NFS) where they are retained in compressed, immutable form.

---

## vSphere Trust Authority

vSphere Trust Authority (vTA) is the hardware-rooted trust framework that underpins VCF 9's most sensitive security controls. It provides remote attestation of ESXi host integrity, ensuring that only hosts in a known-good state can access encryption keys or run encrypted workloads. vTA is not new to VCF 9---it was introduced in vSphere 7.0---but VCF 9 integrates it more deeply into the platform's operational workflows and makes it a foundational element of the security architecture rather than an optional add-on.

### TPM Attestation

Attestation begins with the Trusted Platform Module (TPM) 2.0 chip installed on each ESXi host's motherboard. During the boot process, the TPM records cryptographic measurements of each boot stage---UEFI firmware, bootloader, ESXi kernel, and loaded modules---into its Platform Configuration Registers (PCRs). These measurements form a chain of trust rooted in the TPM's hardware-protected endorsement key.

When an ESXi host joins a Trust Authority cluster, its TPM endorsement key certificate is exported and imported into the Trust Authority service. The Trust Authority service maintains a list of known-good TPM measurements (the "ESXi base image" definition) and a list of trusted TPM endorsement keys. During attestation, the Trust Authority service compares the host's reported measurements against the known-good baseline. If the measurements match, the host is attested and receives access to encryption keys. If the measurements do not match---because the host's firmware has been tampered with, an unauthorized kernel module has been loaded, or Secure Boot has been disabled---attestation fails and the host is denied access to encryption keys.

### Encrypted VMs and Key Access Gating

The most powerful application of vTA is its integration with VM Encryption and vSAN Encryption key management. When vTA is configured, encryption keys are released only to attested hosts. This means that even if an attacker gains physical access to a host, removes its disks, and installs them in a different server, the data remains encrypted because the unauthorized host cannot obtain the keys.

This key-gating mechanism also protects against insider threats. A rogue administrator who modifies the ESXi boot image to inject a rootkit will cause attestation to fail on the next reboot, and the host will lose access to all encryption keys. The encrypted VMs on that host will become inaccessible, and the Trust Authority service will raise an alarm that is forwarded to VCF Operations for Logs and, from there, to the SIEM.

### Compromised Host Detection

vTA continuously monitors the attestation state of all hosts in the trusted cluster. If a host's attestation lapses---because it was rebooted with modified firmware, because its TPM fails a health check, or because the Trust Authority service loses connectivity to the host for an extended period---the host is flagged as untrusted. VCF 9's integration surfaces this state through multiple channels:

- **vCenter alarms**: A "Host TPM Attestation" alarm is raised in the vSphere Client, visible to administrators on the host summary page.
- **VCF Operations**: The Security Operations dashboard reflects the change in trust posture, and an alert is generated.
- **SIEM forwarding**: The attestation failure event is forwarded to the external SIEM for correlation with other security events.

Automated remediation can be configured using VCF Operations policies. For example, a policy can trigger a DRS migration of all VMs off an untrusted host and place the host in maintenance mode, preventing it from running workloads until its attestation is restored. This automated response limits the window during which a potentially compromised host has access to production workloads.

### Deployment Considerations

vSphere Trust Authority requires a dedicated Trust Authority cluster---a small cluster of ESXi hosts (typically two to four) that run the Attestation Service and the Key Provider Service. The Trust Authority cluster must be in a separate vCenter instance from the clusters it attests. This separation ensures that a compromise of the workload vCenter does not grant access to the attestation infrastructure.

For VCF 9 deployments, the management domain is the natural home for the Trust Authority cluster, with workload domains configured as trusted clusters. The Trust Authority hosts themselves must have TPM 2.0 chips and Secure Boot enabled, and they should be physically secured with the same rigor as network HSMs or KMS appliances.

---

## Bringing It All Together

The security architecture of VCF 9 is not a collection of independent features bolted onto a virtualization platform. It is an integrated, defense-in-depth system where each layer reinforces the others. Identity Broker authenticates users and enforces MFA before they ever reach a management console. RBAC ensures that authenticated users can perform only the actions their roles permit. NSX micro-segmentation prevents compromised workloads from communicating laterally. vSAN and VM encryption protect data at rest and in transit. vSphere Trust Authority ensures that only hardware-verified hosts can access encryption keys. Immutable snapshots guarantee that a clean recovery point survives even a full management-plane compromise. Compliance kits and Ansible playbooks automate the evidence collection and remediation that auditors require. And VCF Operations for Logs ties everything together by providing a single pane of glass for security events, compliance posture, and operational health.

No platform can make security effortless. But VCF 9 substantially reduces the gap between security intent and security implementation, giving infrastructure teams the tools to build private clouds that are defensible by design rather than defended by hope.
