# Chapter 6 --- Networking (NSX 9)

## Why Network Virtualization Is Non-Negotiable

If you have spent any meaningful time inside a data center that still relies on traditional VLAN-based networking, you already know the pain. Every new application deployment means a ticket to the network team. Every firewall change requires a change advisory board meeting. Every topology modification carries the risk of a fat-finger mistake that takes down half a pod. The infrastructure moves at the speed of the slowest approval chain, and in 2026, that is simply unacceptable.

Network virtualization decouples the logical network topology from the physical switching and routing fabric underneath. It does for networking what vSphere did for compute two decades ago: it turns a rigid, hardware-bound resource into a programmable, API-driven service that can be provisioned in seconds. VMware NSX, now at version 9 within VCF 9, is the engine that makes this possible.

But let me be direct about something I tell every customer during architecture workshops: network virtualization is not a "nice to have" capability you bolt on after deploying VCF. It is foundational. VCF 9 ships with NSX kernel modules pre-installed on ESXi. The installer provisions virtual networking capabilities during bring-up. The new VPC networking model---the headline feature of VCF 9---cannot function without NSX. Micro-segmentation, the single most effective defense against lateral movement in a compromised environment, is an NSX capability. Load balancing through Avi is orchestrated through NSX. Federation for multi-site stretching depends on NSX Global Manager.

In short, if you are deploying VCF 9, you are deploying NSX 9. This chapter will ensure you understand exactly what that means, how the architecture works, and how to design it properly from day one.

---

## NSX Architecture in VCF

### The Control Plane: NSX Manager

NSX Manager is the centralized management and control plane for the entire virtual networking and security stack. It exposes the REST API that SDDC Manager, vCenter, and your automation tooling consume. It maintains the desired-state configuration for every segment, gateway, firewall rule, and load balancer policy in the environment.

In VCF 9, NSX Manager deployment has been simplified in two important ways. First, you can now deploy a single NSX Manager appliance for environments where high availability of the management plane is not a strict requirement---lab environments, small edge deployments, or proof-of-concept installations. This reduces the resource footprint from three appliances (the traditional cluster) down to one. Second, licensing is handled through vCenter: you add your license file to vCenter, connect NSX Manager, and the key synchronizes automatically. No more juggling separate license portals.

For production environments, the recommendation remains unchanged: deploy a three-node NSX Manager cluster for full high availability. The cluster uses a Raft-based consensus algorithm, and losing a single node does not impact operations. Losing two nodes, however, will put the control plane into a read-only state. Plan your anti-affinity rules accordingly.

### Transport Nodes

A transport node is any node that participates in the NSX data plane. In VCF, this means your ESXi hosts and your NSX Edge appliances. When SDDC Manager adds a cluster to a workload domain, it applies a Transport Node Profile to each host in the cluster. This profile configures the host's vSphere Distributed Switch (VDS) for NSX, assigns Tunnel Endpoints (TEPs), and registers the host with the appropriate transport zones.

In VCF 9, the NSX kernel modules (VIBs) are bundled with ESXi by default. This is a significant operational improvement over previous releases where the VIBs had to be installed separately, often requiring a host reboot. With VCF 9, the host is NSX-ready from the moment ESXi is installed, and vSphere Lifecycle Manager (vLCM) handles ongoing updates in lockstep with vSphere patches---including live patching capabilities that can apply NSX updates without a full maintenance window.

### Transport Zones

A transport zone defines the reach of a logical network. It determines which transport nodes can communicate with each other over a given network type. NSX supports two types:

- **Overlay Transport Zone**: Spans all hosts that need to participate in GENEVE-encapsulated overlay networks. VCF supports a single overlay transport zone per NSX instance, and all vSphere clusters within and across workload domains sharing that NSX instance share the same overlay transport zone. This is by design: it ensures that overlay segments are reachable across the entire NSX domain.

- **VLAN Transport Zone**: Used for VLAN-backed segments and for Edge uplink connectivity to the physical network. You can configure one or more VLAN transport zones per VDS. VLAN transport zones are scoped more narrowly---typically aligned with the physical VLAN topology at the top-of-rack switch layer.

### Tunnel Endpoints and GENEVE Encapsulation

Every transport node receives one or more Tunnel Endpoint (TEP) IP addresses. A TEP is assigned to each physical NIC configured as an active uplink on the VDS. If your host has two physical NICs mapped to two uplinks, it will receive two TEP addresses. These TEPs are the source and destination for GENEVE-encapsulated traffic between hosts.

GENEVE (Generic Network Virtualization Encapsulation) is the overlay protocol NSX uses to create isolated, multi-tenant broadcast domains across the physical network fabric. It encapsulates the original Layer 2 frame inside a UDP packet with a GENEVE header that carries metadata, including the Virtual Network Identifier (VNI) that identifies which logical segment the traffic belongs to.

The physical network only needs to provide IP reachability between TEPs. It does not need to understand GENEVE. However, the physical network must support an MTU of at least 1700 bytes (1600 bytes for the inner frame plus the GENEVE overhead). In practice, I recommend setting the physical MTU to 9000 bytes (jumbo frames) across the entire fabric. This is standard practice in modern data center designs and eliminates any fragmentation concerns.

---

## T0/T1 Gateway Deep Dive

### The Role of Each Tier

The two-tier gateway model in NSX is one of the most elegant aspects of the architecture, and also one of the most frequently misunderstood. Let me clarify the roles unambiguously.

**Tier-0 (T0) Gateway**: This is the north-south boundary. It connects the NSX overlay world to the physical network. The T0 peers with your physical leaf switches using BGP, advertises routes for the overlay subnets, and receives default routes (or more specific routes) from the physical network. The T0 runs on NSX Edge appliances---dedicated VMs or bare-metal nodes that sit at the boundary between the virtual and physical networks.

**Tier-1 (T1) Gateway**: This is the tenant or application isolation boundary. A T1 provides routing, NAT, firewall, and load balancing services for a specific set of segments. T1 gateways connect upward to a T0 using an automatically provisioned internal transit link on the 100.64.0.0/10 address space. NSX Manager handles the route exchange between T1 and T0 automatically---you do not need to configure BGP or OSPF between them.

Think of the T0 as your data center's virtual border router, and the T1 as a virtual tenant router. You typically deploy one T0 per workload domain (sometimes shared across domains), and as many T1s as you have tenants, applications, or security zones that need isolation.

### Active-Active vs. Active-Standby

The T0 gateway is instantiated on NSX Edge nodes, and you choose between two HA modes:

**Active-Active**: Both Edge nodes forward traffic simultaneously. This is the mode you need for ECMP (Equal-Cost Multi-Path) routing, which distributes traffic across all Edge uplinks. In a typical two-Edge-node cluster with two uplinks per Edge, you get four BGP sessions to the physical leaf switches and four ECMP paths for both ingress and egress traffic. Active-active mode effectively doubles your north-south bandwidth compared to active-standby.

However, active-active mode has a critical constraint: stateful services such as NAT, stateful firewall on the gateway, and VPN cannot run in active-active mode because connection state would need to be synchronized between edges in real time. If you need these services on the T0, you must use active-standby.

**Active-Standby**: One Edge node handles all traffic while the other stands by. Failover is fast (typically under four seconds with BFD), and this mode supports all stateful services. The tradeoff is that you are not utilizing the bandwidth of the standby Edge until a failover occurs.

My standard recommendation for production environments: deploy the T0 in active-active mode for pure routing with ECMP, and push any stateful services (NAT, gateway firewall) down to the T1 level, where active-standby is the norm. This gives you maximum north-south throughput at the T0 while retaining stateful capabilities at the T1.

### BGP Peering with the Physical Network

The T0 gateway supports BGP as the dynamic routing protocol for peering with the physical infrastructure. In an ECMP deployment, each Edge node establishes eBGP sessions with the directly connected leaf switches. The typical design uses a distinct ASN for the NSX Edges and a distinct ASN for the physical leaf switches.

A few design considerations that come up in every engagement:

- **BFD (Bidirectional Forwarding Detection)**: Always enable BFD on the BGP sessions. BFD provides sub-second failure detection, which is critical for fast failover. Without BFD, BGP relies on hold timers that default to 180 seconds---far too slow for production environments.

- **AS-Multipath-Relax**: If your leaf switches have different ASNs (common in spine-leaf fabrics where each leaf has its own ASN), you must enable the AS-Multipath-Relax feature on the physical switches to allow ECMP across paths with different AS path attributes.

- **Inter-SR Routing**: When the T0 is in active-active mode, NSX establishes an iBGP session between the Service Router (SR) components on each Edge node. This Inter-SR routing ensures that if one Edge loses its uplink to the physical network but retains its TEP connectivity, traffic can still flow through the other Edge. It is a critical resiliency mechanism that is often overlooked during design.

- **Route Filtering**: Always configure prefix lists and route maps to control which routes are advertised and accepted. You do not want your NSX environment accidentally becoming a transit path for physical network traffic.

### T1 as an Isolation Boundary

The T1 gateway is where most of the per-tenant or per-application logic lives. Each T1 can have its own NAT rules, its own gateway firewall policies, its own DNS forwarding configuration, and its own set of connected segments. VMs on segments connected to one T1 cannot reach VMs on segments connected to a different T1 unless you explicitly allow it through routing and firewall rules.

This isolation model is powerful for multi-tenancy, compliance zoning, and blast-radius containment. In a VCF environment, I typically design one T1 per application tier (web, app, database) or one T1 per tenant, depending on the organizational model.

---

## NSX Segments: Overlay vs. VLAN-Backed

Segments are the Layer 2 broadcast domains in NSX. Every VM connects to a segment, and the segment determines how traffic is forwarded.

### Overlay Segments

Overlay segments are the bread and butter of NSX. They are created in the overlay transport zone and use GENEVE encapsulation to extend Layer 2 connectivity across the physical fabric without requiring any VLAN configuration on the physical switches. Each overlay segment receives a unique VNI, and traffic between VMs on different hosts is encapsulated in GENEVE and forwarded between TEPs.

Overlay segments can be connected to T1 or T0 gateways for Layer 3 routing. They support features like DHCP relay, ARP suppression, and IP discovery. In VCF 9, overlay segments are the default for workload connectivity, and the new VPC networking model (discussed later in this chapter) builds entirely on overlay segments.

### VLAN-Backed Segments

VLAN-backed segments map directly to a VLAN ID on the physical network. They are created in a VLAN transport zone and are typically used for specific purposes: Edge uplinks to the physical network, connectivity to physical appliances that cannot participate in the overlay, or brownfield migration scenarios where existing VLANs need to be preserved during the transition to NSX.

VLAN-backed segments do not use GENEVE encapsulation. Traffic on a VLAN-backed segment is forwarded as standard 802.1Q-tagged frames on the physical network. You can override the default teaming policy on a VLAN-backed segment to steer traffic to specific physical uplinks---useful for separating management traffic from workload traffic at the physical NIC level.

In VCF 9, you can also use Guest VLAN Tagging on segments, which allows VMs with 802.1Q-aware guest operating systems to send tagged frames that NSX maps to specific segments. This provides an additional layer of segmentation without requiring multiple vNICs per VM.

### When to Use Which

Use overlay segments for all new workload deployments. Use VLAN-backed segments only when you have a specific requirement that mandates it---physical appliance connectivity, regulatory constraints that require traffic inspection on the physical wire, or legacy integrations that cannot be refactored. The goal is to minimize your VLAN-backed footprint over time.

---

## Enhanced Data Path (EDP)

### Mechanism

Enhanced Data Path is NSX's high-performance forwarding engine. It replaces the standard kernel-level data path with an optimized fast path designed for maximum throughput, minimal latency, and reduced CPU overhead.

The mechanism works through flow-based processing. When a packet arrives at the virtual switch, the EDP checks a flow cache for a matching entry. The flow cache is indexed by the standard five-tuple: source IP, destination IP, source port, destination port, and protocol. If a match is found (a cache hit), the packet is forwarded immediately using the cached forwarding decision without traversing the full pipeline of lookup tables. If no match is found (a cache miss), the packet goes through the slow path, the forwarding decision is computed, and the result is installed in the flow cache for subsequent packets in the same flow.

Starting with NSX 4.x and continuing into NSX 9, EDP is available in two modes:

- **EDP-Standard**: Uses an interrupt-driven model. The host CPU handles packets when the NIC raises an interrupt. This mode provides a good balance between performance and CPU utilization for most workloads.

- **EDP-Performance**: Uses a polling-based model. Dedicated CPU cores continuously poll the NIC for packets, eliminating interrupt overhead entirely. This mode delivers the lowest possible latency and highest packet-per-second rates, but it consumes CPU cores even when there is no traffic. It is designed for latency-sensitive workloads where every microsecond matters.

### DPU Offload

The most significant advancement in NSX data path performance is the ability to offload the entire forwarding pipeline---including overlay encapsulation/decapsulation, distributed routing, distributed firewall rule evaluation, and flow tracking---from the host CPU to a Data Processing Unit (DPU).

VMware's vSphere Distributed Services Engine enables this offload architecture. ESXi, along with the NSX data plane components, runs on the DPU's embedded ARM cores (typically Arm Cortex-A72), freeing the host's x86 cores entirely for workload VMs. The DPU handles all network I/O at line rate without consuming any host CPU cycles.

The supported DPU platform is the NVIDIA BlueField family. BlueField-2 DPUs provide dual-port 25GbE or 100GbE connectivity with 8 Arm cores and up to 16 GB of onboard DDR4 memory. BlueField-3 DPUs push this to 400Gb/s with significantly more processing power and integrated cryptographic acceleration. Both connect via PCIe Gen 4 x16 and require a supplementary 75W power connector (the P-Series variants).

Two data path modes are available with DPU offload:

- **MUX Mode**: Provides higher flexibility and supports the full range of NSX features. The DPU acts as a transparent accelerator, and the host CPU can still participate in data path decisions when needed.

- **UPTV2 (Uniform Passthrough v2) Mode**: Provides higher raw performance by bypassing more of the host-side processing. It achieves the best throughput and latency numbers but may have feature parity limitations depending on the NSX release.

### The 3x Throughput Claim and When It Matters

VMware has published benchmarks showing up to 3x throughput improvement with DPU offload compared to software-only NSX processing. In testing with NVIDIA BlueField-2 DPUs, environments have demonstrated line-rate forwarding at 100Gb/s with firewall rules active---a result that would consume a significant portion of host CPU cores in a software-only configuration.

Where does this matter most?

- **AI Inference Workloads**: AI inference clusters generate enormous east-west traffic volumes as models are loaded, data is shuffled between nodes, and results are aggregated. Offloading NSX to DPUs ensures that network processing does not compete with GPU-bound inference workloads for CPU cycles.

- **Microservices Architectures**: Kubernetes-based microservices environments can generate millions of flows between pods. Each flow must traverse the distributed firewall. DPU offload keeps this processing off the application CPUs.

- **High-Frequency Trading (HFT)**: In financial services, every microsecond of latency is measurable in dollars. EDP-Performance mode on a DPU eliminates interrupt overhead and provides deterministic, low-latency packet processing.

- **High-Throughput Analytics**: Data lakes and analytics platforms that move terabytes of data between compute nodes benefit from line-rate overlay processing without CPU overhead.

### Hardware Requirements and Tradeoffs

Deploying DPU-based acceleration requires careful planning:

- **Server Compatibility**: The server must support DPU installation (PCIe Gen 4 x16 slot, supplementary power connector, BIOS support for Distributed Services Engine).
- **DPU Firmware**: The DPU firmware must match the ESXi and NSX versions. VMware maintains a Hardware Compatibility List (HCL) for supported DPU models and firmware versions.
- **Resource Overhead**: The DPU runs its own instance of ESXi and NSX components on its ARM cores. This is transparent to the administrator but means the DPU has a fixed resource footprint that cannot be reclaimed for workloads.
- **Cost**: BlueField DPUs are not inexpensive. The ROI analysis must weigh the cost of the DPU against the value of the x86 cores it frees up and the performance improvement it delivers.
- **Feature Parity**: Not all NSX features may be available in DPU-offloaded mode in every release. Always validate your feature requirements against the current NSX release notes.

My guidance: deploy DPUs in hosts that run network-intensive or latency-sensitive workloads. Do not deploy them universally across the entire VCF environment unless the workload profile justifies it.

---

## VPC Networking in VCF 9

### The Paradigm Shift

VPC networking is the single most important new capability in VCF 9 from a networking perspective. It brings the public cloud operating model---self-service, isolated, API-driven networking---to the private cloud. If you have used AWS VPCs or Azure VNets, the conceptual model will feel immediately familiar.

In previous VCF releases, networking was administrator-centric. A network administrator created segments, attached them to gateways, configured firewall rules, and assigned IP addresses. Application teams submitted requests and waited. VPC networking inverts this model: the infrastructure administrator defines the boundaries (IP spaces, external connectivity, security policies), and the application teams provision their own networks within those boundaries.

### IP Spaces

An IP Space is a defined pool of IP address ranges that can be allocated to VPCs. The administrator creates IP Spaces that represent the organization's address plan---for example, a 10.0.0.0/8 space for internal workloads or a 172.16.0.0/12 space for development environments. When a VPC is created, its subnets draw addresses from the assigned IP Space. NSX manages the IPAM automatically, eliminating the need for external IPAM tools or spreadsheet-based address tracking.

### VPC Subnets and Access Modes

Within a VPC, you create subnets. Each subnet has an access mode that determines its connectivity:

- **Public**: The subnet's prefix is advertised externally through the Transit Gateway and T0. Workloads on public subnets are reachable from the physical network. Use this for front-end services that need inbound connectivity.

- **Private-VPC**: The subnet is isolated within the VPC. Workloads can communicate with other subnets in the same VPC but cannot reach other VPCs or the external network. Use this for internal application tiers that should not be directly reachable.

- **Private-Transit Gateway (Private-TGW)**: The subnet routes traffic through the Transit Gateway. Workloads can communicate with subnets in other VPCs connected to the same Transit Gateway but are not advertised externally. Use this for shared services that need cross-VPC reachability without external exposure.

### Transit Gateway

The Transit Gateway is a new network construct introduced in VCF 9. It replaces the traditional T1-to-T0 chaining model with a simplified, centralized routing hub. When you create a workload domain and deploy Edge nodes, VCF configures the Transit Gateway automatically.

The Transit Gateway serves as the interconnection point between VPCs. VPCs that need to communicate with each other attach to the Transit Gateway, which handles route exchange between them. The Transit Gateway also provides the north-south path to the physical network through the T0 gateway.

VCF 9 supports two Transit Gateway deployment models:

- **Centralized Transit Gateway (CTGW)**: NSX Edge VMs host the T0 gateway and provide north-south connectivity. All external traffic flows through the Edge nodes. This is the traditional model and is required when you need stateful services (NAT, VPN) at the border.

- **Distributed Transit Gateway (DTGW)**: External connectivity is achieved without Edge VMs. Each ESXi host connects directly to the physical network using VLAN-backed uplinks, and the distributed router on each host handles routing to the external network. This model eliminates the Edge VM bottleneck and is ideal for environments where stateful border services are handled by external devices (physical firewalls, external load balancers).

### External Connectivity

External connectivity in the VPC model works through the Transit Gateway and T0:

1. A VPC subnet with Public access mode has its prefix advertised through the Transit Gateway to the T0.
2. The T0 advertises the prefix to the physical leaf switches via BGP.
3. Return traffic follows the BGP-learned routes back to the T0, through the Transit Gateway, and down to the VPC subnet.

For Distributed Transit Gateway deployments, the ESXi hosts advertise routes directly to the physical network, and external traffic flows directly to the host without traversing an Edge VM.

### PowerCLI and Automation

VPC networking in VCF 9 is fully API-driven, and VMware provides PowerCLI cmdlets for common operations. While the full cmdlet reference is extensive, here are representative examples of the workflow:

```powershell
# Create an IP Space
New-NsxIpSpace -Name "Production" -CidrBlock "10.100.0.0/16"

# Create a VPC
New-NsxVpc -Name "App-Team-Alpha" -IpSpaceName "Production" -TransitGateway "TGW-WLD01"

# Create a subnet within the VPC
New-NsxVpcSubnet -VpcName "App-Team-Alpha" -Name "Web-Tier" -AccessMode "Public" -SubnetSize 24

# Create a private subnet
New-NsxVpcSubnet -VpcName "App-Team-Alpha" -Name "DB-Tier" -AccessMode "Private-VPC" -SubnetSize 24
```

The NSX Policy API also supports Terraform providers and Ansible modules, enabling infrastructure-as-code workflows that integrate VPC provisioning into CI/CD pipelines.

### Comparison to AWS VPC

The naming is intentional. VMware designed VPC networking to provide conceptual parity with the public cloud:

| Concept | AWS VPC | VCF 9 VPC |
|---------|---------|-----------|
| Isolated network boundary | VPC | VPC |
| Address pool | CIDR block | IP Space |
| Network segment | Subnet | VPC Subnet |
| Inter-VPC routing | Transit Gateway | Transit Gateway |
| Internet/external access | Internet Gateway | T0 via CTGW or DTGW |
| Route table | Route Table | Managed automatically by NSX |
| Security rules | Security Groups | DFW Security Groups |

The key difference is that VCF VPC networking runs entirely on-premises, on infrastructure you control, with no dependency on a cloud provider. The security, performance, and data sovereignty advantages are obvious.

---

## Micro-Segmentation and Zero Trust

### The Distributed Firewall (DFW)

The NSX Distributed Firewall is arguably the most operationally significant feature in the entire NSX stack. Unlike traditional firewalls that sit at network chokepoints and inspect only traffic that crosses a boundary, the DFW is embedded in the hypervisor kernel on every transport node. Every packet leaving or entering a VM passes through the DFW before it hits the virtual switch. This means the DFW can enforce policy on east-west traffic between VMs on the same host, the same segment, or across segments---traffic that a traditional perimeter firewall never sees.

The DFW evaluates rules based on a first-match model. When a packet matches a rule, that rule's action (allow, drop, reject) is applied, and no further rules are evaluated. This makes rule ordering critical: more specific rules must precede more general rules.

### Security Groups and Tags

The DFW operates on groups, not on IP addresses. This is a fundamental design principle. Instead of writing rules like "allow 10.1.2.5 to 10.3.4.6 on port 443," you write rules like "allow group Web-Servers to group App-Servers on port 443." Groups are defined by membership criteria, and the most powerful criterion is the NSX tag.

Tags are key-value pairs attached to VMs, segments, or other objects. When a VM is tagged with "app:web" and "env:production," it automatically becomes a member of any group whose membership criteria match those tags. This decouples firewall policy from IP addresses, VLAN assignments, and physical topology. VMs can move between hosts, segments, and even data centers, and their security policy follows them because the policy is bound to the tag, not the address.

In practice, I recommend a tagging taxonomy that includes at least three dimensions:

- **Application**: Which application does this VM belong to? (e.g., app:erp, app:crm)
- **Tier**: What role does this VM play? (e.g., tier:web, tier:app, tier:db)
- **Environment**: What lifecycle stage? (e.g., env:prod, env:dev, env:staging)

This three-dimensional taxonomy enables extremely granular policies while remaining manageable. You can write a rule that allows the ERP web tier in production to talk to the ERP app tier in production, and that rule will automatically apply to any VM that matches those criteria---regardless of where it runs.

### Rule Ordering and Policy Categories

NSX organizes DFW policies into categories that are evaluated in a fixed order:

1. **Ethernet**: Layer 2 rules (e.g., ARP filtering). Evaluated first.
2. **Emergency**: Break-glass rules for incident response. High priority.
3. **Infrastructure**: Rules for shared infrastructure services (DNS, NTP, AD, DHCP).
4. **Environment**: Broad zoning rules (e.g., production cannot talk to development).
5. **Application**: Application-specific micro-segmentation rules. Most granular.

Within each category, policies are evaluated top-to-bottom, and within each policy, rules are evaluated top-to-bottom. The first match wins. This hierarchical structure makes it possible for different teams to manage different policy categories without stepping on each other's rules.

### Intrusion Detection and Prevention (IDPS)

NSX IDPS brings signature-based and behavior-based threat detection directly into the distributed firewall. It inspects traffic inline at every VM boundary, detecting and optionally blocking known exploit patterns, malware callbacks, and protocol anomalies.

The IDPS signature database is updated regularly from VMware's threat intelligence feed. You can also import custom signatures in Suricata-compatible format. IDPS can be configured per policy, per group, or globally, and it supports detect-only mode (IDS) for initial deployment and prevent mode (IPS) once you have tuned the signatures and are confident in the rule set.

The combination of DFW micro-segmentation and inline IDPS creates a defense-in-depth posture that is extremely difficult for attackers to navigate. Even if an attacker compromises a VM, they cannot move laterally without triggering firewall blocks and IDPS detections.

---

## NSX Federation and Multi-Site

### Global Manager Architecture

NSX Federation enables centralized management of multiple NSX deployments across geographically distributed sites. The architecture introduces two roles:

- **Global Manager (GM)**: A dedicated NSX Manager appliance (deployed as a cluster for HA) that provides a single pane of glass for cross-site configuration. The GM does not manage transport nodes directly---it pushes configuration to the Local Managers.

- **Local Manager (LM)**: The standard NSX Manager cluster at each site. The LM manages the transport nodes, Edge appliances, and data plane at its site. It receives cross-site configuration from the GM and merges it with its local configuration.

The GM-to-LM communication uses a secure channel, and the LMs continue to operate independently if the GM becomes unavailable. This is a critical resilience property: a GM outage does not impact data plane forwarding or local management operations at any site.

### Stretched Segments

Federation's most visible capability is stretched segments: overlay segments that span two or more sites. A VM on a stretched segment at Site A can communicate at Layer 2 with a VM on the same stretched segment at Site B, as if they were on the same physical LAN. The traffic is encapsulated in GENEVE and forwarded between the sites over an inter-site transport network (typically a dedicated L3 link or a dark fiber connection).

Stretched segments are essential for:

- **Workload mobility**: vMotion or SRM-based failover between sites without IP address changes.
- **Active-active data centers**: Applications that run simultaneously at both sites with the same network identity.
- **Disaster recovery**: Pre-provisioned network connectivity at the DR site that activates instantly during failover.

Design consideration: stretched segments carry the risk of split-brain scenarios and broadcast storms that span the WAN. Use them judiciously, only for workloads that genuinely require Layer 2 adjacency across sites. For everything else, use Layer 3 routing between sites.

### VCF Fleet and Federation

VCF 9 introduces the concept of Fleet-level management, where a Fleet Manager orchestrates operations across multiple VCF instances. NSX Federation is the networking backbone for this capability. Fleet-level management components---the Fleet Manager itself, the global vCenter instances, shared services---can be deployed on NSX overlay segments that are stretched between VCF instances using Federation.

The documented design pattern is:

1. Deploy Global Manager at the primary site.
2. Register each VCF instance's NSX Local Manager with the Global Manager.
3. Create stretched segments for fleet-level component connectivity.
4. Deploy fleet-level VMs on the stretched segments, enabling IP mobility between sites if a primary site failure requires relocation.

This design provides resilience for the fleet management plane itself, ensuring that a single-site failure does not orphan the entire fleet.

---

## Avi Load Balancer

### Architecture: Controller and Service Engines

The Avi Load Balancer (formerly NSX Advanced Load Balancer) is the integrated application delivery solution in VCF. Its architecture separates the control plane from the data plane:

- **Avi Controller**: A cluster of three controller VMs that provides centralized management, analytics, and orchestration. The Controller communicates with vCenter and NSX Manager to discover compute and network resources, and it automates the lifecycle of Service Engines. The Controller also provides a rich analytics dashboard with real-time metrics, logs, and end-to-end request tracing.

- **Service Engines (SEs)**: The data plane VMs that handle actual traffic processing. SEs are deployed, scaled, and managed automatically by the Controller. When you configure a new Virtual Service (VIP), the Controller provisions SEs (or reuses existing ones), places them in the correct port groups, downloads the appropriate configuration, and begins processing traffic.

SEs are organized into SE Groups, which define their sizing (CPU, memory), HA mode (active-active, N+M, active-standby), and placement constraints. You can create different SE Groups for different tiers of service---for example, a high-performance SE Group with dedicated resources for production applications and a shared SE Group for development workloads.

### NSX Cloud Mode

When Avi is deployed in NSX Cloud mode, it uses the NSX Cloud Connector to integrate directly with NSX Manager. This integration provides:

- **Automated SE placement**: SEs are deployed on NSX segments with the correct security tags and group memberships.
- **Dynamic pool membership**: Backend server pools can be populated automatically based on NSX security groups or tags, eliminating manual server registration.
- **Consistent security policy**: DFW rules apply to SE traffic just as they do to any other VM, ensuring that load-balanced traffic passes through micro-segmentation enforcement.

### Layer 7 Capabilities

Avi provides full Layer 7 load balancing with:

- **HTTP/HTTPS content switching**: Route requests to different backend pools based on URI path, hostname, headers, or cookies.
- **SSL/TLS offload**: Terminate SSL/TLS at the SE, decrypting traffic before forwarding to backends. This offloads the cryptographic processing from application servers and centralizes certificate management.
- **Web Application Firewall (WAF)**: Inline WAF based on the ModSecurity Core Rule Set, protecting applications from OWASP Top 10 vulnerabilities.
- **Rate limiting and connection management**: Protect backends from traffic surges and slow client connections.
- **HTTP/2 and gRPC support**: Native support for modern application protocols.

### Global Server Load Balancing (GSLB)

GSLB extends load balancing across multiple sites. One Avi Controller cluster serves as the GSLB leader, and Controller clusters at other sites serve as followers. The leader maintains the global configuration and health status, and all sites synchronize automatically.

GSLB works by responding to DNS queries with the IP address of the optimal site based on configurable criteria: geographic proximity, site health, active connections, or custom weights. It supports active-active (traffic distributed across sites), active-standby (DR failover), and weighted distribution models.

In a VCF multi-site deployment with NSX Federation, GSLB provides the application-layer complement to network-layer stretched segments: where Federation provides Layer 2/3 connectivity between sites, GSLB provides intelligent Layer 7 traffic distribution.

---

## VCF Operations for Networks

### Why Visibility Matters

You cannot secure what you cannot see, and you cannot troubleshoot what you cannot map. VCF Operations for Networks (the evolution of vRealize Network Insight / Aria Operations for Networks) provides deep visibility into both the virtual and physical network fabric.

### Flow Analysis

Flow analysis captures and analyzes network flows across the entire VCF environment. It ingests data from multiple sources:

- **NSX flow data**: IPFIX exports from the distributed firewall and distributed router on every host.
- **Physical flow data**: NetFlow v5/v7/v9 and sFlow from physical switches and routers.
- **VM-level metrics**: Network utilization, packet rates, and error counts per vNIC.

Flow Insights identifies top talkers, abnormal traffic patterns, analytics outliers, and potential security issues. It can detect DDoS attack patterns, misconfigured firewall rules (flows that should be blocked but are not), and unused firewall rules (rules that no traffic ever matches, indicating policy bloat).

For micro-segmentation planning, flow analysis is invaluable. Before writing a single DFW rule, you can use flow data to understand the actual communication patterns between applications. VCF Operations for Networks can generate recommended firewall rules based on observed traffic, dramatically accelerating the path from zero segmentation to a fully enforced micro-segmented environment.

### Topology Mapping

The topology mapping engine discovers and visualizes the complete network path between any two endpoints---from VM to vNIC to virtual switch to TEP to physical switch to physical router and back. It shows:

- **Overlay paths**: The GENEVE tunnel path between source and destination TEPs.
- **Underlay paths**: The physical switch hops the encapsulated traffic traverses.
- **Redundant paths**: ECMP paths, backup paths, and failover topology.
- **Object relationships**: Which VMs are on which segments, which segments are connected to which gateways, which gateways peer with which physical routers.

Topology maps can display both logical (NSX constructs) and physical (switch/router) views, and they overlay health status on each component. A failed physical link shows up as a red highlight on the topology map, immediately showing the blast radius of the failure.

### Troubleshooting Workflows

VCF Operations for Networks includes guided troubleshooting workflows that walk operators through a structured diagnostic process:

1. **Symptom identification**: Select the type of problem (connectivity failure, performance degradation, security policy violation).
2. **Scope definition**: Identify the affected VMs, segments, or applications.
3. **Automated analysis**: The system maps dependencies, checks component health, analyzes flow data, and compares current state against known-good baselines.
4. **Root cause identification**: The system highlights probable causes with supporting evidence---a failed TEP, a misconfigured firewall rule, an MTU mismatch, a BGP session flap.
5. **Remediation guidance**: Suggested actions to resolve the issue, with links to the relevant NSX Manager or vCenter UI for implementation.

These workflows convert what used to be a multi-hour, multi-tool, multi-team troubleshooting exercise into a guided, data-driven process that a single operator can execute in minutes.

### Heat Maps and Anomaly Detection

VCF Operations for Networks generates heat maps that show network utilization, error rates, and latency across the environment. Anomaly detection uses machine learning to establish baseline behavior for each network path and alert when deviations occur---before users notice a problem.

This proactive monitoring capability is particularly valuable in large VCF deployments where the sheer number of segments, gateways, and flows makes it impossible for human operators to maintain situational awareness through manual monitoring.

---

## Bringing It All Together

NSX 9 in VCF 9 is not simply a networking product---it is the nervous system of the entire private cloud platform. Every workload that runs, every packet that flows, every security policy that protects, and every multi-site design that stretches across geography depends on NSX.

The key architectural decisions you need to make early in your VCF 9 deployment:

1. **T0 HA mode**: Active-active with ECMP for maximum bandwidth, pushing stateful services to T1. Or active-standby if you need NAT and VPN at the border.

2. **VPC vs. Segment networking**: VPC networking is the future. If you are building a greenfield VCF 9 environment, design for VPCs from day one. Segment networking remains fully supported for brownfield migrations and workloads that do not fit the VPC model.

3. **Transit Gateway model**: Centralized (with Edge VMs) for environments that need stateful border services. Distributed (without Edge VMs) for environments where stateful services are handled externally and you want to eliminate the Edge bottleneck.

4. **DPU offload**: Deploy DPUs for hosts running network-intensive, latency-sensitive, or AI/ML workloads. Do not deploy universally unless cost analysis supports it.

5. **Federation scope**: Use Federation for multi-site designs that require stretched segments or consistent cross-site security policy. Do not stretch segments unless Layer 2 adjacency is genuinely required.

6. **Micro-segmentation strategy**: Start with flow analysis in VCF Operations for Networks, develop your tagging taxonomy, implement DFW policies in monitor-only mode, validate, then enforce.

7. **Load balancing tier**: Deploy Avi in NSX Cloud mode for tight integration. Size SE Groups based on throughput requirements. Plan GSLB early if multi-site application delivery is in scope.

These decisions are interconnected. Your T0 design affects your Transit Gateway model. Your VPC design affects your micro-segmentation approach. Your Federation design affects your GSLB topology. Treat NSX design as a holistic exercise, not a collection of independent feature configurations.

In the next chapter, we will turn our attention to storage---the other foundational pillar of VCF---and examine how vSAN 9 delivers the performance, resilience, and efficiency that modern workloads demand.
