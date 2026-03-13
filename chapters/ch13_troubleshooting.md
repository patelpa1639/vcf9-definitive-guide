# Chapter 13: Troubleshooting

Every VMware Cloud Foundation environment will eventually present you with a problem that demands more than a reboot and a prayer. Whether it is a lifecycle update that stalls at eighty percent, an NSX transport node that refuses to prepare, or a vSAN health check that lights up red at two in the morning, the difference between a brief incident and a prolonged outage comes down to methodology. In this chapter I share the systematic approach I use as a Technical Account Manager working with VCF deployments daily, the log locations you need to know by heart, the observability tools that VCF 9 puts at your fingertips, and the most common issues I see across NSX, vSAN, and lifecycle management -- along with the fixes that work.

---

## A Structured Troubleshooting Methodology

Panic is the enemy of resolution. Before you touch a single CLI, adopt a six-phase methodology that keeps you disciplined and prevents you from chasing ghosts.

### Phase 1: Define the Problem

Start by articulating the problem in one sentence. "The workload domain expansion failed" is not good enough. "Host commissioning for workload domain WLD-02 failed during NSX transport node preparation with a realization error on host esxi-05.lab.local" gives you a target. Gather the who, what, when, and where before you touch anything. Talk to whoever reported the issue, check the VCF Operations alerts timeline, and review recent change records. More than half the incidents I work begin with an unrecorded change -- a firewall rule, a DNS record deletion, an NTP drift nobody noticed.

### Phase 2: Gather Evidence

Collect logs, screenshots, and health check outputs before you start theorizing. In VCF 9, VCF Operations provides a centralized view of metrics, logs, and flows. Pull a Troubleshooting Workbench session immediately. Download SoS utility output from the management domain. Capture timestamps -- every timestamp you record now saves you thirty minutes of log correlation later.

### Phase 3: Hypothesize

With evidence in hand, form two or three plausible hypotheses. Rank them by likelihood and by how quickly you can test each one. A DNS resolution failure is faster to confirm or rule out than a subtle VXLAN encapsulation issue, so test DNS first.

### Phase 4: Test

Test one hypothesis at a time. Change one variable at a time. Document every test you perform and its result, even the negative results. If you change two things simultaneously and the problem resolves, you will never know which fix actually mattered -- and you may have introduced a new latent issue.

### Phase 5: Resolve

Apply the fix, confirm the resolution, and verify that no secondary issues were introduced. Run VCF Operations health checks, re-execute the failed workflow, and monitor for recurrence over the next hour at minimum.

### Phase 6: Document

This is the phase everyone skips, and it is the most valuable. Record the root cause, the resolution steps, the timeline, and any preventive measures. Update your internal runbook. If the issue maps to a Broadcom KB article, link to it. Future you -- or the colleague covering your on-call shift -- will be grateful.

---

## Log Locations for Every VCF 9 Component

Knowing where to look is half the battle. VCF 9 consolidates management under VCF Operations (the evolution of SDDC Manager), but the underlying components still write their own logs. Here is the definitive reference.

### VCF Operations (formerly SDDC Manager)

VCF Operations is your control plane. When a lifecycle workflow, domain operation, or credential rotation fails, start here.

| Log Path | Contents |
|---|---|
| `/var/log/vmware/vcf/domainmanager/domainmanager.log` | Domain creation, cluster expansion, host commissioning, workload domain lifecycle events. This is the single most important log in any VCF troubleshooting session. |
| `/var/log/vmware/vcf/lcm/lcm.log` | Lifecycle management operations: bundle downloads, pre-checks, upgrade orchestration, BOM compliance validation. |
| `/var/log/vmware/vcf/operationsmanager/operationsmanager.log` | VCF Operations platform events, health monitoring, alert processing. |
| `/var/log/vmware/vcf/commonsvcs/commonsvcs.log` | Shared services including certificate management, credential management, DNS and NTP validation. |
| `/var/log/vmware/vcf/sddc-support/` | Output directory for SoS utility support bundle collection. |
| `/var/log/vmware/vcf/vcf-sos/` | SoS utility execution logs and health check results. |

To collect all VCF Operations logs at once, use the SoS utility:

```bash
sudo /opt/vmware/sddc-support/sos --log-dir /var/log/vmware/vcf/sddc-support/
```

### VCF Installer

The VCF Installer appliance is used during initial bring-up and management domain deployment. Its logs are critical when Day 0 deployment fails.

| Log Path | Contents |
|---|---|
| `/var/log/vmware/vcf/bringup/bringup.log` | End-to-end bring-up orchestration, step-by-step deployment progress. |
| `/var/log/vmware/vcf/bringup/bringup-debug.log` | Verbose debug output for bring-up, including API call traces and full error stacks. |
| `/var/log/vmware/vcf/vcfinstaller/vcfinstaller.log` | VCF Installer appliance operations, imaging status, and validation results. |

When a bring-up fails, always check `bringup-debug.log` first. The standard `bringup.log` often truncates the actual error, while the debug variant gives you the full stack trace and the exact API call that failed.

### vCenter Server

| Log Path | Contents |
|---|---|
| `/var/log/vmware/vpxd/vpxd.log` | Core vCenter operations: inventory, tasks, events, DRS, HA. |
| `/var/log/vmware/vpxd/vpxd-alert.log` | Critical alerts and error conditions. |
| `/var/log/vmware/vsan-health/vmware-vsan-health-*.log` | vSAN health service operations and health check results. |
| `/var/log/vmware/sps/sps.log` | Storage Policy Based Management (SPBM) operations. |
| `/var/log/vmware/vmcam/vmcam.log` | vCenter certificate authority operations. |
| `/var/log/vmware/content-library/` | Content library sync and subscription logs. |

For vCenter diagnostic bundles, use the vSphere Client: navigate to **Menu > Administration > Support > Export Support Bundle**, or from the command line:

```bash
/usr/lib/vmware-vmon/vmon-cli --alldump
```

### ESXi Hosts

| Log Path | Contents |
|---|---|
| `/var/log/vmkernel.log` | Kernel-level events: storage, networking, driver issues, purple screens. |
| `/var/log/hostd.log` | Host agent operations: VM power state changes, configuration updates. |
| `/var/log/vobd.log` | VMkernel observations: threshold alerts, hardware events. |
| `/var/log/esxupdate.log` | Patch and update operations via vSphere Lifecycle Manager. |
| `/var/log/syslog.log` | General system log aggregating messages from multiple services. |
| `/var/log/nsx-syslog.log` | NSX data plane events on the host: DFW rules, transport node agent status. |

For ESXi support bundles, use the `vm-support` command:

```bash
vm-support -w /tmp/
```

Or collect via the vSphere Client under **Host > Monitor > Support > Export System Logs**.

### NSX Manager

| Log Path | Contents |
|---|---|
| `/var/log/syslog` | General system log and primary aggregation point for NSX services. |
| `/var/log/proton/nsxapi.log` | NSX API operations: policy changes, configuration pushes, edge deployments. |
| `/var/log/corfu/corfu.log` | Corfu distributed datastore: cluster consistency, data replication between NSX Manager nodes. |
| `/var/log/proton/nsx-management-plane.log` | Management plane operations: inventory sync, transport node communication. |
| `/var/log/nsx-audit.log` | Audit trail: user logins, configuration changes, API calls with user context. |
| `/var/log/policy/policy.log` | Declarative policy engine: intent-to-realization pipeline, policy push status. |

When troubleshooting NSX Manager cluster health, focus on `corfu.log` for split-brain or replication lag issues, and `nsxapi.log` for API timeouts.

### NSX Edge Nodes

| Log Path | Contents |
|---|---|
| `/var/log/syslog` | General system log. |
| `/var/log/edge/edge.log` | Edge routing and services: BGP sessions, NAT, load balancing. |
| `/var/log/datapath/datapath.log` | Data plane forwarding decisions, packet drops, encapsulation errors. |

To generate an NSX support bundle, use the NSX Manager UI: **System > Support Bundle > Download**, or the API:

```
POST https://<nsx-manager>/api/v1/administration/support-bundles?action=collect_support_bundles
```

### vSAN

vSAN does not have a single appliance log location. Its diagnostics are distributed across ESXi hosts and vCenter.

| Log Path / Command | Contents |
|---|---|
| `/var/log/vmkernel.log` (on each ESXi host) | vSAN I/O path events, disk errors, component state changes. |
| `/var/log/vobd.log` (on each ESXi host) | vSAN threshold alerts and hardware observations. |
| `esxcli vsan health cluster list` | Health check summary with green/yellow/red status for all tests. |
| `esxcli vsan debug resync summary` | Current resync progress, objects remaining, estimated completion. |
| `esxcli vsan debug object health summary get` | Object health state summary across the cluster. |
| `esxcli vsan debug disk list` | Disk health, congestion values, and I/O performance metrics. |

### VCF Automation (formerly vRealize Automation)

| Log Path | Contents |
|---|---|
| `/var/log/vmware/prelude/` | Automation service core logs. |
| `/var/log/vmware/vco/` | Orchestrator workflow execution logs. |
| `/var/log/vmware/catalog/` | Service catalog and entitlement processing. |

---

## Using Observability Workbench and VCF Operations for Networks

VCF 9 introduced a significant evolution in how operators triage issues. The Troubleshooting Workbench and VCF Operations for Networks replace the scattered, component-by-component approach of earlier releases with a converged observability experience.

### Troubleshooting Workbench

The Troubleshooting Workbench, accessible from VCF Operations, is where you create and manage active troubleshooting sessions. Think of it as a collaborative investigation board that correlates logs, metrics, and alerts across your entire VCF stack.

When you open the Workbench, you see a search bar, a list of active troubleshooting sessions, and recent searches. The power lies in its ability to let you parse, modify, process, and analyze logs collected from every VCF component using an advanced list of predefined search fields drawn from content packs and custom definitions.

A practical workflow looks like this:

1. An alert fires in VCF Operations indicating a VM performance degradation.
2. Open the Troubleshooting Workbench and create a new session scoped to the affected cluster and time window.
3. Correlate metrics (CPU ready, storage latency) with log entries from ESXi, vCenter, and vSAN during the same window.
4. Use the predefined search fields to filter for vSAN congestion events or DRS migration failures.
5. Identify root cause, document the session, and close.

The Workbench also integrates with Diagnostic Findings, a feature that automatically scans your environment against a library of over one hundred known issue signatures defined by Broadcom Technical Support. When the diagnostics framework detects a match, it surfaces the finding with a direct link to the relevant KB article and recommended remediation. This is proactive troubleshooting at its best -- issues found before users report them.

### VCF Operations for Networks

VCF Operations for Networks (the evolution of vRealize Network Insight / Aria Operations for Networks) provides end-to-end visibility across your virtual and physical network infrastructure. For troubleshooting, it answers the question every network engineer dreads: "Is it the network?"

Key capabilities for triage include:

- **Flow visibility**: See every network flow between VMs, including source, destination, port, protocol, and the physical path packets traverse. When a DFW rule blocks traffic that should be allowed, flow analysis shows you the deny action and the specific rule ID.
- **Path tracing**: Trace the complete path from VM to VM, through virtual switches, transport nodes, TEP tunnels, physical switches, and back. Identify where packet loss or latency is introduced.
- **Micro-segmentation planning**: Before you apply a new DFW policy, use flow data to validate that your rules will not break existing application communication patterns.
- **NSX deployment health**: Monitor NSX Manager cluster state, transport node status, and edge node capacity from a single dashboard.

A known issue to be aware of: if you upgraded VCF Operations for Networks from version 6.12 or earlier to 6.14 or later, custom adapter instances may be in a stopped state and NSX flows may not be visible. Verify your data source configurations show "Validated successfully" for both vCenter and NSX Manager sources after any upgrade.

---

## Common NSX Issues

NSX is the networking backbone of VCF, and it is also the component that generates the most support cases in my experience. Here are the issues I see repeatedly.

### Manager Cluster Health

The NSX Manager cluster runs on a Corfu distributed datastore. When a Manager node loses connectivity to its peers, Corfu cannot maintain quorum, and configuration changes stall. Symptoms include API timeouts, inability to save firewall rules, and transport nodes showing "Disconnected" status.

**Diagnosis**: SSH into each Manager node and check `/var/log/corfu/corfu.log` for replication errors. Run `get cluster status` from the NSX CLI to verify cluster health. Look for NTP drift between Manager nodes -- even a few seconds of skew can cause Corfu instability.

**Resolution**: Restore network connectivity between Manager nodes. If NTP drift is the cause, correct the NTP configuration and restart the nsx-cluster-manager service. In severe cases where a Manager node has been isolated for an extended period, you may need to remove and re-deploy it as a new cluster member.

### Transport Node Preparation Failures

This is the single most common NSX issue in VCF deployments. Transport node preparation installs the NSX kernel modules on ESXi hosts and configures the N-VDS or VDS for overlay traffic. When it fails, workload domain expansion halts.

**Common causes and fixes**:

- **ENS/Enhanced Data Path incompatibility**: Physical NICs in the host may not support Enhanced Networking Stack. Modify the Transport Node Profile under System > Fabric > Hosts > Transport Node Profiles and set Advanced Configuration to "Standard" instead of "Enhanced."
- **Stale NVDS references**: If a host was previously prepared for NSX with an NVDS that has since been removed, residual configuration blocks the new preparation. On the affected host, run `localcli network ip interface list` to identify stale references and clean them up.
- **Standard switch naming conflict**: A standard vSwitch with the same name as the VDS used for NSX preparation causes a collision. Rename the conflicting standard switch.
- **Realization errors with no NSX UI errors**: During VCF 9 installations, transport node preparation can fail with a realization error while the NSX interface shows no errors. Check the VCF Operations domainmanager.log for the actual failure detail -- the error is often a network connectivity issue between the ESXi host management VMkernel and the NSX Manager.
- **Expired certificates**: Transport nodes transition to "Failed" or "Disconnected" state when their certificates expire. Replace certificates using the NSX Manager API or UI under System > Certificates.

**General recovery approach**: In most cases, rebooting the affected ESXi host forces a resync with NSX Manager and resolves the preparation. If that fails, reboot the NSX Manager node that owns the host relationship.

### Overlay Connectivity Issues

When VMs on different hosts within the same NSX segment cannot communicate, the overlay tunnel is the suspect. Verify TEP (Tunnel Endpoint) connectivity by pinging between TEP IPs from the ESXi host CLI with the correct VLAN and MTU:

```bash
vmkping ++netstack=vxlan <remote-TEP-IP> -d -s 1572
```

If the ping fails, work through the physical network: verify the TEP VLAN is trunked on all intermediate switches and that the MTU supports the overlay overhead (minimum 1600 bytes, recommended 9000 for jumbo frames).

### Distributed Firewall Blocking Legitimate Traffic

When applications break after a DFW policy change, use VCF Operations for Networks flow analysis to identify which rule is dropping the traffic. The flow record shows the rule ID and action. Cross-reference the rule ID in NSX Manager under Security > Distributed Firewall. Common pitfalls include overly broad "deny all" rules applied at the wrong scope and rules that reference security groups with stale membership.

### T0/T1 Routing Issues

When north-south connectivity fails, check BGP session state on the Tier-0 gateway. From the NSX Edge CLI:

```
get logical-routers
vrf <T0-SR-VRF-ID>
get bgp neighbor summary
```

If BGP sessions are down, verify the physical router peering configuration, confirm the correct ASN, and check that the uplink interface IP is reachable. For Tier-1 route advertisement issues, confirm that the desired route types (connected, NAT, LB VIP) are enabled in the T1 route advertisement configuration.

---

## Common vSAN Issues

vSAN powers the storage layer across all VCF workload domains. Understanding its health check system and common failure modes is essential.

### Health Check Interpretation

vSAN health checks use a traffic-light system: green indicates passing, yellow indicates a warning that should be investigated, and red indicates a failure requiring immediate attention. Run `esxcli vsan health cluster list` from any ESXi host in the cluster, or use the vSphere Client under Cluster > Monitor > vSAN > Skyline Health.

Key health check categories to prioritize:

- **Network health**: Connectivity and MTU checks between vSAN VMkernel interfaces. A failing MTU check often indicates a physical switch misconfiguration where jumbo frames are not enabled end to end.
- **Data health**: Object health, component placement, and resync status. A degraded object means vSAN cannot satisfy the storage policy for that VM.
- **Physical disk health**: Disk capacity, congestion values, and hardware errors. A congestion value consistently above 20 indicates a performance bottleneck that will affect workloads.
- **Cluster membership**: Ensures all hosts in the vSphere cluster have vSAN enabled. A host can be in the cluster but not participating in vSAN if it was added via CLI and the vSAN service was not enabled.

### Resync Operations

When a host exits maintenance mode or recovers from a failure, vSAN resyncs objects to restore compliance with storage policies. Monitor resync progress with:

```bash
esxcli vsan debug resync summary
esxcli vsan debug resync list
```

The `resync list` output shows each object, the bytes remaining to resync, and the estimated size. A resync that appears stuck may indicate a network bottleneck on the vSAN VMkernel, a failing disk that cannot serve reads, or insufficient resources to place the component on available hosts.

Do not put additional hosts into maintenance mode while a resync is in progress. Doing so reduces the available capacity for component placement and can push objects into a non-compliant or even inaccessible state.

### Component Placement Failures

When vSAN cannot place a component to satisfy a storage policy, the object becomes non-compliant. Use `esxcli vsan debug object health summary get` to identify affected objects. Common causes include:

- Insufficient hosts to satisfy the Failures to Tolerate (FTT) policy. An FTT=1 RAID-1 policy requires at least three hosts with capacity.
- A fault domain configuration that restricts placement. If you use fault domains, each domain must have sufficient capacity.
- Disk group imbalance where one host's disk groups are full while others have space. vSAN tries to distribute components evenly, but extreme imbalance can block placement.

### Global Deduplication and Compression Entitlement

In VCF 9, vSAN deduplication and compression is available under the VCF entitlement, but it must be enabled per cluster and requires all-flash disk groups. A common support case arises when an administrator enables deduplication on a cluster with hybrid disk groups (SSD cache + HDD capacity), which is not supported and results in a configuration error. Verify your disk group configuration before enabling this feature. Also note that once enabled, disabling deduplication and compression requires a full data evacuation of each disk group, which is disruptive and time-consuming.

---

## VCF Operations Lifecycle Failures

Lifecycle management -- upgrading and patching the entire VCF stack -- is the operation most likely to fail in a VCF environment. The orchestration complexity is high, touching every component in a specific order, and a single pre-check failure can halt the entire workflow.

### Pre-Check Remediation

Before any upgrade, VCF Operations runs a battery of pre-checks. Common pre-check failures include:

- **NTP drift**: Hosts or appliances with time skew exceeding the threshold. Fix NTP configuration on the affected component and verify with `ntpq -p` or `chronyc sources`.
- **DNS resolution**: Forward and reverse DNS must resolve correctly for every component. A missing PTR record for a single ESXi host will fail the pre-check.
- **Certificate validity**: Expiring certificates on vCenter, NSX Manager, or ESXi hosts block the upgrade. Renew certificates before proceeding.
- **Password expiration**: Default password policies can cause component passwords to expire between the time you plan the upgrade and when you execute it. Verify and extend password expiration dates as needed.
- **Hardware compatibility**: The target BOM requires specific firmware and driver versions. Check the vSAN HCL and the VCF BOM compatibility matrix.

### Bundle Download Failures

When bundle downloads fail with "DepotConnectionFailureException" or "Downloading Bundle Failed," work through these checks:

1. **Network connectivity**: Verify that the VCF Operations appliance can reach the Broadcom depot (`depot.broadcom.com`) on port 443. Test with `curl -v https://depot.broadcom.com`.
2. **Proxy configuration**: If your environment uses an HTTP proxy, confirm that the proxy certificate is imported into the VCF Operations trust store. This is the most common cause of download failures in enterprise environments.
3. **Authentication**: VCF 9 requires authenticated downloads. Verify that your Broadcom Support Portal credentials are correctly configured in VCF Operations under Administration > Depot Settings.
4. **Stale bundle artifacts**: If a previous download partially completed, the residual files can block a retry. Delete the folder matching the bundle ID under `/nfs/vmware/vcf/nfs-mount/bundle/` and retry.
5. **LCM service restart**: As a last resort, restart the lifecycle management service: `systemctl restart lcm`. Allow up to fifteen minutes for the depot to reinitialize before retrying the download.

### Stuck Upgrades

An upgrade that appears stuck -- progress frozen at a percentage for an extended period -- requires careful investigation. Do not cancel or restart the workflow without understanding the current state.

1. Check `/var/log/vmware/vcf/lcm/lcm.log` for the last logged operation. The upgrade may be waiting for a host to exit maintenance mode, a vMotion to complete, or a vSAN resync to finish.
2. If the upgrade is stuck waiting on a vCenter task, check `vpxd.log` on the relevant vCenter for errors.
3. For NSX upgrades stuck on transport node upgrades, check the NSX Manager UI under System > Upgrade for per-host upgrade status. A single host failing its upgrade will block the entire upgrade group.
4. Never force-cancel an in-progress upgrade without guidance from Broadcom support. A partially applied upgrade can leave components in an inconsistent state that is harder to recover from than a stalled upgrade.

### BOM Compliance Check

VCF enforces a Bill of Materials (BOM) that specifies exact component versions. After an upgrade, if BOM compliance shows a drift, it typically means a component was not upgraded in the correct sequence or an async patch was applied outside the VCF lifecycle workflow. Use the VCF Operations UI to review the BOM compliance report, identify the non-compliant component, and apply the correct version through a targeted update.

---

## SupportAssist and Log Assist Workflow

When you need to engage Broadcom Technical Support, the speed of your case resolution depends heavily on the quality of the diagnostic data you provide. VCF 9 includes two integrated tools that streamline this process.

### Log Assist

Log Assist, accessible from VCF Operations under Infrastructure Operations > Diagnostics, automates the collection and upload of log bundles to the Broadcom Support Portal. Here is the workflow:

1. Navigate to VCF Operations > Diagnostics > Log Assist.
2. Select the components relevant to your issue (vCenter, ESXi hosts, NSX Manager, VCF Operations itself).
3. Log Assist validates three things: connectivity from the assigned collector to the selected component, connectivity from your environment to the Broadcom Support Portal, and that the integration permissions are sufficient for log download.
4. Initiate the bundle collection. Each unified collector supports up to five parallel support bundles. Additional components are queued.
5. Log Assist automatically attaches property-based and log-based diagnostic findings that are active at the time of collection. This means the support engineer receiving your bundle gets not just raw logs but also contextual diagnostic analysis.
6. The bundle is uploaded directly to your support case. Upload speed depends on your uplink bandwidth.

### SupportAssist

SupportAssist provides proactive monitoring and automated case creation for hardware and software issues. In VCF environments, it can detect failing components and open a support case before you even notice the problem. Configure SupportAssist through VCF Operations under Administration > SupportAssist, ensuring that your environment has outbound connectivity to the Broadcom telemetry endpoints.

### SoS Utility

The SoS (Supportability and Serviceability) utility remains available on the VCF Operations appliance for manual log collection and health checks. It is particularly useful when the GUI-based tools are inaccessible -- for example, when the VCF Operations appliance itself is the component experiencing issues.

```bash
# Collect all logs
sudo /opt/vmware/sddc-support/sos --log-dir /tmp/sos-output/

# Run health checks
sudo /opt/vmware/sddc-support/sos --health-check

# Collect specific component logs
sudo /opt/vmware/sddc-support/sos --domain <domain-name> --log-dir /tmp/sos-output/
```

---

## Top Support Resources

No one troubleshoots VCF in a vacuum. Here are the resources I rely on daily.

### Broadcom Knowledge Base

The Broadcom Knowledge Base at `knowledge.broadcom.com` is the authoritative source for known issues, workarounds, and resolution steps. Key articles to bookmark:

- **VMware Cloud Foundation Known Issues** (KB 415800): A master list of known issues across all VCF versions, updated regularly. Check this article first when you encounter an unexpected error.
- **Update Sequence for VCF 9.0** (KB 390634): The correct upgrade order for VCF 9 and compatible VMware products. Deviating from this sequence causes BOM compliance failures.
- **VCF Authenticated Downloads Configuration** (KB 390098): Instructions for configuring authenticated access to the Broadcom depot, which is mandatory for VCF 9 bundle downloads.

When searching the KB, use component-specific terms rather than generic descriptions. "NSX transport node preparation realization error" returns better results than "VCF host add failed."

### Broadcom TechDocs

The official VCF 9 documentation at `techdocs.broadcom.com` has been significantly restructured and improved. The troubleshooting sections for each component include step-by-step resolution guides. The Diagnostics Findings documentation lists every automated signature with its description and remediation steps.

### Community Forums

The Broadcom Community forums (formerly VMware Communities) remain a valuable resource for peer-to-peer troubleshooting. Many active contributors are VMware engineers and experienced administrators who share real-world solutions that have not yet made it into formal KB articles.

### William Lam's Blog

William Lam (`williamlam.com`), Distinguished Platform Engineering Architect in the VCF Division at Broadcom, maintains the most comprehensive independent resource for VCF. His "Ultimate Lab Resource for VCF 9.0" post is an essential reference for lab deployments and testing. His hardware considerations guide for VCF 9 has saved many administrators from compatibility surprises. When the official documentation leaves a gap, William's blog usually fills it with tested, practical guidance.

### Additional Community Resources

Several community blogs consistently produce high-quality VCF troubleshooting content:

- **lab2prod.com.au**: Maintains "The Unofficial VCF Troubleshooting Guide," a practical companion to the official documentation with real-world scenarios.
- **my-cloudy-world.com**: Detailed technical walkthroughs of VCF 9 features and architectural decisions.
- **lifeofbrianoc.com**: Focused VCF operational content including log location guides and upgrade procedures.
- **gibsonvirt.com**: Practical upgrade and patching process documentation with screenshots and tips.

### Engaging Broadcom Support

When you open a support case, include the following to accelerate resolution:

1. A clear problem statement with the exact error message.
2. The VCF version, including the patch level (for example, 9.0.2.0).
3. Log bundles collected via Log Assist or SoS utility, attached to the case before the first call.
4. A timeline of events leading up to the issue.
5. Any recent changes to the environment, including firmware updates, network changes, or configuration modifications.

The difference between a case that takes three days and one that takes three hours often comes down to how much diagnostic data you provide upfront.

---

## Summary

Troubleshooting VCF is not about memorizing every error message -- it is about having a system. Define the problem precisely. Gather evidence from the right logs. Use the Troubleshooting Workbench and VCF Operations for Networks to correlate across the stack. Form hypotheses and test them methodically. Resolve with confidence and document for the future.

The log locations in this chapter should be taped to your monitor until they become muscle memory. The common issues covered here represent the vast majority of what I see in production VCF environments. And when you reach the limit of what you can resolve independently, the support resources listed above -- combined with a well-prepared support case -- will get you to resolution.

VCF 9 has made troubleshooting meaningfully easier with integrated observability, automated diagnostic findings, and streamlined log collection. Take the time to learn these tools before you need them under pressure. The middle of an outage is the worst time to discover that your Log Assist integration is not configured or that your SoS utility output directory is full.

In the next chapter, we turn from fixing problems to preventing them, with a comprehensive guide to VCF operational best practices and Day 2 automation.
