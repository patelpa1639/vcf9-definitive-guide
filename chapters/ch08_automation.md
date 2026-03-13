# Chapter 8 --- Automation and APIs

## Why Automation Is No Longer Optional

If you manage fewer than a dozen ESXi hosts and a single workload domain, you might still get away with clicking through the SDDC Manager UI for every lifecycle operation. But the moment your VCF footprint grows to multiple workload domains, hundreds of hosts, and thousands of virtual machines -- the moment you layer NSX VPCs, vSAN stretched clusters, and VCF Automation tenancies on top -- manual operations become the bottleneck. They are slow. They are error-prone. And they simply do not scale.

Automation in VMware Cloud Foundation 9 is not an afterthought bolted onto the side of the platform. It is an integral engineering priority. With VCF 9.0, Broadcom shipped a renamed and rewritten PowerCLI module, a unified SDK for Python and Java developers, OpenAPI 3.0 specifications across roughly ninety percent of VCF APIs, a modern VI JSON wire protocol for vSphere management, first-party Terraform providers, STIG remediation playbooks for Ansible, and a GitOps-native delivery pipeline powered by Argo CD. The message is clear: if you are not automating your VCF environment, you are operating it wrong.

### Two Personas, One Platform

Throughout this chapter I will address two distinct personas. The first is the **infrastructure administrator** -- the VI admin, the network engineer, the storage architect -- who needs to provision workload domains, apply firmware updates, enforce security baselines, and keep the lights on. The second is the **developer or platform engineer** who consumes the infrastructure, deploying applications into Kubernetes namespaces or virtual machines through self-service portals, CI/CD pipelines, and declarative manifests.

VCF 9 serves both audiences. PowerCLI and the SDDC Manager REST API are the administrator's power tools. Terraform providers, the VCF SDK, and Argo CD are the developer's instruments. VCF Automation (VCFA) sits in the middle, bridging both worlds with its Provider Portal and Organization Portal workflows. Understanding all of these layers -- and knowing when to reach for each one -- is what separates a competent VCF operator from an exceptional one.

---

## VCF PowerCLI 9.0: A Ground-Up Reimagining

### The Name Change

The first thing you will notice is the name. What was formerly `VMware.PowerCLI` is now `VCF.PowerCLI`. This is not a cosmetic rebranding. The rename reflects a deliberate decision by Broadcom engineering to align the PowerCLI release cadence and versioning with VMware Cloud Foundation itself. VCF.PowerCLI 9.0 ships alongside VCF 9.0 and is tested against the exact same bill of materials. Going forward, PowerCLI version numbers will track VCF releases, eliminating the historical confusion around which PowerCLI version supports which vSphere version.

Installation is straightforward:

```powershell
# Remove the legacy module if present
Get-Module VMware.PowerCLI -ListAvailable | Uninstall-Module -Force

# Install VCF PowerCLI 9.0 from the PowerShell Gallery
Install-Module -Name VCF.PowerCLI -Scope CurrentUser -AllowClobber

# Verify the installation
Get-Module VCF.PowerCLI -ListAvailable
```

### The 70% Performance Improvement

Behind the scenes, the VCF PowerCLI engineering team rewrote many of the core cmdlets as .NET-compiled classes rather than interpreted PowerShell script functions. The difference is substantial. Compiled cmdlets run directly within the .NET runtime, benefiting from Just-In-Time (JIT) compilation, optimized memory allocation, and more efficient pipeline execution.

On a Windows Server 2025 host running PowerShell 7.x, importing the old `VMware.PowerCLI` module took well over sixty seconds. Importing `VCF.PowerCLI` completes in roughly eighteen seconds -- an improvement of up to 70 percent. Even on legacy PowerShell 5.1, load times improved by more than 50 percent. The repackaging of cmdlets into fewer sub-modules also reduces I/O overhead, because the runtime loads fewer assemblies into memory.

For automation scripts that run on a schedule -- nightly compliance checks, hourly health probes, or event-driven remediation -- that forty-second savings on every invocation compounds rapidly.

### The Initialize / Invoke SDK Pattern

VCF PowerCLI 9.0 introduces auto-generated SDK modules that provide one-to-one bindings with every VCF REST API endpoint, derived directly from the OpenAPI specification. These SDK cmdlets follow a consistent two-verb pattern:

- **`Initialize-*`** cmdlets construct the data structures (request bodies, parameter objects) on the client side. They never communicate with the API server.
- **`Invoke-*`** cmdlets execute the actual operation against the server, sending the prepared payload over the wire.

This separation of concerns is deliberate. It allows you to build up complex request objects incrementally, inspect them before submission, and reuse them across multiple calls. Here is a concrete example that creates a virtual machine through the SDK:

```powershell
# Connect to vCenter
Connect-VIServer -Server vcenter01.lab.local

# Step 1: Initialize the placement spec
$placement = Initialize-VmPlacementSpec `
    -Folder "group-v1234" `
    -ResourcePool "resgroup-5678" `
    -Datastore "datastore-91011"

# Step 2: Initialize the VM create spec
$vmSpec = Initialize-VmCreateSpec `
    -Name "web-server-01" `
    -GuestOS "RHEL_9_64" `
    -Placement $placement

# Step 3: Invoke the creation
Invoke-CreateVm -CreateSpec $vmSpec
```

Not every vSphere or VCF API operation has a high-level cmdlet like `New-VM`. When Broadcom introduces a new API in a dot-release -- say, a new vSAN health check endpoint -- the auto-generated SDK cmdlets are available immediately, because they are generated from the published OpenAPI spec. You do not have to wait for a dedicated high-level cmdlet to be written.

### Discovery Helpers

With hundreds of auto-generated cmdlets, discoverability matters. VCF PowerCLI includes helper commands to explore available operations:

```powershell
# List all SDK cmdlets for SDDC Manager
Get-Command -Module VMware.Sdk.Vcf.SddcManager

# Find all Initialize cmdlets related to clusters
Get-Command Initialize-*Cluster* -Module VMware.Sdk.Vcf.SddcManager

# Get detailed help for a specific cmdlet
Get-Help Invoke-GetClusterByIdSddcManager -Detailed
```

### New Modules: NSX and SDDC Manager

VCF PowerCLI 9.0 adds two entirely new SDK modules:

**VMware.Sdk.Nsx.Policy** provides cmdlets for the NSX Policy API, including the new VPC constructs introduced in VCF 9. You can create and manage NSX Projects, VPCs, subnets, and security policies directly from PowerShell. The existing VM network adapter cmdlets (`Get-NetworkAdapter`, `Set-NetworkAdapter`) have been updated to accept VPC subnet objects, so you can attach a virtual machine to a VPC subnet in a single pipeline:

```powershell
# Retrieve a VPC subnet from NSX
$subnet = Invoke-GetVpcSubnet -VpcId "vpc-prod-01" -SubnetId "subnet-web-tier"

# Attach a VM's network adapter to the VPC subnet
Get-VM "web-server-01" | Get-NetworkAdapter | Set-NetworkAdapter -VpcSubnet $subnet -Confirm:$false
```

**VMware.Sdk.Vcf.SddcManager** provides cmdlets that wrap the SDDC Manager public API. This module is particularly valuable for lifecycle automation:

```powershell
# Connect to SDDC Manager
Connect-VcfSddcManager -Server sddc-mgr.lab.local -User admin@local -Password $securePass

# Retrieve all workload domains
Invoke-GetDomainsSddcManager

# Query a specific host's commission status
Invoke-GetHostSddcManager -HostId "host-uuid-1234"

# Check the current bundle availability for lifecycle updates
Invoke-GetBundlesSddcManager
```

---

## Unified VCF SDK for Python and Java

### The Problem It Solves

Before VCF 9, VMware's SDK landscape was fragmented. Python developers had to juggle `pyvmomi` for the VI SOAP API, `vSphere Automation SDK for Python` for the REST API, and separate packages for NSX, vSAN, and SDDC Manager. Java developers faced a similar sprawl across multiple Maven artifacts. Each SDK had its own authentication model, its own error handling conventions, and its own release schedule.

VCF SDK 9.0 consolidates everything into a single deliverable per language:

- **Python**: a unified package on PyPI, installable with `pip install vcf-sdk`.
- **Java**: a unified artifact on Maven Central, declared with a single dependency block.

Both SDKs include component modules for vCenter, vSAN, NSX, SDDC Manager, and even the VCF Installer -- all sharing a common authentication layer and consistent API conventions.

### OpenAPI 3.0 Coverage

A cornerstone of the unified SDK strategy is the adoption of OpenAPI 3.0 across approximately 90 percent of all VCF API surfaces. vCenter 9.0 adds OpenAPI 3.0 support for all vCenter and vSAN APIs, complementing the existing vCenter REST and VI JSON interfaces. This standardization means you can use any OpenAPI code generator -- not just the VMware SDK -- to produce client bindings in Go, TypeScript, Rust, or any other language that has an OpenAPI generator plugin.

The OpenAPI specifications are published on the Broadcom Developer Portal and in the `vmware/vcf-api-specs` GitHub repository. You can import them directly into Postman, use them with Swagger UI, or feed them into your own code generation pipeline.

### The VI JSON Protocol

The VI JSON wire protocol deserves special mention. Introduced originally in vSphere 8, VI JSON is a JSON-based, REST-like protocol that exposes the same VI (vSphere Infrastructure) APIs that were historically available only through SOAP. It runs on the same endpoint with the same security model, but it speaks JSON instead of XML.

For developers working in modern languages like Go, Python, or JavaScript -- languages where SOAP client libraries are either absent or painful -- VI JSON eliminates a longstanding barrier. For existing SOAP-based automation, migration to VI JSON can often be done entirely at the transport layer with minimal application-side changes, because the API operations and object models remain identical.

```python
# Example: Using the VCF SDK for Python to list VMs via VI JSON
from vcf_sdk.vsphere import VsphereClient

client = VsphereClient(
    server="vcenter01.lab.local",
    username="administrator@vsphere.local",
    password="VMware1!"
)

# List all VMs in the inventory
vms = client.vcenter.vm.list()
for vm in vms:
    print(f"{vm.name} - Power: {vm.power_state} - CPUs: {vm.cpu_count}")
```

### API Changelog and Versioning

VCF APIs use URI-based versioning: every endpoint carries a `/<version>/` prefix, and the API version evolves independently of the product version. When a version is incremented, older versions are deprecated with a documented sunset period. Broadcom publishes an API changelog with each release that details added, modified, and removed endpoints -- a critical resource for teams maintaining long-lived automation codebases.

---

## REST API Automation

### Authentication: The Token Pattern

Every SDDC Manager API call requires a bearer token. The authentication flow is simple but must be understood before you write your first line of automation code.

1. POST your credentials to the `/v1/tokens` endpoint.
2. Receive an access token (valid for 1 hour) and a refresh token (valid for 24 hours).
3. Pass the access token in the `Authorization: Bearer <token>` header on every subsequent request.
4. When the access token expires, use the refresh token to obtain a new one without re-authenticating.

Here is a complete, working bash example that authenticates against SDDC Manager, retrieves the list of workload domains, and prints them as formatted JSON:

```bash
#!/usr/bin/env bash
# vcf-domains.sh -- List all VCF workload domains via the SDDC Manager API
set -euo pipefail

SDDC_MGR="sddc-mgr.lab.local"
USERNAME="admin@local"
PASSWORD="VMware1!VMware1!"

# Step 1: Obtain a bearer token
TOKEN=$(curl -sk -X POST "https://${SDDC_MGR}/v1/tokens" \
  -H "Content-Type: application/json" \
  -d "{\"username\": \"${USERNAME}\", \"password\": \"${PASSWORD}\"}" \
  | jq -r '.accessToken')

if [ -z "$TOKEN" ] || [ "$TOKEN" = "null" ]; then
  echo "ERROR: Failed to obtain access token" >&2
  exit 1
fi

# Step 2: Query workload domains
curl -sk "https://${SDDC_MGR}/v1/domains" \
  -H "Authorization: Bearer ${TOKEN}" \
  | jq '.elements[] | {id, name, type, status}'

# Step 3: Query the VCF bundle inventory for available updates
echo "--- Available Update Bundles ---"
curl -sk "https://${SDDC_MGR}/v1/bundles" \
  -H "Authorization: Bearer ${TOKEN}" \
  | jq '.elements[] | {id, description, version, type, status}'
```

This pattern -- obtain token, call API, parse JSON -- is the foundation for every REST-based integration with VCF. Whether you are writing a Python script, a Jenkins pipeline step, or a custom monitoring integration, the flow is identical.

### Practical API Use Cases

The SDDC Manager API covers the full lifecycle of a VCF environment:

- **Host commissioning and decommissioning**: `POST /v1/hosts` to commission unassigned hosts with their network and storage configuration.
- **Workload domain creation**: `POST /v1/domains` with a JSON spec that defines the cluster configuration, vSAN storage, and NSX overlay.
- **Lifecycle updates**: `POST /v1/bundles` to download update bundles, then `PATCH /v1/domains/{id}` to apply them.
- **Certificate management**: `GET /v1/certificates` and `POST /v1/certificates/generates` to rotate certificates across the stack.
- **Backup and restore**: `POST /v1/backups/tasks` to trigger file-based backups of SDDC Manager and its managed components.

---

## Terraform: Infrastructure as Code for VCF

### The Provider Landscape

Terraform is the de facto standard for infrastructure-as-code in the VMware ecosystem. For VCF 9, several Terraform providers are available, each addressing a different layer of the stack:

| Provider | Registry Name | Purpose |
|----------|--------------|---------|
| vSphere | `hashicorp/vsphere` | VM lifecycle, resource pools, datastores, content libraries |
| NSX | `vmware/nsxt` | Segments, gateways, firewall rules, load balancers, VPCs |
| VCF | `vmware/vcf` | Workload domains, clusters, host commissioning |
| VCFA | `vmware/vcfa` | VCF Automation Provider and Organization configuration |

### An HCL Example: Deploying a Virtual Machine

```hcl
terraform {
  required_providers {
    vsphere = {
      source  = "hashicorp/vsphere"
      version = "~> 2.9"
    }
  }
}

provider "vsphere" {
  vsphere_server       = var.vcenter_server
  user                 = var.vcenter_user
  password             = var.vcenter_password
  allow_unverified_ssl = true
}

data "vsphere_datacenter" "dc" {
  name = "SDDC-Datacenter"
}

data "vsphere_compute_cluster" "cluster" {
  name          = "wld01-cluster01"
  datacenter_id = data.vsphere_datacenter.dc.id
}

data "vsphere_datastore" "ds" {
  name          = "vsan-datastore-wld01"
  datacenter_id = data.vsphere_datacenter.dc.id
}

data "vsphere_network" "net" {
  name          = "web-tier-subnet"
  datacenter_id = data.vsphere_datacenter.dc.id
}

data "vsphere_virtual_machine" "template" {
  name          = "rhel9-template"
  datacenter_id = data.vsphere_datacenter.dc.id
}

resource "vsphere_virtual_machine" "web" {
  count            = 3
  name             = "web-${count.index + 1}"
  resource_pool_id = data.vsphere_compute_cluster.cluster.resource_pool_id
  datastore_id     = data.vsphere_datastore.ds.id
  num_cpus         = 4
  memory           = 8192
  guest_id         = data.vsphere_virtual_machine.template.guest_id

  network_interface {
    network_id   = data.vsphere_network.net.id
    adapter_type = data.vsphere_virtual_machine.template.network_interface_types[0]
  }

  disk {
    label            = "disk0"
    size             = data.vsphere_virtual_machine.template.disks[0].size
    thin_provisioned = true
  }

  clone {
    template_uuid = data.vsphere_virtual_machine.template.id

    customize {
      linux_options {
        host_name = "web-${count.index + 1}"
        domain    = "lab.local"
      }

      network_interface {
        ipv4_address = "10.10.20.${count.index + 11}"
        ipv4_netmask = 24
      }

      ipv4_gateway = "10.10.20.1"
    }
  }
}
```

This example deploys three RHEL 9 virtual machines from a template, places them on a vSAN datastore within a VCF workload domain cluster, and customizes their networking. Run `terraform plan` to preview the changes, then `terraform apply` to execute.

### VCFA and Terraform Integration

The `vmware/vcfa` Terraform provider, released alongside VCF 9, enables you to automate the configuration of VCF Automation's Provider Portal and Organization Portals. Provider administrators can use it to create organizations, assign resource quotas, configure identity providers, and define network policies -- all without touching the UI.

```hcl
provider "vcfa" {
  url      = "https://vcfa.lab.local/provider"
  user     = "administrator"
  password = var.vcfa_admin_password
  org      = "system"
}

resource "vcfa_org" "tenant_alpha" {
  name         = "tenant-alpha"
  display_name = "Tenant Alpha"
  description  = "Production tenant for Alpha team"
  is_enabled   = true
}

resource "vcfa_org_regional_networking" "alpha_net" {
  org_id    = vcfa_org.tenant_alpha.id
  region_id = data.vcfa_region.primary.id
}
```

### GitOps Workflow with Terraform

For mature operations teams, the recommended pattern is to store Terraform configurations in a Git repository and manage them through a CI/CD pipeline:

1. Engineers commit HCL changes to a feature branch.
2. A pull request triggers `terraform plan` in a CI job, posting the plan output as a PR comment.
3. After review and merge to `main`, the CD pipeline runs `terraform apply` with state stored in a remote backend (Terraform Cloud, S3, or a Consul cluster).
4. Drift detection runs on a schedule, alerting the team if manual changes have been made outside of Terraform.

This workflow provides an auditable, reviewable, and repeatable process for infrastructure changes -- exactly what regulated industries demand.

---

## Ansible: Configuration Management and Compliance

### Use Cases in a VCF Environment

While Terraform excels at provisioning infrastructure, Ansible is the tool of choice for ongoing configuration management and compliance enforcement. In a VCF context, common Ansible use cases include:

- **Guest OS hardening**: applying CIS or STIG benchmarks to deployed virtual machines.
- **VCF component hardening**: locking down ESXi hosts, vCenter appliances, NSX Managers, and SDDC Manager to DISA STIG baselines.
- **Day-2 operations**: rotating passwords, updating NTP servers, collecting support bundles, and validating backup schedules across the fleet.
- **Drift detection**: comparing the live configuration of hosts and appliances against a declared desired state, and remediating automatically.

### Broadcom STIG Remediation Playbooks

Broadcom publishes official Ansible playbooks that automate STIG remediation across every VCF component. Starting with VCF 9.0, all Ansible roles for STIG compliance have been consolidated into a single playbook, covering:

- SDDC Manager appliance
- vCenter Server appliance
- ESXi hosts
- NSX Manager nodes
- VCF Operations for Networks
- VCF Operations HCX
- VCF Operations Fleet Management

Each task within the playbook is tagged with the corresponding STIG ID, allowing you to remediate a single control or an entire category. For example, to remediate only the SSH-related controls on SDDC Manager:

```bash
ansible-playbook -i inventory.yml vcf-stig-remediation.yml \
  --tags "sshd" \
  --limit "sddc_manager" \
  -e "stig_run_type=remediate"
```

Or to target a specific STIG ID:

```bash
ansible-playbook -i inventory.yml vcf-stig-remediation.yml \
  --tags "SDDC-9X-000025" \
  --limit "sddc_manager"
```

### A Conceptual Playbook: ESXi Host Hardening

Below is a simplified example illustrating how you might structure a playbook for ESXi host configuration beyond STIG compliance -- enforcing organizational standards for NTP, syslog, and core dump configuration:

```yaml
---
- name: VCF ESXi Host Baseline Configuration
  hosts: esxi_hosts
  gather_facts: false
  vars:
    ntp_servers:
      - 10.10.1.10
      - 10.10.1.11
    syslog_target: "udp://loginsight.lab.local:514"
    coredump_target: "10.10.1.20"

  tasks:
    - name: Configure NTP servers
      community.vmware.vmware_host_ntp:
        hostname: "{{ vcenter_hostname }}"
        username: "{{ vcenter_username }}"
        password: "{{ vcenter_password }}"
        esxi_hostname: "{{ inventory_hostname }}"
        ntp_servers: "{{ ntp_servers }}"
        state: present
      delegate_to: localhost

    - name: Set syslog target
      community.vmware.vmware_host_config_manager:
        hostname: "{{ vcenter_hostname }}"
        username: "{{ vcenter_username }}"
        password: "{{ vcenter_password }}"
        esxi_hostname: "{{ inventory_hostname }}"
        options:
          Syslog.global.logHost: "{{ syslog_target }}"
      delegate_to: localhost

    - name: Enable lockdown mode
      community.vmware.vmware_host_lockdown:
        hostname: "{{ vcenter_hostname }}"
        username: "{{ vcenter_username }}"
        password: "{{ vcenter_password }}"
        esxi_hostname: "{{ inventory_hostname }}"
        state: present
      delegate_to: localhost
```

The `community.vmware` Ansible collection provides modules for most vSphere and ESXi management tasks. For VCF-specific operations like workload domain management, you will typically call the SDDC Manager REST API using Ansible's `uri` module, applying the same token-based authentication pattern described earlier.

---

## VCF Automation as a Self-Service Cloud

VCF Automation (VCFA) is the multitenancy and self-service layer of VMware Cloud Foundation 9. It replaces VMware Cloud Director and VMware Aria Automation, unifying their capabilities under a single platform that sits natively within the VCF stack. VCFA introduces two types of organizations: **VM-Apps Organizations** (which provide the traditional VM-centric experience familiar to Aria Automation users) and **All-Apps Organizations** (which expose a Kubernetes-native API surface for consuming infrastructure declaratively).

### Provider Admin Workflow

The Provider Administrator is responsible for the foundational setup of VCFA:

1. **Deploy VCFA**: The VCFA appliance is deployed through the SDDC Manager UI or API and registered with the management domain's vCenter and NSX instances.

2. **Access the Provider Portal**: Navigate to `https://vcfa.lab.local/provider` and authenticate as the system administrator using the "system" organization.

3. **Create Organizations**: Click "Create Organization" to define a new tenant. Specify the organization name, assign resource constraints (CPU, memory, storage quotas), and select the backing vSphere Supervisor or resource pool.

4. **Configure Identity**: Integrate with an OIDC identity provider so that tenant users authenticate through your enterprise SSO. The VCFA Provider Organization can itself act as the OIDC identity provider for downstream tenant organizations.

5. **Assign Networking**: Allocate NSX VPCs or network segments to the organization, defining which IP pools and routing topologies are available to tenant users.

6. **Publish Catalog Items**: Create service catalog entries -- VM templates, Kubernetes cluster blueprints, Helm charts -- that tenant users can deploy through the self-service portal.

### Organization Admin Workflow

The Organization Administrator manages resources within a single tenant:

1. **Access the Organization Portal**: Navigate to `https://vcfa.lab.local/tenant/tenant-alpha` and authenticate through the configured identity provider.

2. **Manage Users and Roles**: Invite team members and assign roles (Organization Admin, Namespace Admin, Viewer) to control access.

3. **Create Namespaces**: Define vSphere Namespaces or Kubernetes namespaces within the allocated resource quota. Each namespace gets its own resource limits, storage policies, and network isolation.

4. **Deploy Workloads**: Use the service catalog to deploy VMs, VKS (VMware Kubernetes Service) clusters, or containerized applications. For All-Apps Organizations, workloads can be deployed declaratively through `kubectl` or Argo CD.

5. **Configure VPCs and Subnets**: Set up NSX VPCs and subnets for network segmentation within the organization, defining firewall rules and load balancer configurations.

6. **Monitor and Govern**: View resource consumption dashboards, set approval policies for expensive deployments, and integrate cost visibility tools.

### Argo CD and GitOps

VCF 9 ships with a Broadcom-built Argo CD Operator, available as a vSphere Supervisor Service (version 9.0.0.0100 and later). This operator enables GitOps-driven delivery for both virtual machines and Kubernetes workloads.

The architecture is straightforward:

1. A Git repository serves as the single source of truth, containing Kubernetes manifests, Helm charts, or VM deployment specifications.
2. Argo CD, running on a vSphere Supervisor namespace, continuously monitors the repository for changes.
3. When a commit is pushed, Argo CD reconciles the desired state in Git with the live state on the cluster, applying any changes automatically.
4. If manual drift occurs -- someone modifies a resource outside of Git -- Argo CD detects the discrepancy and either alerts or auto-remediates, depending on your sync policy.

For edge deployments, this model is particularly powerful. A central operations team can manage hundreds of distributed VCF edge sites from a single Git repository, pushing consistent configurations and application updates without direct access to each remote cluster.

```yaml
# Example Argo CD Application manifest for VCF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: web-tier-prod
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://git.lab.local/infra/vcf-deployments.git
    targetRevision: main
    path: environments/production/web-tier
  destination:
    server: https://supervisor.lab.local:6443
    namespace: tenant-alpha-prod
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

### Event Broker and Extensibility

VCFA includes an event broker subsystem inherited from the Aria Automation extensibility framework. Events are emitted at key points in the resource lifecycle -- VM provisioning, network creation, policy changes, deployment approvals -- and can trigger automated responses through subscriptions.

Subscriptions can invoke:

- **Extensibility Actions**: lightweight serverless functions (Python, Node.js) that run inline within VCFA.
- **VCF Operations Orchestrator Workflows**: complex multi-step workflows for ITSM integration, approval routing, or cross-platform orchestration.
- **Webhooks**: HTTP callbacks to external systems like ServiceNow, Slack, or custom event processors.

A common pattern is to subscribe to the `compute.provision.post` event and trigger a workflow that registers the new VM in your CMDB, assigns it a monitoring profile, and notifies the requesting team via Slack. This event-driven model eliminates the manual handoffs that plague traditional change management processes.

---

## Lifecycle Automation with PowerCLI and the API

Day-to-day VCF operations are where automation delivers its greatest return on investment. The following examples illustrate common lifecycle tasks performed through VCF PowerCLI and the SDDC Manager API.

### Checking the Bill of Materials

Before planning an upgrade, you need to understand what you are running today:

```powershell
# Connect to SDDC Manager
Connect-VcfSddcManager -Server sddc-mgr.lab.local -User admin@local -Password $securePass

# Retrieve the current BOM for all domains
$domains = Invoke-GetDomainsSddcManager
foreach ($domain in $domains.elements) {
    Write-Host "Domain: $($domain.name) - Type: $($domain.type)" -ForegroundColor Cyan

    # Get clusters within the domain
    $clusters = Invoke-GetClustersSddcManager | Where-Object { $_.domainId -eq $domain.id }
    foreach ($cluster in $clusters) {
        Write-Host "  Cluster: $($cluster.name) - Host Count: $($cluster.hostCount)"
    }
}

# Check for available update bundles
$bundles = Invoke-GetBundlesSddcManager
$available = $bundles.elements | Where-Object { $_.status -eq "AVAILABLE" }
Write-Host "`nAvailable Update Bundles: $($available.Count)" -ForegroundColor Green
$available | Format-Table version, description, type -AutoSize
```

### Querying Domain Health

```powershell
# Get the health status of a specific workload domain
$domain = Invoke-GetDomainsSddcManager | Where-Object { $_.name -eq "wld01" }
$domainDetail = Invoke-GetDomainByIdSddcManager -DomainId $domain.id

Write-Host "Domain: $($domainDetail.name)"
Write-Host "Status: $($domainDetail.status)"
Write-Host "vCenter: $($domainDetail.vcenter.fqdn)"
Write-Host "NSX Manager: $($domainDetail.nsxManager.fqdn)"
```

### Monitoring Fleet Operations

VCF Operations Fleet Management allows you to manage hundreds of VCF instances from a single pane. Querying fleet status programmatically is essential for large-scale deployments:

```powershell
# Connect to the Fleet Management instance
Connect-VIServer -Server fleet-vcenter.lab.local

# Retrieve all managed VCF instances and their compliance status
$fleetInstances = Invoke-GetManagedInstances

foreach ($instance in $fleetInstances) {
    $status = if ($instance.complianceStatus -eq "COMPLIANT") { "OK" } else { "DRIFT DETECTED" }
    Write-Host "$($instance.name): $status" -ForegroundColor $(
        if ($status -eq "OK") { "Green" } else { "Red" }
    )
}
```

### Automating Host Commission via the REST API

When rack-and-stack delivers new servers, commissioning them into VCF can be fully automated:

```bash
#!/usr/bin/env bash
# commission-hosts.sh -- Commission new hosts into SDDC Manager
set -euo pipefail

SDDC_MGR="sddc-mgr.lab.local"

# Authenticate
TOKEN=$(curl -sk -X POST "https://${SDDC_MGR}/v1/tokens" \
  -H "Content-Type: application/json" \
  -d '{"username":"admin@local","password":"VMware1!VMware1!"}' \
  | jq -r '.accessToken')

# Commission three new hosts
curl -sk -X POST "https://${SDDC_MGR}/v1/hosts" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "fqdn": "esxi-04.lab.local",
      "username": "root",
      "password": "HostPass123!",
      "networkPoolId": "net-pool-mgmt-01",
      "storageType": "VSAN"
    },
    {
      "fqdn": "esxi-05.lab.local",
      "username": "root",
      "password": "HostPass123!",
      "networkPoolId": "net-pool-mgmt-01",
      "storageType": "VSAN"
    },
    {
      "fqdn": "esxi-06.lab.local",
      "username": "root",
      "password": "HostPass123!",
      "networkPoolId": "net-pool-mgmt-01",
      "storageType": "VSAN"
    }
  ]' | jq '.'

echo "Host commission task submitted. Monitor progress in SDDC Manager."
```

### Exporting Configuration for Disaster Recovery

A robust DR strategy requires that your VCF configuration be exportable and version-controlled:

```powershell
# Export workload domain configurations to JSON for DR documentation
$domains = Invoke-GetDomainsSddcManager

foreach ($domain in $domains.elements) {
    $detail = Invoke-GetDomainByIdSddcManager -DomainId $domain.id
    $fileName = "domain-$($domain.name)-$(Get-Date -Format 'yyyyMMdd').json"
    $detail | ConvertTo-Json -Depth 10 | Out-File -FilePath $fileName
    Write-Host "Exported $($domain.name) to $fileName"
}
```

---

## Bringing It All Together

The automation landscape in VCF 9 is broad, but each tool has its place:

- **VCF PowerCLI 9.0** is your interactive and scripted workhorse for day-to-day operations, lifecycle management, and ad-hoc queries. The Initialize/Invoke SDK pattern gives you access to every API endpoint without waiting for high-level cmdlet coverage.

- **The Unified VCF SDK** (Python and Java) is the foundation for custom integrations, monitoring tools, and applications that need programmatic access to the VCF stack from non-PowerShell environments.

- **The SDDC Manager REST API** is the universal interface. Every automation tool -- PowerCLI, Terraform, Ansible, custom scripts -- ultimately communicates through it. Understanding the token-based authentication flow and the API's resource model is foundational knowledge.

- **Terraform** is the right choice for declarative infrastructure provisioning -- workload domains, VM fleets, NSX topologies, and VCFA organizations. Store your HCL in Git, review changes through pull requests, and apply them through CI/CD pipelines.

- **Ansible** is the right choice for configuration management and compliance enforcement. Use the Broadcom STIG playbooks as your baseline, and extend them with your own organizational standards.

- **VCFA** bridges the gap between infrastructure operators and application developers, providing self-service portals, resource governance, and multitenancy. The Provider Portal / Organization Portal model gives administrators control while giving developers autonomy.

- **Argo CD** closes the loop with GitOps-driven continuous delivery, ensuring that what is declared in Git is what runs in production -- across every cluster, every domain, and every edge site.

The organizations that extract the most value from VCF 9 are the ones that treat automation not as a project but as a discipline. Start with the SDDC Manager API and PowerCLI for foundational operations. Layer Terraform for provisioning. Add Ansible for compliance. Implement VCFA for self-service. And when you are ready, adopt GitOps with Argo CD for full declarative lifecycle management. Each layer builds on the one below it, and together they transform VCF from a private cloud platform into a fully automated, self-healing, policy-driven infrastructure machine.

In the next chapter, we will explore VCF Operations and Observability -- the monitoring, logging, and analytics capabilities that give you visibility into the platform you have just automated. Because automation without observability is flying blind, and observability without automation is just watching things break in high definition.
