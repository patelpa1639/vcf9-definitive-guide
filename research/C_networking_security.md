# VCF 9 Networking and Security — Research Document

> Compiled March 2026. All content written from original research; not copied verbatim from any source.

---

## 1. NSX Enhanced Data Path (EDP)

### Overview and Performance Gains

The Enhanced Data Path represents a fundamental rearchitecture of how NSX processes packets at the hypervisor level. In VCF 9.0, EDP Standard mode is enabled by default for all new deployments, marking a shift from the legacy standard vSwitch data path that had been the norm for years. Testing has demonstrated throughput improvements of up to three times compared to the traditional NSX data path, along with significant reductions in latency and CPU utilization per packet.

### Kernel Bypass Mechanism

EDP achieves its performance advantage primarily through a DPDK-inspired approach to packet handling. Instead of relying on the traditional interrupt-driven model where the CPU must context-switch to handle each incoming packet, EDP uses polling-mode drivers that continuously check for new packets. This eliminates the overhead of interrupt processing and allows the CPU to process packets in tight, efficient loops. The net effect is that packets traverse fewer code paths between the physical NIC and the virtual machine, reducing per-packet overhead substantially.

EDP Standard (previously referred to as ENS or EDP Interrupt Mode) is the recommended configuration for all general-purpose compute workloads and NSX Edge clusters running ESXi 8.0 Update 3e with NSX 4.2.2 or later releases. It delivers measurably higher packet processing efficiency without requiring application-level changes.

### DPU Offload Capabilities

Beyond the software-level optimizations, NSX supports offloading network and security processing to Data Processing Units (DPUs, also called SmartNICs). When a DPU is present, NSX can move switching, routing, and firewall operations off the main host CPUs entirely and onto the dedicated processors embedded in the DPU. This frees host compute resources for tenant workloads while maintaining the full NSX feature set, including security policy enforcement, observability, and distributed routing.

VCF 9.0 requires EDP-capable network adapters. Administrators should verify NIC compatibility before deployment, as not all adapters support the enhanced data path modes. Broadcom and NVIDIA are among the vendors providing DPU hardware validated for NSX offload.

### Sources
- https://williamlam.com/2025/06/is-my-nic-supported-with-enhanced-data-path-edp-with-vcf-9-0.html
- https://techdocs.broadcom.com/us/en/vmware-cis/nsx/vmware-nsx/4-2/administration-guide/host-switches/enhanced-datapath.html
- https://drpranayjha.wordpress.com/2025/08/30/an-explanation-how-nsx-enhanced-data-path-gives-you-the-3x-boost-in-vcf-9/
- https://www.vmware.com/docs/vmware-nsx-datasheet
- https://www.securefever.com/blog/nsx-with-smartnics-make-the-esxi-hypervisor-fast-again-na45t

---

## 2. VPC Networking in VCF 9

### The VPC Construct

VCF 9.0 introduces the Virtual Private Cloud (VPC) as a first-class networking primitive, bringing a public-cloud-style consumption model to on-premises infrastructure. A VPC is an isolated networking domain assigned to a cloud consumer, containing subnets (which map to NSX logical networks under the hood), firewall rules, NAT policies, and load balancing services. This design allows tenants to self-service their own network configurations without requiring deep NSX expertise or raising tickets with infrastructure teams.

Two networking object models coexist in VCF 9: VPC Networking and traditional Segment Networking. VPC Networking is the recommended path for new deployments, as it aligns with familiar public cloud paradigms and integrates directly into both VCF Automation and vCenter workflows.

### IP Spaces and Address Management

VPC networking relies on IP Blocks — address prefixes that NSX automatically subnets for use by VPC workloads. Two key IP blocks are defined during configuration:

- **VPC External IP Block**: A routable prefix that management components (vCenter, NSX, Avi, VCF Automation) use to communicate with Supervisor services and VPC endpoints.
- **Private Transit Gateway IP Block**: Used internally for subnets routable between VPCs, primarily consumed by Supervisor Service Pods. The default range is 172.20.0.0/16, though it can be changed if it conflicts with existing management networks.

NSX handles the allocation and subdivision of these blocks automatically, removing manual IPAM burden from administrators.

### Transit Gateway Architecture

The Transit Gateway is a new network construct introduced in VCF 9, configured during workload domain creation. Its role is to interconnect VPCs with each other and with the Provider Gateway (the northbound exit point to the physical network). Each NSX Project receives its own Transit Gateway, and every VPC must belong to a project.

Two Transit Gateway modes are available:

**Centralized Transit Gateway (CTGW)**: The northbound connection routes through a traditional NSX Tier-0 gateway running on Edge nodes. This model supports the full range of network services, including Source NAT, load balancing, and VCF Automation integration. Individual tenant VPCs connect through their own active-standby Transit Gateways.

**Distributed Transit Gateway (DTGW)**: The Transit Gateway runs directly on ESXi hosts without any Edge VMs. It connects to the physical fabric via an external VLAN, bypassing the Tier-0 entirely. This eliminates the dependency on Edge nodes, reduces network hops, improves throughput and latency, and simplifies troubleshooting. However, DTGW does not support VCF Automation or Supervisor integration, and stateful services like Source NAT and load balancing are unavailable. Only stateless 1:1 NAT and distributed DHCP are supported. A hard requirement is that every ESXi host must attach to the same VLAN on the physical network.

### Subnet Types

Within a VPC, three subnet types control reachability:

- **Private VPC**: Not routable outside the VPC. Workloads require NAT for external communication.
- **Private TGW**: Not routable north of the Transit Gateway, but can reach other workloads south of it. NAT is needed for communication beyond the TGW.
- **Public**: Fully routable north of the TGW. Workloads are generally accessible from external endpoints, subject to Provider Gateway routing configuration.

### VCFA Organization Model Integration

VCF Automation (VCFA) integrates with VPC networking through a provider-tenant model. The provider configures Regional Networking for each tenant organization, specifying which Provider Gateway handles external connectivity and which Edge Cluster serves Transit and VPC Gateways. Each tenant gets a single regional networking configuration, meaning they connect to one Provider Gateway per region. Tenants then consume VPCs through the VCFA self-service catalog, with full lifecycle management handled through automation workflows.

### Sources
- https://blogs.vmware.com/cloud-foundation/2025/06/17/modernize-networking-in-vcf-9-0/
- https://blogs.vmware.com/cloud-foundation/2025/07/02/vmware-virtual-private-cloud/
- https://fojta.wordpress.com/2025/12/05/vcf-automation-9-0-networking-deep-dive/
- https://vstellar.com/2025/07/vcf-9-part-3-networking-models/
- https://vstellar.com/2025/08/vcf-9-part-9-vpc-networking-deep-dive/
- https://sdn-warrior.org/posts/vcf9-nsx-vpc/
- https://blogs.vmware.com/cloud-foundation/2025/06/25/vpc-distributed-network-connectivity-no-nsx-edge-vms/

---

## 3. NSX Micro-Segmentation and Distributed Firewall (DFW)

### Distributed Firewall Fundamentals

The NSX Distributed Firewall enforces security policy at each virtual machine's virtual NIC within the hypervisor kernel. Unlike perimeter firewalls that inspect traffic only as it enters or leaves a network boundary, the DFW operates on every east-west packet flowing between workloads on the same host or across hosts. This architecture means that even if an attacker compromises one VM, lateral movement to adjacent workloads is blocked by policy, regardless of whether those workloads share the same physical host or network segment.

In VCF 9, the DFW supports Layer 7 application-aware rules in addition to traditional L3/L4 filtering, and it integrates with service insertion for both north-south and east-west traffic paths. This makes micro-segmentation strategies more granular and compliance-aligned.

### Zero Trust Implementation with vDefend DFW 1-2-3-4

Broadcom has published a prescriptive four-stage methodology called vDefend DFW 1-2-3-4, designed to move organizations from a flat network to full zero-trust micro-segmentation in weeks rather than months:

**Stage 1 — Security Assessment**: Activate the DFW, enable traffic visualization across clusters, and generate a Security Segmentation Report. This report identifies improvement areas and establishes a baseline security score that can be tracked over time.

**Stage 2 — Infrastructure Services (Days)**: The system auto-discovers shared infrastructure services such as DNS, NTP, DHCP, and LDAP. Locking down these services first eliminates the most common command-and-control and data exfiltration channels that attackers rely on.

**Stage 3 — Environment/Zone Segmentation**: Administrators import metadata (via CSV or other methods) to define boundaries between environments such as Development, Production, and DMZ zones. The platform auto-tags workloads based on this metadata and monitors for traffic leakage between zones, flagging violations.

**Stage 4 — Application Micro-Segmentation**: This stage involves three sub-steps: mapping workloads to applications, defining ring-fencing controls that specify permitted ports and protocols between application tiers, and continuously monitoring inter-tier traffic for deviations.

A key feature throughout this workflow is automated Firewall Rule Analysis, which identifies duplicate rules, redundant rules, shadowed rules, and overly permissive configurations. This eliminates the manual rule auditing that has traditionally been one of the biggest barriers to effective micro-segmentation.

### vDefend IDS/IPS and Malware Prevention

Beyond basic firewalling, NSX vDefend provides distributed IDS/IPS that inspects traffic against signature databases to detect and block intrusion attempts. The IDS/IPS operates in both Classic and Turbo modes and functions on both the Distributed Firewall and Gateway Firewall. For IDS/IPS to inspect traffic, DFW must be enabled first — if a DFW rule blocks traffic before it reaches the IDS engine, no inspection occurs.

Distributed Malware Prevention extracts files from both east-west and north-south traffic flows and analyzes them for malicious behavior. A service virtual machine (SVM) is deployed on each host in the cluster, consuming 4 vCPUs, 6 GB RAM, and 80 GB of disk space per host.

### Sources
- https://blogs.vmware.com/security/2025/11/vdefend-dfw-1-2-3-4-vcf.html
- https://digitalthoughtdisruption.com/2025/07/15/vmware-vcf-9-nsx-deep-dive/
- https://puneetsharma.blog/2025/11/27/part-2-vcf-9-0-nsx-security-hardening-regulatory-compliance/
- https://techdocs.broadcom.com/us/en/vmware-security-load-balancing/vdefend/vdefend-atp/9-0/nsx-ids-ips-and-nsx-malware-prevention.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/nsx-ids-ips-and-nsx-malware-prevention.html

---

## 4. T0/T1 Gateway Architecture

### Tier-0 Gateway

The Tier-0 (T0) gateway serves as the north-south boundary between the NSX overlay and the physical network. It is the only gateway tier that supports BGP peering with upstream routers, making it the critical handoff point for dynamic routing. T0 gateways are realized on NSX Edge nodes as Service Routers (SRs).

**Active-Active Mode**: When deployed in active-active configuration, all Edge nodes actively forward traffic. This mode enables ECMP (Equal-Cost Multi-Path) routing, where multiple equal-cost paths to the same destination are used simultaneously. Upstream physical routers learn the same prefixes from multiple Edge nodes via BGP and distribute traffic across them. If one Edge node fails or loses its BGP session, the remaining nodes continue forwarding without interruption. Active-active mode also supports inter-SR iBGP, allowing Edge nodes within the same T0 to reroute traffic internally if a particular node loses its northbound peering session.

**Active-Standby Mode**: One Edge node is primary and handles all traffic while the standby takes over upon failure. This mode supports stateful services like NAT and VPN that require session state tracking on a single node. In VCF 9, the VPC with Full Services networking model uses active-standby T0 gateways to provide NAT, load balancing, and other stateful capabilities.

### ECMP and BGP Peering

ECMP is available only on T0 gateways in active-active mode. When configured, the T0 advertises routes to all its Edge uplinks simultaneously, and the physical leaf switches distribute incoming traffic across them. This achieves both higher aggregate throughput and resilience. BGP is the sole dynamic routing protocol supported on T0 gateways (unlike the legacy NSX-V platform, which supported OSPF on edge routers as well).

### Tier-1 Gateway

Tier-1 (T1) gateways provide distributed routing within the overlay. They connect to T0 gateways via automatically managed router links on the internal 100.64.0.0/10 address space. This peering between T1 and T0 is fully transparent to administrators — NSX manages the link addresses and route advertisements internally. T1 gateways support services such as load balancing, NAT, DNS forwarding, and gateway firewall policies.

### VCF 9 Changes

In VCF 9, the Transit Gateway effectively replaces the traditional T0 in many VPC deployment scenarios. The Transit Gateway is architecturally similar to a T0 but lacks external interfaces — its sole function is to interconnect VPCs and connect them northbound to either a Provider Gateway (in centralized mode) or directly to a VLAN (in distributed mode). The T0 with external interfaces is reduced to a Service Router-only component without a Distributed Router (DR) in VPC networking.

### Sources
- https://vmwarecloud.com.au/vmware-cloud-hosting/nsx-t-tier0-ecmp-architecture-and-routing-explained/
- https://vxplanet.com/2019/07/27/nsx-t-tier0-ecmp-routing-explained/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/advanced-network-management/administration-guide/tier-0-gateways/configure-bgp-in-nsx.html
- https://vrealize.it/2025/07/11/vcf9-nsx-edge-setup-what-has-changed/
- https://vstellar.com/2025/05/nsx-4-x-vrf-gateways-part-1-architecture-configuring-vrf-gateways/

---

## 5. NSX Federation Multi-Site

### Global Manager Architecture

NSX Federation enables centralized management of multiple NSX deployments across geographically distributed sites. The architecture introduces a Global Manager (GM) — a dedicated NSX Manager cluster deployed in the "Global" role. The GM cluster consists of three virtual machines in an active configuration, with a separate standby cluster that stays continuously synchronized. If the active GM fails, the standby can be promoted.

The active GM pushes policy configuration down to the Local Managers (LMs) at each participating site. Each site retains its own LM cluster for local operations, meaning that if connectivity to the GM is lost, local networking and security continue to function autonomously. The GM provides a single pane of glass for defining cross-site networking topologies and security policies.

### Stretched Segments

One of Federation's most valuable capabilities is the ability to stretch Layer 2 segments across multiple sites. A stretched segment appears as a single broadcast domain spanning two or more locations, enabling workloads to retain their IP addresses when migrated or recovered at a remote site. This is particularly important for disaster recovery scenarios, as it eliminates the need to re-IP workloads during failover, significantly reducing Recovery Time Objectives (RTO) and operational complexity.

Stretched T0 and T1 gateways accompany stretched segments, ensuring that routing and gateway services are consistently available at all participating sites. The GM coordinates the configuration of these multi-site gateways so that policies remain synchronized.

### Federation in VCF 9

VCF supports NSX Federation for multi-region deployments, enabling stretched networks and unified security policies across VCF instances. The VCF 9 documentation explicitly includes Federation configuration guidance, including bridging configuration on the Global Manager for connecting overlay segments to physical VLANs at each site.

### Sources
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/advanced-network-management/administration-guide/managing-nsx-t-in-multiple-locations/nsx-t-federation.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/advanced-network-management/administration-guide/managing-nsx-t-in-multiple-locations/nsx-t-federation/overview-of-federation/understanding-federation.html
- https://www.wwt.com/article/federation-nsx-t-gets-centralized
- https://vstellar.com/2020/10/nsx-t-federation-part-1-introduction-architecture/
- https://my-sddc.net/creating-stretched-networks-with-nsx-t-federation/

---

## 6. Avi Load Balancer Integration

### Controller Architecture

The Avi Load Balancer (formerly NSX Advanced Load Balancer) uses a controller-based architecture where a centralized Controller cluster manages distributed Service Engines. The Controller cluster is typically deployed as three nodes for high availability. Quorum requires at least two of three nodes to be operational. Nodes communicate via encrypted SSH tunnels with a ten-second heartbeat interval; if four consecutive heartbeats (40 seconds) are missed, a node is considered down.

In VCF 9, Avi Controllers are deployed and managed through VCF Operations, which handles automated lifecycle operations including spin-up, spin-down, scaling, and password rotation. A single-node Controller option is also available for lab and smaller environments.

### Service Engines

Service Engines (SEs) are the data-plane components that actually process application traffic. They are deployed as virtual machines on ESXi hosts and configured through Service Engine Groups (SEGs), which define sizing, placement, and HA behavior. When an SE fails due to a host failure, the Controller first defers to vSphere HA to restore the SE on another host. If vSphere HA does not recover the SE within a timeout window, the Controller creates a replacement SE on a healthy host.

### NSX Cloud Mode

Avi integrates with NSX through a Cloud Connector that links the Controller to the management domain's NSX Manager and vCenter. This NSX Cloud configuration enables Avi to automatically discover NSX networking topology, place Service Engines on appropriate networks, and integrate with NSX security policies. The Cloud Connector must be configured during initial deployment.

### L7 Capabilities and VPC Support

Avi provides enterprise-grade L4 through L7 load balancing services including SSL termination, content switching, and HTTP multiplexing. For Kubernetes workloads in VCF 9, Avi serves as the ingress controller and supports the Gateway API for vSphere Kubernetes Service (VKS) deployments. Web Application Firewall (WAF) capabilities can be applied across multiple VCF workload domains.

Avi version 31.1 and later adds native support for VCF VPC networking, including simplified VPC configurations with centralized Transit Gateway connectivity. Multi-tenancy support allows each tenant to manage their own load balancing resources with proper isolation.

### Global Server Load Balancing (GSLB)

GSLB distributes application traffic across multiple geographically dispersed VCF instances, providing both disaster recovery and performance optimization. Configuration and monitoring are centralized through the Avi Controller, with analytics providing visibility into application health and performance across all regions.

### Self-Service and Automation

Load balancing can be consumed as code through VCF Automation integrations, with built-in service catalog items enabling self-service provisioning. This aligns with VCF 9's broader philosophy of offering infrastructure-as-a-service consumption models.

### Sources
- https://blogs.vmware.com/load-balancing/2025/06/17/avi-for-vcf-9/
- https://williamlam.com/2025/07/single-node-vmware-avi-load-balancer-with-vcf-9-0.html
- https://techdocs.broadcom.com/us/en/vmware-security-load-balancing/avi-load-balancer/avi-load-balancer-vmware-cloud-foundation/9-0.html
- https://fojta.wordpress.com/2025/08/16/load-balancing-vcf-automation-with-avi/
- https://www.vmware.com/docs/avi-load-balancer-for-vcf

---

## 7. vSphere Trust Authority

### Architecture Overview (Legacy)

vSphere Trust Authority (vTA) was designed as a hardware root-of-trust framework built on two core services running on a dedicated Trust Authority cluster:

**Attestation Service**: Validates the integrity of remote ESXi hosts by examining measurements captured by their TPM 2.0 chips. When an ESXi host boots, the TPM records cryptographic measurements of the software stack. The Attestation Service compares these measurements against pre-approved ESXi image baselines and verifies the TPM's endorsement key. Upon successful validation, the service issues a signed JSON Web Token (JWT) containing identity and validity assertions.

**Key Provider Service**: Acts as a gatekeeper between ESXi hosts and external KMIP-compliant key management servers. It only releases encryption keys to hosts that present valid attestation tokens, ensuring that unauthorized or tampered hosts cannot access encrypted workload data. This removes the need for vCenter or individual ESXi hosts to hold key server credentials directly.

The full trust chain operates as follows: an ESXi host boots and its TPM records measurements; the host contacts the Attestation Service with these measurements; upon successful verification, the host receives a JWT; the host presents this token to the Key Provider Service to obtain encryption keys for encrypted VMs. All communication occurs over HTTPS on port 443 through the ESXi host's reverse proxy.

### Status in VCF 9

VCF 9.0 has discontinued vSphere Trust Authority, removing it along with several other legacy security components. Broadcom's stated rationale is to eliminate legacy attack surfaces and drive adoption of modern alternatives. The confidential computing capabilities introduced in VCF 9 — based on AMD SEV-SNP and Intel TDX — provide hardware-based data-in-use encryption with per-VM encryption keys and workload/host attestation, serving as the forward-looking replacement for vTA's trust model.

### Sources
- https://techdocs.broadcom.com/us/en/vmware-cis/vsphere/vsphere/9-0/vsphere-trust-authority-overview.html
- https://blogs.vmware.com/cloud-foundation/2025/08/05/security-vmware-cloud-foundation-9-0/
- https://4sysops.com/archives/vsphere-7-0-vmware-vsphere-trust-authority-vta/

---

## 8. VCF Identity Broker

### Purpose and Architecture

The VCF Identity Broker (formerly known as VIDB — VMware Identity Broker) serves as the unified authentication control plane for the entire VCF stack. It eliminates the fragmented, component-specific login experience of earlier versions by managing the connection between all VCF components and an external Identity Provider (IdP) or Directory Service. When properly configured, administrators log in once and gain seamless access to all VCF components based on their assigned permissions.

### Deployment Modes

Two deployment models are available:

**Embedded Mode**: The Identity Broker runs within vCenter as an integrated service. This is suitable for single-instance deployments and smaller environments.

**Appliance Mode**: A clustered deployment of at least three nodes, providing high availability with tolerance for a single node failure. This mode supports connecting up to five VCF instances to a single Identity Broker cluster, making it the appropriate choice for large-scale or multi-instance fleet management.

### Supported Identity Providers and Protocols

The Identity Broker supports modern authentication standards:

- **OIDC (OpenID Connect)** and **OAuth 2.0** for token-based authentication
- **SAML 2.0** for federated identity assertions

Validated identity providers include:
- Microsoft Entra ID (Azure AD)
- Microsoft Active Directory Federation Services (ADFS)
- Okta
- Ping Identity / PingFederate
- Any generic SAML 2.0-compliant provider
- Keycloak (community-validated)

For user and group provisioning, the following directory services are supported:
- Active Directory via LDAP
- OpenLDAP
- SCIM (System for Cross-domain Identity Management) for automated provisioning
- Just-In-Time (JIT) provisioning

### Multi-Factor Authentication

Because the Identity Broker delegates authentication to external IdPs, organizations can enforce MFA policies through their existing corporate identity infrastructure. Any MFA method supported by the configured IdP (TOTP, push notifications, hardware tokens, etc.) applies automatically to VCF logins.

### SSO Scope

Once configured, SSO extends across all major VCF components: vSphere Client, NSX Manager, VCF Operations, VCF Operations for Logs, VCF Operations for Networks, VCF Automation, and HCX. This replaces the Enhanced Linked Mode (ELM) approach used in earlier VCF versions.

### Sources
- https://blogs.vmware.com/cloud-foundation/2026/02/18/bringing-out-of-the-box-modern-identity-to-your-infrastructure-with-vmware-cloud-foundation-9-0/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/fleet-management/what-is/protocols-suported-for--sso.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/fleet-management/what-is/setting-up-sso/cofigure-vmware-cloud-foundation-identity-provider.html
- https://williamlam.com/2025/06/vcf-9-0-single-sign-on-sso-with-keycloak-idp.html
- https://vxworld.co.uk/2025/11/11/vcf-9-deploying-vcf-identity-broker/

---

## 9. RBAC Model Across VCF Components

### Unified Identity Foundation

VCF 9's RBAC model builds on the Identity Broker's unified SSO layer. After users and groups are provisioned from a directory service (AD/LDAP or SCIM), role assignments map these identities to service-specific roles across each VCF component. The goal is a least-privilege model where each user or group receives only the permissions necessary for their function.

### NSX RBAC

NSX implements granular role-based access control scoped to Projects and VPCs:

- **Enterprise Admin**: Full access to the entire NSX environment, including all projects and system-wide configurations.
- **Project Admin**: Full access to all configurations within a specific project, but no access to other projects or system-level settings.
- **Network Admin / Network Operator**: Permissions limited to networking configuration (or read-only viewing) within the scope of a project.
- **Security Admin / Security Operator**: Permissions limited to security policy configuration (or read-only viewing) within a project.

Enterprise administrators create NSX Projects and assign RBAC policies before tenants can begin creating VPCs. This ensures that multi-tenant boundaries are established before self-service consumption begins.

### vCenter RBAC

vCenter continues to use its established role and permission model, with roles assigned at various inventory levels (datacenter, cluster, resource pool, VM folder). VCF 9 adds programmatic APIs (OpenAPI 3.0-compliant) for managing permissions, roles, and access control, enabling infrastructure-as-code approaches to RBAC management.

### VCF Operations and Automation RBAC

VCF Operations and VCF Automation each maintain their own role definitions that align with the unified identity layer. In VCF Automation, the provider-tenant model maps naturally to RBAC: providers configure infrastructure and set policies, while tenant administrators manage resources within their allocated scope.

### Cross-Component Coordination

When VCF SSO is configured for a component (NSX, vCenter, etc.), the same identity source feeds all of them. This means a single group membership in Active Directory can grant a user appropriate roles across vCenter, NSX, VCF Operations, and VCF Automation simultaneously, provided role assignments are configured in each component.

### Sources
- https://techdocs.broadcom.com/us/en/vmware-cis/nsx/vmware-nsx/4-2/administration-guide/authentication-and-authorization/role-based-access-control.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/fleet-management/what-is/setting-up-sso/connect-components.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vvs/1-0/identity-and-access-management-for-vmware-cloud-foundation.html
- https://blogs.vmware.com/cloud-foundation/2025/08/05/security-vmware-cloud-foundation-9-0/

---

## 10. vSAN Encryption — At-Rest, VM Encryption, and In-Transit

### Data-at-Rest Encryption

vSAN data-at-rest encryption operates at the cluster level, encrypting all data as it is written to physical storage devices. The encryption uses a two-tier key hierarchy: Key Encryption Keys (KEKs) retrieved from a key management server (KMS) protect the Data Encryption Keys (DEKs) that actually encrypt the data. In VCF 9, a new wrapping-key approach reduces the accumulation of keys on the KMS by protecting KEKs rather than storing thousands of individual keys.

The vSphere Native Key Provider (NKP) is available as a built-in key management solution that operates entirely within vSphere and can leverage TPM chips on hosts for key persistence and resilience, eliminating the need for an external KMS in some scenarios.

In the vSAN Express Storage Architecture (ESA), encryption occurs higher in the I/O stack, immediately after compression. This means data is encrypted only once regardless of how many times it is read or moved within the storage layer, reducing CPU overhead and improving efficiency compared to the Original Storage Architecture (OSA).

### VM Encryption

vSphere VM Encryption is independent of vSAN and works with any storage backend. It encrypts VM files (VMDK, VMX, snapshots, swap) at the hypervisor layer using the VMkernel cryptographic module. VM Encryption and vSAN data-at-rest encryption can be used together for defense-in-depth, though they use different encryption paths. The VMkernel cryptographic module has achieved FIPS 140-2 and FIPS 140-3 validation.

### Data-in-Transit Encryption

vSAN data-in-transit encryption secures all vSAN traffic (data and metadata) transmitted between hosts in a cluster. As of VCF 9, this feature is supported for aggregated vSAN HCI clusters but is not supported when using vSAN storage clusters (disaggregated compute/storage). Data-in-transit encryption and data-at-rest encryption are configured independently and can be enabled in any combination.

### Sources
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/vsan-deployment-administration-and-monitoring/administering-vmware-vsan/using-encryption-in-a-vsan-cluster-1/vsan-data-at-rest-encryption/enable-data-at-rest-encryption-on-existing-vsan-cluster.html
- https://www.ntpro.nl/blog/archives/3831-vSAN-Encryption-Services-in-VMware-Cloud-Foundation-9.0.html
- https://virtualpad.wordpress.com/2026/03/04/quick-notes-about-vsan-encryption-in-vcf-9/
- https://www.vmware.com/docs/vmw-vsan-encryption-services
- https://blogs.vmware.com/cloud-foundation/2025/08/05/security-vmware-cloud-foundation-9-0/

---

## 11. Immutable Snapshots for Ransomware Recovery

### vSAN Data Protection

vSAN Data Protection provides native, snapshot-based protection for VCF workloads. Protection Groups define which VMs are protected and whether their snapshots should be immutable. When immutability is enabled via a simple checkbox in the protection group configuration, snapshots cannot be altered or deleted by any user or process, including administrators. This provides a foundational defense against ransomware that attempts to encrypt or destroy backup data.

The system supports up to 200 crash-consistent snapshots per VM, with vSAN-to-vSAN replication achieving Recovery Point Objectives as low as one minute. vSAN Data Protection is included in the VCF license for all environments running vSAN ESA.

### VMware Live Recovery Integration

vSAN Data Protection integrates with VMware Live Cyber Recovery (VLCR) for comprehensive ransomware protection and recovery. Key capabilities include:

**On-Premises Isolated Clean Room**: VCF 9 introduces the ability to perform cyber recovery operations entirely on-premises, addressing data sovereignty requirements for organizations that cannot send data to cloud-based recovery sites. An isolated clean room environment allows recovery validation without risking contamination of production systems.

**Accelerated Local Recovery**: A new capability allows invoking cyber recovery directly from local vSAN snapshots, achieving restore speeds up to 24 times faster than standard delta-based failback. This dramatically reduces Recovery Time Objectives for ransomware incidents.

**Enterprise DR Orchestration**: VMware Live Recovery provides orchestrated disaster recovery across a wide range of VCF-based topologies with enhanced vSphere replication for mission-critical applications.

### Recovery Workflow

The recovery model emphasizes that prevention alone is insufficient — organizations need tested recovery mechanisms. The workflow involves: maintaining a deep history of immutable snapshots, using snapshot analytics to identify the last known-clean restore point, spinning up an isolated clean room environment, validating the restore point, and then recovering to production.

### Sources
- https://blogs.vmware.com/cloud-foundation/2025/11/04/vsan-data-protection-in-vmware-cloud-foundation-the-solution-you-already-own/
- https://blogs.vmware.com/cloud-foundation/2025/06/17/vmware-live-recovery-vcf-9-0/
- https://blogs.vmware.com/cloud-foundation/2025/06/17/announcing-availability-of-vsan-9-0/
- https://blogs.vmware.com/cloud-foundation/2024/08/02/superior-snapshots-using-vmware-vsan-data-protection/
- https://www.vmware.com/docs/vcf-ransomware-recovery-solution-brief

---

## 12. Compliance Frameworks

### Supported Frameworks

VCF 9 provides compliance tooling and configuration guidance aligned with multiple regulatory frameworks:

| Framework | Key NSX/VCF Controls |
|-----------|---------------------|
| **NIST SP 800-53** | AC-7 (account lockout), SC-7 (boundary protection via DFW), SC-8 (transmission security via TLS), CM-6 (configuration baselines) |
| **PCI-DSS v4** | Requirement 2.2 (micro-segmentation via DFW to isolate Cardholder Data Environment at vNIC level), Requirement 8.2.1 (password complexity enforcement) |
| **HIPAA** | Technical safeguards for access controls, audit controls, transmission security, and encryption of ePHI at rest and in transit |
| **ISO 27001** | A.13.1.3 (network segregation independent of physical topology) |
| **FISMA/FedRAMP** | NIST-based control families with particular emphasis on SC-7 boundary protection |
| **DISA STIG** | Comprehensive Security Technical Implementation Guides for each VCF component |

### DISA STIG Coverage

VCF 9 ships with STIG documentation and tooling for the following components:
- ESXi hosts
- NSX Manager and networking components
- vCenter Server
- VCF Application (SDDC Manager / VCF Operations)
- Virtual Machines
- VCF Automation
- VCF Identity Broker
- VCF Operations

### Ansible Remediation

Starting with VCF 9, all Ansible roles for STIG remediation are consolidated into a single unified playbook. This playbook targets VCF appliances over REST APIs and configures any supported non-compliant controls automatically. The workflow is:

1. Run an initial audit using GOSS-based scanning to assess current compliance posture.
2. Review the compliance report identifying non-compliant controls.
3. Execute the Ansible playbook to remediate violations.
4. Re-run the audit to verify remediation success.

Additional tools available include Chef InSpec profiles for auditing, MITRE STIG Viewer compatibility, and PowerShell scripts for Windows-based components.

### Compliance Dashboard

VCF Operations provides a Security and Compliance interface where administrators can:
1. Select the applicable security configuration guide (e.g., NSX Security Configuration Guide).
2. Choose a compliance policy (default or custom).
3. Run assessments against the selected baseline.
4. Review non-compliant findings grouped by criticality.
5. Remediate violations according to published guidance.

The dashboard provides continuous monitoring and drift detection, automatically recalibrating security scores as configurations change.

### NSX-Specific Hardening Guidance

For NSX Manager, best-practice hardening includes: enforcing account lockout after three failed attempts with a 15-minute lockout period, requiring 12-character minimum passwords with complexity requirements, setting 15-minute session timeouts, disabling SSH except when operationally necessary, restricting management access to authorized networks, enforcing TLS 1.2 or higher (with TLS 1.3 as the VCF 9 default), deploying trusted CA certificates, implementing API rate limits, and using DRS anti-affinity rules for multi-node Manager deployments.

For T0/T1 gateways, hardening includes disabling unnecessary DHCP services, removing inactive interfaces, disabling multicast where not needed, enabling OSPF MD5 authentication, and configuring strict-mode unicast Reverse Path Forwarding.

### Sources
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/stig/9-0/vcf-stig-documentation/docs-tutorials-cloud-foundation-9x-product-vcf-application-remediate9-app-remediate9-app.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/stig/9-0/vcf-stig-documentation/docs-tutorials-cloud-foundation-9x-ansible-playbook-overview.html
- https://puneetsharma.blog/2025/11/27/part-2-vcf-9-0-nsx-security-hardening-regulatory-compliance/
- https://puneetsharma.blog/2025/11/04/part-1-vcf-9-0-management-plane-security-hardening-regulatory-compliance/
- https://blogs.vmware.com/cloud-foundation/2025/08/05/security-vmware-cloud-foundation-9-0/

---

## 13. Certificate Auto-Rotation and Management

### The Problem

Certificate management has traditionally been one of the most operationally burdensome tasks in VCF environments. Expired certificates cause management plane outages, and the CA/Browser Forum's direction toward shorter certificate lifespans (moving toward 47-day maximums) makes manual renewal impractical at scale.

### VCF 9 Auto-Renewal Architecture

VCF 9 introduces automatic certificate renewal, enabled through a toggle in the VCF Operations UI. The feature covers three categories of components:

- **Management components**: VCF Operations, VCF Automation
- **Infrastructure appliances**: vCenter Server, NSX Manager, SDDC Manager
- **ESXi hosts**

### Supported Certificate Authorities

Multiple CA options are available, each with different auto-renewal capabilities:

**Microsoft Active Directory Certificate Services**: Issues signed certificates for both management and infrastructure components, with full auto-renewal support. This is the most comprehensive option for enterprises already running Microsoft PKI.

**Fleet Management Appliance CA**: Part of VCF 9's fleet management layer, this acts as a CA for management components and supports auto-renewal.

**VMCA (VMware Certificate Authority)**: The CA embedded in vCenter Server. It automatically renews certificates for infrastructure components and ESXi hosts.

**Embedded OpenSSL CA (SDDC Manager)**: Supports auto-renewal for infrastructure components only (not management components). Available for environments that do not use Microsoft CA or the Fleet Management CA.

### Operational Improvements

Certificate replacement workflows have been improved to minimize management interface disruption, particularly for vCenter Server. New alerts in VCF Operations track upcoming certificate expirations, giving administrators visibility into the renewal pipeline.

Organizations using VMware-provided CAs (rather than enterprise PKI) must add the relevant CA certificates to administrator systems and mark them as trusted to avoid browser warnings.

### Management Transition

Certificate management has moved from SDDC Manager (whose UI is deprecated in VCF 9) to VCF Operations and the vSphere Client. SDDC Manager continues to run as a backend service, but day-2 certificate operations are performed through the new management interfaces.

### Sources
- https://blogs.vmware.com/cloud-foundation/2025/06/19/automatic-certificate-renewal-in-vcf-9/
- https://blogs.vmware.com/cloud-foundation/2025/09/18/10-vmware-cloud-foundation-9-enhancements-simplifying-your-day-2-operations/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/building-your-private-cloud-infrastructure/sddc-manager-workflows.html
- https://blogs.vmware.com/cloud-foundation/2025/08/05/security-vmware-cloud-foundation-9-0/

---

## Additional VCF 9 Security Notes

### Confidential Computing (Replacement for vSphere Trust Authority)

VCF 9 introduces support for AMD SEV-SNP (Secure Encrypted Virtualization - Secure Nested Paging) and Intel TDX (Trust Domain Extensions). These technologies provide hardware-based data-in-use encryption with per-VM encryption keys, protecting workload memory from access by the hypervisor, neighboring VMs, and even undiscovered vulnerabilities. This represents the modern replacement for the discontinued vSphere Trust Authority.

### TLS Defaults

TLS 1.3 is the default transport encryption protocol in VCF 9. TLS 1.2 remains available as a fallback, and a strict "NIST_2024_TLS_13_ONLY" profile option is available for environments that require it.

### Security Dashboard

VCF Operations includes a new security dashboard providing continuous monitoring of security controls and compliance postures, with drill-down capabilities for change auditing and drift detection across the entire VCF stack.

### Sources
- https://blogs.vmware.com/cloud-foundation/2025/08/05/security-vmware-cloud-foundation-9-0/
