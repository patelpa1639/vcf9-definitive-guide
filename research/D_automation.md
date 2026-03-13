# VCF 9 Automation and Developer Tooling Research

## 1. VCF PowerCLI 9.0

### Rebranding and Module Restructuring

With VCF 9.0, Broadcom renamed the PowerShell automation toolset from VMware.PowerCLI to VCF.PowerCLI. This change aligns the module naming with the broader VMware Cloud Foundation versioning and release cadence. The new module is installed from the PowerShell Gallery as `VCF.PowerCLI`, and administrators are advised to uninstall the legacy `VMware.PowerCLI` package before installing the new one. Despite the rename, all existing cmdlets, modules, and automation scripts continue to function as expected, preserving backward compatibility for existing workflows.

### Performance Improvements

Performance optimization was a central goal of the 9.0 release, and it was achieved through two complementary strategies.

First, many core cmdlets were rewritten as .NET-based compiled classes rather than interpreted PowerShell script functions. By leveraging Just-In-Time (JIT) compilation and optimized memory allocation patterns inherent to .NET, these compiled cmdlets deliver up to 70 percent faster execution in certain scenarios, particularly on PowerShell 7.x. On PowerShell 5.1, the improvement is measured at over 50 percent.

Second, the module packaging was restructured to consolidate cmdlets into fewer sub-modules. Fewer sub-modules means fewer assemblies need to be loaded into memory at import time, and the reduction in inter-module dependencies decreases I/O overhead. The practical result is dramatic: on Windows Server 2025, importing the old VMware.PowerCLI module took over one minute, while importing VCF.PowerCLI completes in roughly 18 seconds.

### New Modules and Cmdlets

VCF PowerCLI 9.0 introduces several new modules that extend automation coverage across the VCF stack:

**NSX Virtual Private Cloud (VPC) Module**: Previously, VPC management was limited to the vSphere UI. The new PowerCLI module exposes VPC lifecycle operations programmatically. Key cmdlets include `New-Vpc` for creating VPCs with parameters like `-Name`, `-PrivateIp`, and `-Description`; `Get-Vpc` for retrieving VPC objects; and `New-VpcSubnet` for adding subnets with configurable access modes (Private, Private-Transit Gateway, Public) and DHCP settings. The VM network adapter cmdlets (`New-NetworkAdapter`, `Set-NetworkAdapter`) have also been updated to accept VPC subnet objects, so virtual machines can be connected to VPC subnets directly through PowerCLI.

**SDDC Manager Module**: This module provides cmdlets for managing the foundational VCF infrastructure, including `Get-SddcCluster`, `Get-SddcDomain`, `Get-SddcHost`, and `Get-SddcVcenter`. These cmdlets complement the SDK-based approach by offering curated, high-level operations for common administrative tasks.

**Global Authorization APIs**: New SDK cmdlets expose privilege, role, and permission management through the vCenter authorization framework, enabling programmatic creation and modification of authorization policies.

**vSAN Space Reporting**: Enhanced cmdlets provide global deduplication metrics and improved storage capacity visibility.

Sources:
- https://blogs.vmware.com/cloud-foundation/2025/06/24/introducing-vcf-powercli-9-0/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vcf-cli-api-sdk.html
- https://developer.broadcom.com/powercli

---

## 2. PowerCLI Initialize/Invoke SDK Pattern

### How the Pattern Works

The PowerCLI SDK auto-generates cmdlets directly from the OpenAPI specifications of VCF component APIs. These auto-generated cmdlets follow a two-verb pattern that separates client-side data preparation from server-side execution.

**Initialize-* cmdlets** construct the request body on the client side without making any network calls. They build the complex nested data structures required by the API, handling type validation and parameter mapping. Multiple Initialize cmdlets can be chained together to compose deeply nested specifications.

**Invoke-* cmdlets** take the initialized specification objects and execute the corresponding API operation on the server. They handle authentication, HTTP transport, and response deserialization.

### Practical Example: Creating a Virtual Machine

The pattern is best illustrated through a multi-step VM provisioning workflow:

```powershell
# Step 1: Build the placement spec (client-side only)
$VcenterVMPlacementSpec = Initialize-VcenterVMPlacementSpec `
  -Folder "group-v4" `
  -ResourcePool "resgroup-11" `
  -Datastore "datastore-16"

# Step 2: Build the full VM creation spec, embedding the placement spec
$VcenterVMCreateSpec = Initialize-VcenterVMCreateSpec `
  -GuestOs "WINDOWS_SERVER_2025" `
  -Name "Webapp-VM" `
  -Placement $VcenterVMPlacementSpec

# Step 3: Execute the creation on the server
Invoke-CreateVM -VcenterVMCreateSpec $VcenterVMCreateSpec
```

### Discovery Helper Cmdlets

A key usability feature of the SDK is a set of helper cmdlets that map REST API paths to their corresponding Initialize and Invoke cmdlets. This makes it straightforward to find the right cmdlet when you know the API endpoint you want to call:

| Helper Cmdlet | Module | Target Component |
|---|---|---|
| `Get-vSphereOperation` | VMware.Sdk.vSphere | vSphere APIs |
| `Get-NsxOperation` | VMware.Sdk.Nsx.Policy | NSX Policy APIs |
| `Get-VcfSddcManagerOperation` | VMware.Sdk.Vcf.SddcManager | SDDC Manager APIs |
| `Get-VcfOpsOperation` | VMware.Sdk.Vcf.Ops | VCF Operations APIs |
| `Get-VcfInstallerOperation` | VMware.Sdk.Vcf.Installer | VCF Installer APIs |
| `Get-SrmOperation` | VMware.Sdk.Srm | Site Recovery Manager |

For example, running `Get-vSphereOperation -Path /vCenter/VM -Method POST` returns information about the `Invoke-CreateVM` cmdlet. This allows administrators to translate API documentation directly into PowerCLI commands without guessing at cmdlet names.

### Value Proposition

The Initialize/Invoke pattern ensures that every REST API endpoint exposed by a VCF component is immediately accessible through PowerCLI, even when a curated, high-level cmdlet has not yet been written. This closes the coverage gap that historically existed between the API surface area and the PowerCLI cmdlet library.

Sources:
- https://blogs.vmware.com/cloud-foundation/2025/07/02/demystifying-vcf-powercli-9-0-sdk/
- https://my-cloudy-world.com/2025/07/10/vmware-cloud-foundation-9-0-understanding-powercli-sdk-cmdlets/
- https://angrysysops.com/2025/07/02/powercli-reimagined-deep-dive-into-vcf-powercli-9-0/

---

## 3. New PowerCLI Modules in Detail

### NSX VPC Module

The NSX VPC module is perhaps the most significant new addition because it opens up programmatic access to a networking construct that was previously UI-only. VPCs in VCF 9 provide logically isolated networking subnets that are routed together by a VPC Gateway. Three subnet access modes are supported:

- **Private**: Scoped within the VPC, not routable to external networks.
- **Private-Transit Gateway**: Enables connectivity between VPCs through a transit gateway.
- **Public**: Provides external network reachability.

A typical automation workflow creates a VPC, then iterates to build out subnets for different application tiers:

```powershell
New-Vpc -Name "Holodeck-Vpc" -PrivateIp "192.168.0.0/24" -Description "Demo VPC"
$vpc = Get-Vpc -Name "Holodeck-Vpc"
foreach ($subnet in @('Web','App','DB')) {
    New-VpcSubnet -Vpc $vpc -Name $subnet -AccessMode Private -DhcpMode Server
}
```

### SDDC Manager Module

The SDDC Manager module provides curated cmdlets for querying and managing VCF infrastructure constructs. While the SDK-based Invoke cmdlets can reach every SDDC Manager API endpoint, the curated cmdlets offer a more idiomatic PowerShell experience with pipeline support and formatted output.

### VPC-VM Integration

The updated `New-NetworkAdapter` and `Set-NetworkAdapter` cmdlets now accept VPC subnet references, allowing a virtual machine's network interface to be placed onto a VPC subnet in a single command. This bridges the networking and compute management planes within a single scripting workflow.

Sources:
- https://blogs.vmware.com/cloud-foundation/2025/06/24/introducing-vcf-powercli-9-0/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/administration-sdks-cli-and-tools/introduction-to-vmware-vsphere-powercli/managing-vsphere-with-powercl/managing-virtual-private-clouds-with-vcf-powercli/connect-a-virtual-machine-to-a-vpc-subnet-in-vcenter.html

---

## 4. Unified VCF SDK for Python and Java

### Consolidation Strategy

Prior to VCF 9.0, developers working with the VMware platform in Python or Java had to pull in multiple separate SDKs for each component (vSphere, vSAN, NSX, SDDC Manager, etc.), each with its own versioning cadence. This frequently led to version mismatches, especially in Java where dependency trees are complex. The Unified VCF SDK merges these component SDKs into a single package with synchronized versioning.

### Components Covered

The initial release of the Unified SDK encompasses modules for:

- VMware vSphere (compute management)
- VMware vSAN (storage)
- VMware Cloud Foundation SDDC Manager (new in 9.0)
- VMware Cloud Foundation Installer (new in 9.0)
- VMware vSAN Data Protection

The vSphere Management SDK and Automation SDK, which were previously separate deliverables, are now unified into single packages within the consolidated SDK.

### Installation

**Python** (via PyPI):
```bash
pip install vcf-sdk
```

Individual component libraries can also be installed separately, for example `pyvmomi` for vSphere or `vcf-installer` for the installer module.

**Java** (via Maven Central):
```xml
<dependency>
    <groupId>com.vmware.sdk</groupId>
    <artifactId>vcf-sdk</artifactId>
    <version>9.0.0.0</version>
</dependency>
```

Both SDKs are also available for offline installation through the Broadcom Developer Portal, which is important for air-gapped or regulated environments.

### Distribution Channels

- **PyPI** and **Maven Central** for standard online installation
- **Broadcom Developer Portal** for offline binary downloads
- **GitHub** repositories (`vmware/vcf-sdk-java` and `vmware/vcf-sdk-python`) for source code, issue tracking, and sample code

### Code Samples

Broadcom provides dedicated sample repositories (`vcf-java-sdk-samples` and `vcf-python-sdk-samples`) with practical examples demonstrating common API usage patterns, authentication flows, and error handling.

Sources:
- https://blogs.vmware.com/cloud-foundation/2025/06/24/introducing-a-unified-vcf-sdk-9-0-for-python-and-java/
- https://developer.broadcom.com/vcf-python-sdk
- https://developer.broadcom.com/sdks/vcf-python-sdk/latest

---

## 5. OpenAPI 3.0 Specification

### Coverage

VCF 9.0 adopts OpenAPI 3.0 across approximately 90 percent of its API surface area. vCenter 9.0 specifically introduces OpenAPI 3.0 support alongside existing VI JSON and vCenter REST APIs. The specification files are distributed as JSON/YAML documents conforming to the OpenAPI 3.0.1 standard.

### Practical Benefits

The move to a standardized, machine-readable API specification format unlocks several developer workflows:

- **Automatic client library generation** using tools such as Swagger Codegen, Microsoft Kiota, or OpenAPI Generator, enabling developers to produce typed API clients in virtually any programming language.
- **Interactive API exploration** through Swagger UI, Redoc, or Postman, allowing developers to test API calls against live environments without writing code.
- **AI-assisted development** through compatibility with tools like GitHub Copilot, which can leverage the structured API definitions to suggest code completions.

### Specification Access

The OpenAPI specification files can be downloaded from the Broadcom Developer Portal or the `vmware/vcf-api-specs` GitHub repository. The repository organizes specifications into folders by protocol type (`/automation` for REST, `/vi-json` for VI JSON).

Sources:
- https://blogs.vmware.com/cloud-foundation/2025/08/01/apis-sdks-and-powercli-enhancements-in-vmware-cloud-foundation-9-0/
- https://github.com/vmware/vcf-api-specs
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/administration-sdks-cli-and-tools/what-is-the-vsphere-web-services-sdk/setup-for-development-with-openapi.html

---

## 6. VI JSON Protocol

### Background and Purpose

The Virtual Infrastructure JSON (VI JSON) protocol is a modern wire protocol introduced to transition the vSphere management API stack away from SOAP/XML toward a REST-style, JSON-based interface. Initially introduced in vSphere 8.0 Update 1, VI JSON reached broader maturity in VCF 9.0 where it serves as a first-class protocol option alongside the traditional SOAP interface and the vSphere Automation (REST) APIs.

### How It Works

VI JSON exposes the same managed objects, methods, and data types as the original VI SOAP API, but through a REST-style endpoint that accepts and returns JSON. Methods are invoked by sending a POST request with the method name encoded in the URL path and the method parameters serialized as JSON in the request body. This means that existing SOAP-based automation can be migrated to VI JSON with minimal structural changes -- the object model remains identical, only the wire format and transport mechanism change.

### Authentication

Like SOAP, VI JSON uses session-based authentication. A client authenticates through the SessionManager to obtain a session ID, and subsequent requests carry this session ID as a credential. The ServiceInstance singleton provides the entry point for accessing server resources without prior authentication.

### Protocol Expansion

Starting with vSphere 8 Update 3, VI JSON coverage was expanded beyond the core VIM API to include additional SOAP-based endpoints such as Storage Policy Based Management (SPBM), Storage Monitoring Service (SMS), and Virtual Storage Lifecycle Management (VLSM). By VCF 9.0, VI JSON is the recommended path for new integrations that need access to the full breadth of the VI API.

### Migration Path

The explicit design goal was to minimize friction for migration. SDK-based applications can switch to VI JSON entirely at the platform level, with application-side code changes limited to transport configuration rather than business logic. The same structure, managed objects, and method names are preserved.

Sources:
- https://blogs.vmware.com/code/2023/06/10/introducing-vi-json-a-modern-wire-protocol-for-vsphere-management/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/administration-sdks-cli-and-tools/introduction-to-the-vcf-programming-guide/the-vsphere-web-services-sdk/client-applications-using-the-json-protocol.html
- https://developer.broadcom.com/xapis/virtual-infrastructure-json-api/latest/

---

## 7. First-Ever API Changelog in Release Notes

### Significance

VCF 9.0 includes the first-ever API changelog embedded directly within the VCF release notes. Previously, developers had to compare API specifications between releases manually to identify what had changed. The changelog provides a consolidated, structured summary of every API modification across all VCF components.

### Changelog Structure

The changelog organizes changes by VCF component. Each component section includes counts and details for four categories of change:

1. **New Operations**: APIs introduced for the first time in this release.
2. **Deprecated Operations**: APIs that remain functional but are marked for removal in a future release.
3. **Deleted Operations**: APIs that have been removed and are no longer available.
4. **Updated APIs**: Structural modifications to existing endpoints (parameter changes, response schema updates, etc.).

### Scope of Components Tracked

The changelog covers the full VCF stack:

- vSphere (Virtual Infrastructure and Automation APIs)
- NSX (networking, edge, policy, and manager APIs)
- VCF Operations
- SDDC Manager
- VMware Identity Broker
- VCF Installer
- HCX Management Platform
- Private AI Services
- VMware Live Recovery (data protection, cyber recovery, site recovery)

### Notable Changes in the 9.0 Changelog

The scale of changes in the 9.0 release is substantial. The Virtual Infrastructure JSON API alone added 307 new operations (reflecting the expansion of VI JSON coverage). The VMware Private AI Service API introduced 57 new operations. On the deprecation and removal side, the NSX Manager API deprecated 79 operations and removed 608, reflecting the ongoing consolidation of NSX management surfaces. The VMware Identity Broker removed 12 operations.

Each entry in the changelog hyperlinks to the corresponding specification on the Broadcom Developer Portal, enabling developers to drill into the details of any individual change.

Sources:
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vcf-cli-api-sdk/vcf-changelog.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vcf-cli-api-sdk/vcf-powercli-changelog.html
- https://blogs.vmware.com/cloud-foundation/2025/08/01/apis-sdks-and-powercli-enhancements-in-vmware-cloud-foundation-9-0/

---

## 8. VCF Automation (VCFA) -- Provider Portal and Organization Setup

### Replacing VMware Cloud Director

VCF Automation (VCFA) is the successor to VMware Cloud Director (VCD) within the VCF 9 stack. It provides the multi-tenancy, self-service provisioning, and service catalog capabilities that Cloud Service Providers (CSPs) and enterprise IT teams need to deliver infrastructure-as-a-service to their internal or external consumers.

### Provider Portal

The Provider Management Portal is the administrative interface for provider (system) administrators, accessed at `https://<vcfa-fqdn>/provider`. The initial login uses the default administrator account configured during VCFA installation. From this portal, providers manage the shared infrastructure, define service tiers, allocate resource quotas, and onboard tenant organizations.

### Organization Types

VCFA supports two types of organizations:

- **VM Apps Organizations**: Designed for traditional virtual machine workloads. These organizations use the VCF Automation catalog, templates, and orchestration workflows familiar from VMware Aria Automation (vRealize Automation). They support extensibility through the Aria Automation model.

- **All Apps Organizations**: A newer construct that exposes infrastructure through Kubernetes-style declarative APIs. Tenants in All Apps organizations consume resources (VMs, Kubernetes clusters, networking, storage) through the Kubernetes API layer. This model supports GitOps workflows and direct integration with tools like Argo CD and Terraform's Kubernetes provider.

### Tenant Onboarding Workflow

The tenant onboarding process follows a structured sequence:

1. The provider administrator creates a Tenant Organization and allocates a Region, Supervisor, and Zones along with resource quotas (compute, memory, storage).
2. The organization is configured with an identity provider (LDAP, SAML, or OIDC/OAuth) for user authentication.
3. Organization administrators create Projects to organize workloads and assign users with role-based access control.
4. Application teams provision vSphere Namespaces, VPCs, and workloads within their allocated quotas.

A Quick Setup wizard is available that streamlines the initial organization creation by applying predefined settings, which is particularly useful for evaluation or lab environments.

### Built-In Services

VCFA ships with several integrated services that tenants can consume:

- Virtual Machine Service (VM provisioning and lifecycle)
- vSphere Kubernetes Service (VKS 3.3.1)
- Network Service (VPC and Transit Gateway management)
- Volume Service (persistent storage)
- Data Services (PostgreSQL and MySQL as-a-service)
- Secret Store
- External DNS

### Orchestration

Built-in orchestration capabilities are provided by VCF Operations Orchestrator 9.0, which supports nested workflow visualization in a tree view for improved troubleshooting. Organizations can also integrate external VCF Operations Orchestrator instances for custom workflows.

Sources:
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes/vmware-cloud-foundation-90-release-notes/platform-whats-new/whats-new-vcf-automation.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/organization-management/vcfa-overview.html
- https://fojta.wordpress.com/2025/06/17/quck-guide-to-vcf-automation-for-vcd-administrators/
- https://blanketvm.com/2025/08/01/vcf-9-vcfa-vm-app-creation/

---

## 9. VCFA Argo CD GitOps Integration

### Broadcom Argo CD Operator for VCF

Broadcom released a Generally Available Argo CD Operator specifically built for VCF 9.0. This is not the upstream open-source Argo CD but a Broadcom-maintained distribution that provides native integration with the VCF platform. The initial release includes Argo CD v2.14.13, validated and supported for the VCF Supervisor environment (Supervisor 1.30.10).

### Architecture

The operator leverages the vSphere Supervisor as its foundation -- the same control plane that manages Kubernetes clusters, virtual machines, and vSphere Pods through declarative APIs. The deployment model separates responsibilities between Cloud Admins and Cloud Users:

- **Cloud Admins** deploy and lifecycle-manage the Argo CD Operator on the Supervisor using vCenter or CLI. They establish security boundaries through namespace access controls.
- **Cloud Users** instantiate scoped Argo CD instances within their assigned vSphere Namespace. Each instance connects independently to Git repositories.

### Supported Workloads

The GitOps model through Argo CD supports management of multiple workload types:

- Virtual machines provisioned and updated declaratively via YAML manifests stored in Git
- Kubernetes cluster lifecycle management (creation, scaling, upgrades)
- vSphere Pods running alongside containerized workloads
- Application deployment into VKS clusters

### Edge Use Case

A particularly compelling use case highlighted by Broadcom is GitOps-driven edge management. Using a centrally managed Git repository as the single source of truth, organizations can drive consistent, repeatable, and automatically reconciled deployments of VMs and containers across multiple distributed edge sites. Argo CD continuously synchronizes the declared state in Git with the actual state of the infrastructure, automatically remediating drift.

### Roadmap

Planned enhancements include integration with the VCF Automation UI for visibility and management, enhanced service discovery, secure credential injection for private Git repositories, and deeper observability and logging capabilities.

Sources:
- https://blogs.vmware.com/cloud-foundation/2025/07/11/gitops-for-vcf-broadcom-argo-cd-operator-now-available/
- https://blogs.vmware.com/cloud-foundation/2025/07/29/vcf-9-0-for-edge-automating-app-deployment-at-scale-with-gitops-using-argo-cd/
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vsphere-supervisor-services-and-standalone-components/latest/modern-applications/continuous-integration-and-delivery-with-argocd.html

---

## 10. VCFA Event Broker

### Extensibility Model

VCF Automation includes an event-driven extensibility framework that enables organizations to extend and automate application lifecycle operations. The Extensibility tab in VCFA is where administrators configure event-driven triggers and associated actions.

### Event Subscriptions

Subscriptions define the binding between infrastructure events and the automation that should run when those events occur. When a matching event fires, the Event Broker sends a payload of information (defined by the event topic's schema) to the configured runnable item. Subscriptions can be bound to:

- **VCF Operations Orchestrator Workflows**: Full orchestration workflows with complex multi-step logic.
- **ABX (Action Based Extensibility) Actions**: Lightweight serverless functions written in Python, Node.js, or PowerShell.

Common event topics include Compute Allocation, Compute Post Provision, and other lifecycle events that fire during VM or application deployment.

### ServiceNow Integration Example

A practical integration scenario is event-based synchronization with ServiceNow. After configuring events through the VCF Operations Orchestrator interface and creating subscriptions in VCFA, the Event Based Data Sync workflow handles events based on the configured subscriptions, automatically pushing updates to ServiceNow so ITSM users receive real-time notifications about infrastructure changes.

### Notification Support

VCFA supports email notification configuration, allowing cloud administrators to set up SMTP servers and send automated messages to users about events occurring within the platform.

### Provider vs. Tenant Extensibility

On the provider side, VCFA inherits extensibility mechanisms from VMware Cloud Director, including plugin and extension support. On the tenant side, however, the VCD-style plugin mechanism is not yet available; instead, tenant extensibility relies on Aria Automation orchestration and the newer ABX action framework.

### Historical Context: vCenter Event Broker Appliance

The VMware Event Broker Appliance (VEBA), which was previously available as a Fling for event-driven automation against vCenter events, has been archived. Broadcom is collecting feedback on a potential event bus capability natively integrated into VMware Cloud Foundation, which could provide a more tightly integrated successor.

Sources:
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/organization-management/vcfa-overview/copy-of-overview-of-vrealize-automation-itsm-application-for-servicenow-user-portal/copy-of-create-a-data-source-choice-in-servicenow-for-new-york-release/configure-event-based-data-sync-in-vmware-aria-automation/configure-event-subscriptions-in-vmware-aria-automation.html
- https://www.brockpeterson.com/post/post-deployment-actions-with-vcf-automation
- https://github.com/vmware-archive/vcenter-event-broker-appliance

---

## 11. Terraform Integration with VCF and VCFA

### Three-Provider Model for VCFA

VCF Automation 9 uses three distinct Terraform providers, each targeting a different layer of the platform:

1. **Terraform Provider for VCF (`vmware/vcf`)**: Manages the foundational VCF infrastructure through SDDC Manager. Resources include `vcf_instance` (ESXi host management), `vcf_domain` (workload domain creation), `vcf_host` (host commissioning), and modules for provisioning VI Workload Domains and configuring NSX Edge Clusters. This provider is available on the Terraform Registry at `registry.terraform.io/providers/vmware/vcf`.

2. **Terraform Provider for VCFA (`vmware/vcfa`)**: A newer provider specifically for VCF Automation. It automates Provider Portal and Organization Portal configuration, including creating organizations, configuring NSX instances and provider gateways, defining roles, and setting up OIDC identity providers for tenant organizations. The provider is available on GitHub at `vmware/terraform-provider-vcfa` and the Terraform Registry.

3. **Kubernetes Provider (`hashicorp/kubernetes`)**: For All Apps Organizations, the native Kubernetes Terraform provider is used to perform CRUD operations against the VCFA Kubernetes API layer. This covers resources like projects and namespace-level configurations that are exposed as Kubernetes custom resources.

Additionally, the existing **VMware Aria Automation Provider (`vmware/vra`)** continues to be used for catalog management and VM Apps Organization configurations.

### Terraform in VCFA Runtime

VCFA also supports Terraform as an authoring tool within its catalog system. Administrators can create Terraform configurations that are executed by the VCFA Terraform runtime when users request catalog items. This requires preparing a Terraform runtime environment within VCFA and configuring provider credentials. Both online and air-gapped deployment models are supported.

### Provider Portal Automation

The VCFA Terraform provider enables full automation of the provider setup workflow. An administrator can script the creation of an organization, allocation of infrastructure quotas, configuration of networking (provider gateways, NSX integration), and identity provider setup -- all as declarative Terraform configurations that can be version-controlled and applied repeatably.

Sources:
- https://registry.terraform.io/providers/vmware/vcf/latest
- https://github.com/vmware/terraform-provider-vcfa
- https://williamlam.com/2025/10/automating-vcf-automation-vcfa-configuration-using-vcfa-terraform-provider.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/organization-management/administering-all-apps-organizations-in-vcfa-automation/terraform-configuration-in-vcf-automation-for-all-apps.html
- https://vrealize.it/2025/08/07/vcf-automation-9-new-terraform-providers-for-all-apps-org/

---

## 12. Ansible Integration and STIG Remediation Playbooks

### Consolidated STIG Playbook

Starting with VCF 9.0, Broadcom consolidated all Ansible roles needed for STIG (Security Technical Implementation Guide) compliance remediation into a single unified playbook. In previous releases, remediation roles were scattered across separate packages for each component. The consolidated playbook simplifies compliance operations by providing a single entry point.

### Components Covered

The VCF 9.x STIG Ansible playbook targets the following components:

**VCF Application Layer (Product Rules)**:
- VCF Automation
- VMware Identity Broker
- VCF Operations
- VCF Operations Fleet Management
- VCF Operations for Logs
- VCF Operations for Networks
- VCF Operations HCX
- SDDC Manager
- vCenter

**Infrastructure Layer**:
- ESXi 9.0 hosts

Each component has a dedicated Ansible role within the consolidated playbook, and roles can be selectively applied to target specific components.

### Playbook Structure

The playbook is located on VCF appliances at:
```
/usr/share/stigs/vcf/9.x/Y25M06-srg/ansible/vmware-cloud-foundation-stig-ansible-hardening/
```

The playbook uses an inventory file that defines groups of target hosts (VCF appliances). Each role targets specific inventory host groups via SSH, applying configuration changes to bring the appliance into compliance with the relevant STIG controls.

### Variable Naming Convention

The playbook uses a standardized variable naming convention to clarify the source and scope of each variable:

- `<rolename>_defaults_description` -- Variables defined in the role's `defaults/` directory (overridable)
- `<rolename>_vars_description` -- Variables defined in the role's `vars/` directory (higher precedence)
- `var_vault_<inventory_name>_description` -- Sensitive variables stored in Ansible Vault

### Capabilities

The STIG automation framework supports three primary workflows:

1. **Scanning**: Auditing VMware products against STIG controls to assess current compliance status.
2. **Reporting**: Generating compliance reports for visualization and CKL (Checklist) files for formal accreditation processes.
3. **Remediation**: Automatically applying configuration changes to bring non-compliant controls into compliance.

### Complementary Tools

In addition to Ansible, the VCF STIG documentation also references the following automation tools:

- **Chef InSpec** for compliance-as-code auditing
- **MITRE Heimdall** for compliance visualization
- **MITRE SAF CLI** for security automation framework operations
- **PowerShell/PowerCLI** for Windows-based remediation workflows

Sources:
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/stig/9-0/vcf-stig-documentation/docs-tutorials-cloud-foundation-9x-ansible-playbook-overview.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/stig/9-0/vcf-stig-documentation/docs-automation-tools-ansible.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/stig/9-0/vcf-stig-documentation/docs-overview-overview.html
- https://techdocs.broadcom.com/us/en/vmware-cis/vcf/stig/9-0/vcf-stig-documentation/docs-tutorials-cloud-foundation-9x-product-vcf-application-remediate9-app-remediate9-app.html

---

## Developer Documentation Improvements

### Centralized Developer Portal

VCF 9.0 consolidates all developer resources onto the Broadcom Developer Portal, eliminating the previously fragmented documentation that was spread across multiple sites (VMware Developer Center, various GitHub repos, separate tech docs sites). The unified portal provides:

- API references organized and filterable by VCF component
- SDK binaries and installation guides
- Release notes and API changelogs
- Getting Started guides for Python, Java, and PowerShell
- Programming guides and developer setup instructions
- Links to GitHub repositories with code samples

### Dedicated Developer Centers

The portal features dedicated landing pages for each major tool:

- **VCF Python SDK**: PyPI binaries, code examples, downloads
- **VCF Java SDK**: Maven Central integration, examples
- **VCF PowerCLI**: Installation guides, changelog, documentation
- **VCF API Specification**: Complete API reference documentation
- **Terraform Providers**: Links to VMware-managed providers

### VCF Consumption CLI

VCF 9.0 also introduces a new VCF Consumption CLI, a first release featuring context management, plugin auto-discovery, support for VCFA and vSphere Supervisor endpoints, and compatibility with air-gapped environments. It is available through multiple package managers and binary downloads.

Sources:
- https://blogs.vmware.com/cloud-foundation/2025/07/06/improved-developer-documentation/
- https://developer.broadcom.com/powercli
- https://developer.broadcom.com/vcf-python-sdk
