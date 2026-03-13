---
title: "VMware Cloud Foundation 9: The Definitive Guide"
subtitle: "A Comprehensive Reference for Architects, Administrators, and Engineers"
author: "Pranav Patel, Technical Account Manager, VMware by Broadcom"
edition: "First Edition — 2026"
---

# Table of Contents

- **Front Matter** — Title Page, Foreword, Preface
- **Chapter 1** — The Private Cloud Imperative
- **Chapter 2** — What's New in VCF 9
- **Chapter 3** — Architecture
- **Chapter 4** — Compute (vSphere 9)
- **Chapter 5** — Storage (vSAN 9)
- **Chapter 6** — Networking (NSX 9)
- **Chapter 7** — Security
- **Chapter 8** — Automation and APIs
- **Chapter 9** — AI and Modern Workloads
- **Chapter 10** — Competitive Analysis
- **Chapter 11** — Lifecycle Management
- **Chapter 12** — Certifications
- **Chapter 13** — Troubleshooting
- **Chapter 14** — Reference, Appendix, and Acknowledgments

---


---

# VMware Cloud Foundation 9: The Definitive Guide

### A Comprehensive Reference for Architects, Administrators, and Engineers

**By Pranav Patel**
Technical Account Manager, VMware by Broadcom

*First Edition — 2026*

*The views in this book are the author's own and do not represent official Broadcom positions.*

---

## Foreword

There is a reckoning underway in enterprise IT, and if you have been paying attention, you saw it coming.

For the better part of a decade, the prevailing wisdom was simple: move everything to the public cloud. Lift and shift. Refactor where you can. Close the data centers. The hyperscalers — Amazon Web Services, Microsoft Azure, Google Cloud Platform — promised infinite elasticity, operational simplicity, and a future unburdened by the weight of physical infrastructure. Chief Information Officers signed multi-year enterprise agreements. Engineering teams retooled around cloud-native paradigms. Entire organizations restructured their IT departments on the assumption that the private data center was a relic of a bygone era.

Then the bills arrived.

By 2025, the pendulum had swung with force. Enterprises that had migrated aggressively to public cloud discovered that the economics of hyperscaler consumption, particularly for steady-state workloads, did not pencil out the way the original business cases had promised. Egress charges, reserved instance complexity, and the sheer cost of running predictable workloads on variable pricing models created a financial reality that many organizations had not fully modeled. Gartner reported that cloud spending waste accounted for roughly twenty-one percent of total cloud infrastructure budgets. GEICO publicly disclosed that its cloud costs had ballooned to two-and-a-half times their pre-migration levels after a decade-long effort to move over six hundred applications to the public cloud. These were not edge cases. They were canaries.

Simultaneously, a second force was reshaping the conversation: artificial intelligence. The explosion of generative AI and large language model workloads introduced a class of computing that was fundamentally different from traditional enterprise applications. These workloads demanded sustained access to GPU clusters, vast quantities of training data, and — critically — sovereignty over that data. Organizations building proprietary AI models quickly realized that sending sensitive training data to a hyperscaler's multi-tenant environment introduced risks that no amount of contractual assurance could fully mitigate. The inference layer, too, demanded low-latency access to data that increasingly lived on-premises. AI did not kill the cloud, but it reminded the industry that data has gravity, and gravity pulls things home.

The regulatory environment added a third accelerant. The European Union's Digital Operational Resilience Act, known as DORA, entered enforcement in January 2025, imposing strict requirements on financial institutions regarding third-party ICT provider risk management, data residency, and operational continuity. The EU's General Data Protection Regulation continued to tighten its grip on cross-border data transfers, with cumulative penalties exceeding four billion euros since its inception. In the United States, proposed updates to the HIPAA Security Rule moved to make encryption of electronic protected health information a mandatory requirement rather than an addressable implementation specification. FedRAMP authorization requirements continued to raise the bar for cloud service providers serving federal agencies. Across every major regulatory jurisdiction, the message was converging: you must know where your data lives, who can access it, and how you will recover it when something goes wrong.

This confluence of cost pressure, AI-driven data gravity, and regulatory mandate has created what I call the private cloud imperative. It is not a rejection of cloud computing. It is a maturation of cloud strategy — a recognition that the right infrastructure for a given workload depends on economics, data sensitivity, latency requirements, and regulatory exposure, not on ideology.

I am Pranav Patel, and I serve as a Technical Account Manager at VMware by Broadcom. In that role, I work daily with enterprise customers who are navigating precisely this inflection point. I sit in architecture reviews where teams are deciding which workloads to repatriate. I join escalation calls where organizations are redesigning their infrastructure to meet DORA compliance deadlines. I advise customers building private AI platforms who need to understand how VMware Cloud Foundation can deliver GPU-as-a-Service without the complexity of bespoke solutions. This book is the resource I wished existed every time a customer looked across the table and asked the question I hear most often: "Where do I even start?"

This is where you start.

---

## Preface

### What This Book Covers

This book is a comprehensive reference for VMware Cloud Foundation 9, the platform that Broadcom has positioned as the foundation of the modern private cloud. It covers the architectural principles, design decisions, operational models, and strategic considerations that architects, administrators, and engineers need to understand in order to plan, deploy, and operate VCF 9 environments at enterprise scale.

The book is organized to build understanding progressively. It begins with the strategic and historical context that explains why VCF 9 exists and what problems it is designed to solve. It then moves through the platform's architecture, covering compute, storage, networking, security, automation, and lifecycle management in depth. Later chapters address advanced topics including multi-instance fleet management, Kubernetes integration, private AI infrastructure, disaster recovery, and the operational practices that distinguish a well-run VCF environment from one that merely functions.

### What This Book Omits

This is not a lab guide. You will not find step-by-step installation walkthroughs, screenshot-laden procedures, or click-by-click configuration instructions. That material exists in VMware's official documentation and in the excellent community resources maintained by the VMware ecosystem. Reproducing it here would be redundant, and it would age faster than any printed book can keep pace with.

Instead, this book focuses on conceptual mastery — the kind of understanding that allows you to look at a VCF design document and immediately identify what is sound, what is risky, and what is missing. It is the understanding that separates an engineer who can follow a runbook from an architect who can write one.

### Three Ways to Read This Book

**For certification preparation:** If you are studying for a VMware certification exam, this book provides the conceptual depth that exam guides often compress into bullet points. Read it cover to cover, paying particular attention to the architecture and design chapters.

**For architecture planning:** If you are designing or redesigning a VCF environment, use this book as a reference. The chapters are structured to address specific architectural domains — compute, storage, networking, security, operations — and each can be consulted independently when you are working through a particular design decision.

**For competitive research:** If you are evaluating VCF against alternatives such as Nutanix, OpenStack, or Apache CloudStack, the early chapters provide the strategic context and architectural overview that will allow you to make informed comparisons. The glossary and terminology sections will help you map VCF concepts to their equivalents in other platforms.

### Assumed Knowledge

This book assumes familiarity with virtualization concepts, basic networking, and storage fundamentals. You should know what a hypervisor does, understand the difference between Layer 2 and Layer 3 networking, and be comfortable with concepts like RAID, iSCSI, and NFS. Prior experience with VMware vSphere is helpful but not strictly required — the book explains VCF-specific concepts from the ground up.

### Currency Note

VMware Cloud Foundation 9.0 reached General Availability on June 17, 2025. Subsequent updates, including VCF 9.0.1 and VCF 9.0.2, have been released with additional features and fixes. This book is current through early 2026 and reflects the platform as it exists at the time of writing. Where features are noted as Technology Preview, that status is indicated. Readers should consult the official VMware Cloud Foundation release notes for the most current information on specific build numbers and patch levels.

---

## Chapter 1: The Private Cloud Imperative

### The Rise and Reckoning of Public Cloud

The public cloud era began not with a grand strategy but with a credit card. In 2006, Amazon Web Services launched Elastic Compute Cloud, and for the first time, a developer could provision a virtual server in minutes without filing a procurement request, waiting for hardware delivery, or negotiating with a facilities team for rack space and power. The impact was seismic. Within a few years, startups were building billion-dollar businesses on infrastructure they did not own. Enterprise IT departments, facing pressure to match the agility of cloud-native competitors, began migrating workloads in earnest.

By the early 2020s, cloud adoption had reached a point that many industry observers considered irreversible. Gartner, Forrester, and IDC published forecasts projecting that the majority of enterprise workloads would run in public cloud within the decade. Cloud-first policies became standard corporate doctrine. Some organizations went further, adopting cloud-only mandates that treated any on-premises infrastructure as technical debt to be eliminated.

The reckoning came not as a single event but as a gradual accumulation of evidence. Organizations running steady-state workloads — databases, application servers, internal platforms — discovered that the consumption-based pricing models of public cloud, which excelled at handling variable and burst workloads, imposed a significant premium on predictable, always-on computing. The promise of elasticity was real, but for workloads that did not need to scale dynamically, that elasticity came with a cost that often exceeded the fully loaded cost of owned infrastructure.

Egress charges emerged as a particularly contentious issue. Cloud providers offered generous ingress pricing — it cost very little to move data into the cloud. Moving it out, however, incurred charges that could be substantial at scale. For organizations running analytics workloads, distributing content, or simply backing up data to a secondary location, egress fees became a line item that finance teams could no longer ignore.

By 2025, the data was unambiguous. Surveys indicated that eighty-six percent of CIOs planned to move at least some workloads from public cloud back to private infrastructure, up from forty-three percent in 2021. Twenty-one percent of workloads and data had already been repatriated. This was not a retreat from cloud computing. It was a correction — a recognition that hybrid architecture, in which workloads are placed based on their specific requirements rather than on a blanket policy, produces better economic and operational outcomes than either cloud-only or on-premises-only strategies.

### Cloud Repatriation and Data Gravity

The term "cloud repatriation" describes the practice of moving workloads from public cloud back to private infrastructure, whether that infrastructure is located in a corporate data center, a colocation facility, or an edge location. The term is deliberately loaded — it implies a homecoming, a return to where the workload belongs. While not every workload benefits from repatriation, a growing body of evidence suggests that a significant category of enterprise workloads does.

The concept of data gravity is central to understanding why repatriation makes sense for certain workloads. Data gravity refers to the tendency of applications, services, and other data sets to be attracted to large concentrations of existing data. As a data set grows, the cost and latency associated with moving it increase, and the applications that depend on it increasingly need to run in proximity to it. For organizations with petabytes of data stored on-premises — whether in structured databases, unstructured file systems, or object stores — the gravitational pull of that data makes it economically and operationally difficult to sustain a cloud-only strategy.

Artificial intelligence has amplified this effect dramatically. Training a large language model requires sustained access to massive data sets. Running inference workloads demands low-latency access to model weights and input data. For organizations building proprietary AI systems — and the number of such organizations is growing rapidly — the economics strongly favor running these workloads on dedicated, on-premises GPU infrastructure rather than paying premium cloud rates for GPU instances that may not even be available on demand.

Gartner has predicted that by 2026, forty percent of enterprises will adopt hybrid compute architectures in mission-critical workflows, up from eight percent in prior years. The hybrid model is not a compromise. It is the architecturally sound approach for organizations whose workload portfolio spans the full spectrum from burst-capable cloud-native applications to steady-state data-intensive systems.

### Regulatory Mandates and Data Sovereignty

If economics provide the incentive for cloud repatriation, regulation provides the mandate. Across every major jurisdiction, lawmakers and regulators have been tightening the rules governing where data can reside, how it must be protected, and what obligations organizations bear when they entrust their data to third-party infrastructure providers.

**GDPR (General Data Protection Regulation):** The European Union's landmark privacy regulation, in effect since 2018, imposes strict requirements on the processing and transfer of personal data. Cross-border transfers of personal data outside the EU require adequacy decisions or approved safeguards such as Standard Contractual Clauses. The invalidation of the Privacy Shield framework and ongoing legal challenges to transatlantic data transfer mechanisms have made compliance with GDPR a primary driver of data localization strategies. Cumulative GDPR penalties have exceeded four billion euros, and enforcement shows no sign of abating.

**DORA (Digital Operational Resilience Act):** Entering enforcement in January 2025, DORA imposes comprehensive requirements on financial institutions operating in the European Union. The regulation mandates that contracts with ICT service providers specify data location, encryption key management, audit rights, and exit strategies. It requires organizations to maintain operational resilience against ICT disruptions and to conduct regular testing of their recovery capabilities. The European Banking Authority reported that sixty-eight percent of banks updated their vendor assessments in 2024 and 2025 specifically to address sovereignty requirements under DORA. Penalties for non-compliance can reach seven percent of global annual turnover — exceeding even the GDPR maximum.

**HIPAA (Health Insurance Portability and Accountability Act):** In the United States, HIPAA governs the protection of electronic protected health information. A proposed 2025 update to the HIPAA Security Rule would elevate encryption from an "addressable" implementation specification to a mandatory requirement, reflecting the reality that unencrypted health data in transit or at rest represents an unacceptable risk in the current threat landscape. Healthcare organizations are increasingly choosing self-hosted platforms to avoid the complexity of third-party processor agreements and to ensure that patient data never leaves their controlled environments.

**FedRAMP (Federal Risk and Authorization Management Program):** FedRAMP provides a standardized approach to security assessment, authorization, and continuous monitoring for cloud products and services used by U.S. federal agencies. The rigor of FedRAMP authorization, particularly at the High impact level, has driven many federal agencies and their contractors to maintain private cloud infrastructure for sensitive workloads, using FedRAMP-authorized public cloud services selectively for workloads with lower sensitivity classifications.

These regulations are not theoretical constraints. They are operational requirements with specific technical implications. An organization subject to DORA must be able to demonstrate, on demand, exactly where its data resides and how its infrastructure will recover from a disruption. An organization subject to GDPR must be able to prove that personal data has not been transferred to a jurisdiction without adequate protections. VMware Cloud Foundation 9 was designed with these requirements in mind, embedding data-residency tagging, geo-fencing, compliance dashboards, and automated certificate management directly into the platform.

### The Software-Defined Data Center

Before examining VMware Cloud Foundation in detail, it is worth establishing what we mean by a software-defined data center, or SDDC. The concept is foundational to everything that follows.

A traditional data center is built on purpose-built hardware. Compute resources are provided by physical servers. Storage is provided by dedicated storage arrays connected via fiber channel or iSCSI fabrics. Networking is provided by physical switches, routers, and firewalls. Each of these infrastructure domains has its own management tools, its own operational procedures, and its own team of specialists. Provisioning a new workload in a traditional data center requires coordination across all three domains — a process that can take days or weeks.

The software-defined data center abstracts each of these infrastructure domains into software, creating virtual resources that can be provisioned, configured, and managed programmatically.

**Compute Virtualization:** The hypervisor — in VMware's case, ESXi (now branded as ESX in version 9) — abstracts the physical server's CPU, memory, and I/O resources into virtual machines. Each virtual machine operates as though it has dedicated hardware, but in reality, it shares the physical server's resources with other virtual machines under the hypervisor's control. This abstraction enables workload mobility, high availability, and efficient utilization of physical hardware.

**Storage Virtualization:** VMware vSAN abstracts the local storage devices in each physical server into a distributed storage pool that is shared across the cluster. Rather than relying on external storage arrays, vSAN creates a hyper-converged storage layer that scales with the compute infrastructure. In VCF 9, vSAN Express Storage Architecture with global deduplication can reduce storage total cost of ownership by thirty-four percent compared to traditional approaches.

**Network Virtualization:** VMware NSX abstracts the physical network into a software-defined overlay that provides switching, routing, firewalling, load balancing, and VPN services entirely in software. Network policies travel with the workload, regardless of which physical host it runs on. NSX's microsegmentation capability allows organizations to apply zero-trust security policies at the individual workload level, a capability that has no practical equivalent in physical networking.

When these three virtualization layers are combined with a unified management and automation platform, the result is a software-defined data center — an infrastructure environment in which every resource can be provisioned on demand, managed through policy, and orchestrated through automation. This is the foundation upon which VMware Cloud Foundation is built.

### The VMware and VCF Story: From ESX to Cloud Foundation 9

The story of VMware Cloud Foundation begins not in the era of cloud computing but in a Palo Alto, California garage in 1998.

**The Founding (1998-2001):** VMware was founded in 1998 by Diane Greene, Mendel Rosenblum, Scott Devine, Ellen Wang, and Edouard Bugnion. Greene and Rosenblum, drawing on Rosenblum's academic research in virtual machine monitors at Stanford University, set out to solve a problem that the industry had largely given up on: virtualizing the x86 architecture. Unlike mainframe platforms, which had supported virtualization since the 1960s, the x86 instruction set was not designed to be virtualized. VMware's breakthrough was a binary translation technique that made it possible to run unmodified operating systems in virtual machines on commodity x86 hardware. VMware Workstation, the company's first product, shipped in 1999 and targeted developers and IT professionals who needed to run multiple operating systems on a single machine.

**The Server Revolution (2001-2009):** In 2001, VMware entered the data center with ESX Server 1.0 and GSX Server. ESX Server included a purpose-built hypervisor — the VMkernel — that ran directly on the hardware, bypassing the host operating system entirely. This bare-metal architecture delivered performance and isolation characteristics that were fundamentally superior to hosted virtualization approaches. Over the next several years, VMware iterated rapidly. VMotion, introduced in 2003, enabled live migration of running virtual machines between physical hosts — a capability that transformed data center operations. VMware High Availability, Distributed Resource Scheduler, and Storage VMotion followed, each adding operational capabilities that made the virtual data center more resilient, more efficient, and easier to manage than its physical predecessor.

**The vSphere Era (2009-2019):** With the release of vSphere 4.0 in 2009, VMware rebranded its data center platform and signaled a shift from server virtualization to a broader cloud infrastructure vision. ESX gave way to ESXi, a slimmer hypervisor that removed the Linux-based Service Console in favor of a minimal attack surface and a more manageable footprint. vSphere 5.0, 5.5, 6.0, 6.5, and 6.7 brought incremental improvements in scalability, security, and management. vSphere 7.0, released in 2020, integrated Kubernetes directly into the hypervisor with the vSphere with Tanzu capability, marking VMware's commitment to supporting both virtual machines and containers as first-class workloads.

**The Cloud Foundation Journey (2016-2024):** VMware Cloud Foundation was first released in 2016 as an integrated platform that brought together vSphere, vSAN, NSX, and SDDC Manager into a single, validated, and lifecycle-managed stack. The vision was ambitious: rather than requiring customers to independently deploy, configure, and manage each component, VCF would provide an opinionated, automated deployment that ensured all components were validated to work together. VCF 2.x, 3.x, 4.x, and 5.x iterated on this vision, progressively improving the automation, expanding the component portfolio, and refining the operational model. VCF 4.x aligned with vSphere 7, and VCF 5.x aligned with vSphere 8.

**The Broadcom Acquisition (2023-2024):** In December 2023, Broadcom completed its acquisition of VMware for approximately sixty-one billion dollars — the largest enterprise software acquisition in history. The acquisition brought immediate and consequential changes. Broadcom discontinued perpetual licensing for all VMware products, moving to a subscription-only model. The product portfolio, which had grown to encompass roughly one hundred seventy offerings, was consolidated into a focused set of platforms anchored by VMware Cloud Foundation and VMware vSphere Foundation. Minimum core requirements were increased to seventy-two cores per purchase. The partner ecosystem was restructured, with the number of authorized VMware Cloud Service Providers reduced from over four thousand five hundred globally to a curated set of Pinnacle and Premier partners.

These changes were disruptive. Some customers experienced significant cost increases, with anecdotal reports ranging from four hundred percent to over one thousand percent depending on the starting point and the licensing model. Gartner and Forrester both documented notable price escalations across their client bases. The restructuring prompted seventy-four percent of IT leaders surveyed by Gartner's Peer Community to report that they were actively exploring VMware alternatives, including Nutanix, OpenStack, Apache CloudStack, and Proxmox.

Yet the disruption also clarified Broadcom's strategic intent. VMware Cloud Foundation was no longer one product among many — it was the product. The entire organization was being restructured around VCF as the unified private cloud platform. The consolidation, however painful in the short term, was designed to create a platform that could compete not against other virtualization vendors but against the hyperscalers themselves.

**VCF 9.0 — The Version Jump (June 2025):** When VMware Cloud Foundation 9.0 reached General Availability on June 17, 2025, the version number raised immediate questions. Why 9? What happened to versions 6, 7, and 8?

The answer lies in version alignment. Broadcom made the strategic decision to synchronize the VCF version number with its core component, vSphere. VCF 5.x had been built on vSphere 8. VCF 9.0 was built on vSphere 9. By aligning the major version numbers, Broadcom eliminated a source of confusion that had long plagued the VMware ecosystem — the need to maintain a mental mapping between VCF versions and the versions of its constituent products. VCF 9 means vSphere 9, ESX 9 (note the return of the ESX branding, dropping the "i"), NSX 5, and vSAN 9. The version jump was not cosmetic. It was a signal that VCF 9.0 represented a platform-level architectural transformation, not an incremental update.

### VCF versus VVF: Understanding the Product Lineup

Under Broadcom's simplified portfolio, VMware's infrastructure offerings are organized into two primary platforms.

**VMware vSphere Foundation (VVF)** is the entry-level platform, designed for organizations with straightforward virtualization needs. VVF includes vSphere, vSAN (with 250 GiB per core of capacity), Tanzu Kubernetes Grid for a single supervisor cluster, and basic management capabilities. It is sold on a per-core subscription basis and is designed exclusively for on-premises deployment. VVF does not include NSX, SDDC Manager, Aria Suite, HCX, or any of the advanced automation, security, and lifecycle management capabilities that define VCF.

**VMware Cloud Foundation (VCF)** is the enterprise platform — the full private cloud stack. VCF includes everything in VVF plus SDDC Manager, NSX for software-defined networking and microsegmentation, VCF Operations and VCF Automation for fleet-level management and self-service consumption, HCX for workload mobility, and a substantially larger vSAN capacity allocation of 1 TiB per core. VCF is designed for organizations building true private cloud environments with multi-tenancy, self-service provisioning, policy-driven governance, and hybrid cloud connectivity.

The distinction is not merely a matter of features. It reflects a fundamentally different operational model. VVF is infrastructure virtualization. VCF is private cloud.

### The Five Pillars of VCF 9

VMware Cloud Foundation 9 is organized around five architectural pillars that define how the platform is built, operated, consumed, secured, and governed. These pillars represent the design philosophy that distinguishes VCF 9 from its predecessors and from competing platforms.

**Pillar 1: One Interface to Private Cloud Operations.** VCF 9 introduces VCF Operations as the centralized management plane for fleet-wide infrastructure operations. From a single console, operators can monitor the health of every VCF instance in the fleet, manage lifecycle operations including patching and upgrades, enforce configuration compliance against CIS and NIST baselines, manage certificates and identity federation, and perform predictive pre-checks before executing changes. The Quick Start App reduces initial setup complexity, and the platform claims up to a ten-fold improvement in daily administrative productivity. Functions that were previously distributed across SDDC Manager, Aria Operations, and Aria Lifecycle have been consolidated into VCF Operations, eliminating tool sprawl and providing a unified operational experience.

**Pillar 2: One Interface for Cloud Consumption.** VCF 9 delivers a self-service consumption experience through VCF Automation. Developers and application teams access infrastructure services through a unified interface that supports Terraform providers, REST APIs, and VCF Automation blueprints. Role-based access controls and pre-configured catalogs ensure that consumers can provision the resources they need without requiring direct access to the underlying infrastructure. This pillar addresses one of the primary reasons organizations adopted public cloud in the first place: the ability to get infrastructure on demand without filing a ticket and waiting for someone to provision it manually.

**Pillar 3: Run Virtual Machines and Containers Side by Side.** VCF 9 treats virtual machines and Kubernetes-managed containers as equally important workload types. The embedded vSphere Kubernetes Service allows operators to provision and manage Kubernetes clusters alongside traditional VMs from the same platform, using the same networking and storage infrastructure. Integration with Argo CD supports GitOps-based deployment workflows for containerized applications. This unified approach eliminates the need for separate infrastructure stacks for VMs and containers, reducing operational complexity and enabling organizations to modernize their application portfolio at their own pace.

**Pillar 4: Sovereign and Secure as a Platform.** Security and sovereignty are not bolted on to VCF 9 — they are embedded in the platform architecture. All product components run with FIPS 140-2 and FIPS 140-3 cryptographic modules enabled by default. The Security Operations Dashboard maps attack surfaces and compliance scores across the fleet. Configuration compliance monitoring continuously compares runtime settings against security baselines. Data-residency tagging and geo-fencing capabilities enable organizations to enforce sovereignty policies at the infrastructure level. Support for AMD SEV-SNP and Intel TDX confidential computing technologies provides hardware-level isolation for sensitive workloads. VMware vDefend delivers microsegmentation, lateral security, and global IDS/IPS policy management.

**Pillar 5: Embedded Cost Control.** VCF 9 provides native cost visibility and chargeback capabilities that go beyond infrastructure metrics. The platform incorporates software licensing costs, operational expenses, and physical data center costs into a comprehensive total cost of ownership view. Predictive cost modeling enables capacity planning and budget forecasting. Chargeback and showback dashboards allow organizations to allocate infrastructure costs to business units, projects, or applications, bringing the cost transparency of public cloud to the private cloud environment.

### Who Deploys VCF and Why

VMware Cloud Foundation is deployed by organizations across virtually every industry, though certain sectors and use cases are disproportionately represented.

**Financial Services:** Banks, insurance companies, and investment firms deploy VCF to meet the stringent regulatory requirements of DORA, PCI DSS, and local financial regulators while maintaining the agility to deliver digital services. The ability to enforce data residency, demonstrate operational resilience, and maintain auditable compliance makes VCF a natural fit for this heavily regulated sector.

**Healthcare:** Hospitals, health systems, and pharmaceutical companies use VCF to host electronic health record systems, medical imaging platforms, and research computing environments where HIPAA compliance and data sovereignty are non-negotiable. The platform's confidential computing capabilities are particularly relevant for organizations working with sensitive patient data and clinical research information.

**Government and Defense:** Federal, state, and local government agencies deploy VCF in environments that must meet FedRAMP, ITAR, and other government-specific security standards. The platform's ability to operate in air-gapped environments and its FIPS-validated cryptographic modules make it suitable for classified and sensitive workloads.

**Manufacturing and Industrial:** Organizations with operational technology environments deploy VCF to host industrial control systems, digital twin platforms, and edge computing workloads. The platform's ability to extend from the core data center to edge locations makes it well suited for organizations with distributed operational footprints.

**Service Providers:** VMware Cloud Service Providers deploy VCF as the foundation for managed private cloud, disaster recovery, and infrastructure-as-a-service offerings. VCF 9's multi-tenancy and self-service capabilities, delivered through VCF Automation, enable service providers to deliver public-cloud-like experiences on dedicated or shared infrastructure.

**AI and Machine Learning:** A growing number of organizations deploy VCF with VMware Private AI Foundation with NVIDIA to build on-premises AI platforms that deliver GPU-as-a-Service with multi-tenancy, monitoring, and lifecycle management. For organizations that cannot or will not send training data to public cloud, this represents a path to enterprise AI that maintains full data sovereignty.

The common thread across these use cases is not a rejection of cloud computing. It is a recognition that certain workloads — those with predictable resource consumption, strict data residency requirements, low-latency demands, or regulatory obligations — are better served by private infrastructure that offers cloud-like operational characteristics. VMware Cloud Foundation 9 is designed to be that infrastructure.

---

## Comprehensive Terminology Glossary

The following glossary covers the essential terms used throughout this book and in the VMware Cloud Foundation 9 ecosystem. Each entry provides a concise definition sufficient to ground the reader in the concept before encountering it in the detailed chapters that follow.

**VCF (VMware Cloud Foundation):** VMware Cloud Foundation is the integrated private cloud platform from VMware by Broadcom that combines compute, storage, networking, security, and management into a unified, lifecycle-managed stack. It is the flagship infrastructure product in the Broadcom VMware portfolio and the platform around which all enterprise-grade VMware capabilities are consolidated. VCF 9.0, generally available since June 2025, represents the latest major release and introduces the fleet-based operational model.

**VVF (VMware vSphere Foundation):** VMware vSphere Foundation is the entry-level virtualization platform offered by VMware by Broadcom, designed for organizations with core compute virtualization needs that do not require the full private cloud capabilities of VCF. VVF includes vSphere, a limited vSAN allocation, and Tanzu Kubernetes Grid for a single supervisor cluster. It does not include NSX, SDDC Manager, VCF Operations, VCF Automation, or the advanced services available in VCF.

**ESX (formerly ESXi):** ESX is the bare-metal hypervisor that forms the compute foundation of VMware Cloud Foundation. With version 9, VMware reverted to the ESX branding, dropping the "i" suffix that had been appended since the removal of the Service Console in ESXi 3.5. ESX 9 provides the compute virtualization layer, supporting both virtual machines and containerized workloads through the vSphere Kubernetes Service.

**vSphere:** vSphere is VMware's enterprise compute virtualization platform, comprising the ESX hypervisor and the vCenter Server management layer. It provides the core capabilities for virtual machine creation, management, high availability, and resource scheduling. In VCF 9, vSphere is at version 9.0 and is available exclusively as a component of VCF or VVF, not as a standalone product.

**vCenter Server:** vCenter Server is the centralized management platform for vSphere environments, providing a single point of control for ESX hosts, virtual machines, clusters, and associated resources. Each VCF domain contains a vCenter Server instance, and in VCF 9, vCenter is managed through the fleet-level VCF Operations platform. vCenter provides the APIs, policy engines, and workflow orchestration that underpin day-to-day infrastructure operations.

**vSAN (Virtual SAN):** vSAN is VMware's software-defined storage solution that aggregates local storage devices across ESX hosts in a cluster into a distributed, shared storage pool. vSAN eliminates the need for external storage arrays in hyper-converged deployments. VCF 9 introduces vSAN Express Storage Architecture with global deduplication, which extends deduplication across multiple clusters rather than individual disks, yielding up to a thirty-four percent reduction in storage total cost of ownership.

**vSAN Express Storage Architecture (ESA):** vSAN ESA is the next-generation storage architecture within vSAN, designed specifically for NVMe-based storage devices. It provides improved performance, space efficiency, and simplified configuration compared to the original vSAN architecture. In VCF 9, vSAN ESA supports global deduplication across clusters and serves as the recommended storage architecture for new deployments.

**NSX:** NSX is VMware's software-defined networking and security platform, providing network virtualization, microsegmentation, distributed firewalling, load balancing, and VPN services. In VCF 9, NSX is at version 5 and provides the overlay networking that enables workload mobility, multi-tenancy, and zero-trust security. NSX is included in VCF but not in VVF, which is one of the key differentiators between the two platforms.

**SDDC Manager:** SDDC Manager is the infrastructure lifecycle management appliance within each VCF instance. It automates the deployment and configuration of workload domains, manages host commissioning and decommissioning, and provides instance-level lifecycle workflows. In VCF 9, many functions previously centralized in SDDC Manager — including licensing, certificate management, and password management — have been elevated to the fleet level through VCF Operations.

**VCF Operations:** VCF Operations is the fleet-level management platform introduced in VCF 9 as a mandatory component. It provides centralized lifecycle management, health monitoring, configuration compliance, certificate management, identity federation, and capacity planning across all VCF instances in a fleet. VCF Operations replaces and consolidates capabilities that were previously distributed across SDDC Manager, Aria Operations, and Aria Lifecycle.

**VCF Automation:** VCF Automation is the fleet-level automation and self-service platform in VCF 9. It provides catalog-based self-service provisioning, infrastructure-as-code capabilities through Terraform integration, blueprint-driven deployment workflows, and governance policies. VCF Automation enables the cloud consumption experience that allows developers and application teams to provision infrastructure without direct operator intervention.

**VCF Installer:** VCF Installer is the deployment tool introduced in VCF 9 for day-zero installation and convergence of VCF environments. It automates the initial bring-up process, including the deployment of the management domain and the registration of the environment with VCF Operations. The VCF Installer simplifies what was previously a complex, multi-step deployment process.

**VCF Fleet:** A VCF fleet is a logical grouping of one or more VCF instances that are managed by a single set of fleet-level components, specifically one instance of VCF Operations and one instance of VCF Automation. The fleet is the unit of centralized management in VCF 9, enabling consistent policy, lifecycle, and governance across geographically distributed infrastructure. For most organizations, a single fleet encompasses the entire VCF environment.

**VCF Instance:** A VCF instance is a discrete VCF deployment consisting of a management domain and, optionally, one or more workload domains. Each instance contains its own SDDC Manager appliance and operates as a self-contained infrastructure management unit within the broader fleet. An instance can operate independently if connectivity to the fleet management layer is temporarily unavailable.

**VCF Private Cloud:** The VCF Private Cloud is the highest-level construct in the VCF 9 hierarchy, representing the entire software-defined data center environment across all fleets and instances. It is the organizational container within which policy, governance, and cost management operate at the broadest scope. A VCF Private Cloud can span multiple data centers, regions, and edge locations.

**Management Domain:** The management domain is the foundational domain created during VCF deployment that hosts all management infrastructure components, including SDDC Manager, vCenter, NSX managers, VCF Operations, and VCF Automation. The management domain is not intended to run consumer workloads. It exists to manage and operate the VCF environment and must be sized and protected accordingly.

**Workload Domain:** A workload domain is a logically isolated infrastructure environment within a VCF instance, consisting of one or more vSphere clusters, their associated vCenter instance, and an NSX deployment. Workload domains host consumer workloads — the virtual machines, containers, and applications that constitute the organization's production, development, and test environments. Multiple workload domains can share an NSX instance but cannot share the management domain's NSX deployment.

**Microsegmentation:** Microsegmentation is a network security technique implemented through NSX that applies firewall policies at the individual workload level rather than at the network perimeter. Each virtual machine or container can have its own security policy that travels with it regardless of its network location or physical host. Microsegmentation is a foundational component of zero-trust security architectures and is one of the key capabilities that differentiates VCF from VVF.

**vDefend:** VMware vDefend is the advanced threat prevention and network security platform integrated into VCF 9. It provides self-service microsegmentation, VPC-aware lateral security with delegated administration, global IDS/IPS policy management, and distributed firewalling. vDefend enables consistent threat defense policies across the entire VCF fleet.

**HCX (Hybrid Cloud Extension):** VMware HCX provides workload mobility services that enable the migration of virtual machines between VCF environments, between VCF and public cloud, or between data centers. HCX supports bulk migration, live migration with zero downtime, and replication-based migration patterns. It is included in VCF and is commonly used during data center migrations and cloud repatriation initiatives.

**Tanzu Kubernetes Grid (TKG):** Tanzu Kubernetes Grid is VMware's Kubernetes distribution, embedded within vSphere through the vSphere Kubernetes Service. TKG provides lifecycle-managed Kubernetes clusters that run on the same infrastructure as virtual machines, sharing compute, storage, and networking resources. In VCF 9, TKG is the mechanism through which containerized workloads are deployed and managed.

**vSphere Kubernetes Service (VKS):** The vSphere Kubernetes Service enables the provisioning and management of Kubernetes clusters directly within vSphere, using the Supervisor Cluster and Tanzu Kubernetes Grid. VKS treats Kubernetes as a first-class workload type alongside virtual machines, allowing both to be managed through a unified operational model. VKS is a key enabler of VCF 9's unified VM and container strategy.

**Supervisor Cluster:** The Supervisor Cluster is a Kubernetes-enabled vSphere cluster that provides the control plane for running Kubernetes workloads within vSphere. It enables the deployment of Tanzu Kubernetes Grid clusters and vSphere Pods, bridging the gap between traditional VM-based infrastructure management and cloud-native container orchestration. The Supervisor is the foundation of VCF's Kubernetes integration.

**Avi Load Balancer:** The Avi Load Balancer, formerly known as NSX Advanced Load Balancer, provides software-defined application delivery services including load balancing, web application firewall, and analytics for both virtual machine and Kubernetes workloads. In VCF 9, Avi supports self-service load balancing provisioning through VCF Automation. It replaces hardware-based load balancers with a distributed, software-defined alternative.

**Advanced NVMe Memory Tiering:** Advanced NVMe Memory Tiering is a VCF 9 capability that extends the host memory pool by using NVMe flash storage as a lower-cost memory tier. Compute-intensive workloads keep their active working sets in DRAM close to the CPU while cold memory pages are transparently migrated to NVMe storage. This capability can reduce memory and server total cost of ownership by up to thirty-eight percent.

**VMware Private AI Foundation with NVIDIA:** VMware Private AI Foundation with NVIDIA is an advanced service available with VCF 9 that provides GPU-as-a-Service capabilities for on-premises AI and machine learning workloads. It delivers GPU multi-tenancy, monitoring, and lifecycle management, enabling organizations to build private AI platforms while maintaining full data sovereignty. It supports inference and training workloads with virtually zero performance overhead compared to bare metal.

**VMware Live Recovery:** VMware Live Recovery is the unified cyber and disaster recovery platform in VCF 9, combining ransomware recovery and traditional disaster recovery into a single solution. It supports up to two hundred immutable snapshots per virtual machine, providing a deep recovery point history that enables organizations to recover from ransomware attacks by restoring to a known-clean state. Live Recovery integrates with VCF Operations for centralized monitoring and management.

**VMware Data Services Manager:** VMware Data Services Manager is a Technology Preview capability in VCF 9 that provides database-as-a-service for PostgreSQL, MySQL, and SQL Server workloads. It enables self-service provisioning of database instances through VCF Automation, bringing the convenience of managed database services to the private cloud environment. As a Technology Preview, it is not yet recommended for production use.

**FIPS 140-2 / FIPS 140-3:** Federal Information Processing Standards 140-2 and 140-3 are U.S. government standards that define requirements for cryptographic modules used to protect sensitive information. In VCF 9, all product components — including vCenter, ESX, and NSX — run with FIPS-enabled cryptographic modules by default. This ensures that encryption operations throughout the platform meet the standard required by federal agencies and regulated industries.

**Configuration Compliance:** Configuration compliance in VCF 9 refers to the automated, continuous comparison of runtime infrastructure settings against established security baselines such as CIS Benchmarks and NIST guidelines. VCF Operations provides dashboards and alerts that identify configuration drift, enabling operators to detect and remediate deviations before they become security vulnerabilities. This capability is essential for organizations operating under regulatory mandates.

**Fleet Management:** Fleet management is the operational model introduced in VCF 9 that enables centralized management of multiple VCF instances from a single set of management services. Fleet management encompasses lifecycle operations, identity federation, certificate management, capacity planning, and compliance monitoring. It replaces the instance-by-instance management approach of prior VCF versions with a unified, scalable operational model.

**Chargeback and Showback:** Chargeback and showback are cost allocation methodologies supported by VCF 9's embedded cost control capabilities. Chargeback assigns actual infrastructure costs to consuming business units or projects, creating financial accountability. Showback provides the same cost visibility without formal billing, enabling organizations to understand consumption patterns and make informed decisions about resource allocation. Both mechanisms bring public cloud cost transparency to the private cloud.

**GitOps:** GitOps is a methodology for managing infrastructure and application deployments using Git as the single source of truth. VCF 9 supports GitOps workflows through its integration with Argo CD, enabling organizations to define, version, and deploy Kubernetes workloads using declarative configurations stored in Git repositories. Changes to the desired state trigger automated reconciliation with the actual state of the infrastructure.

**Workload Mobility:** Workload mobility refers to the ability to move virtual machines and their associated data between hosts, clusters, data centers, or cloud environments without disruption to the running workload. In VCF 9, workload mobility is supported through vMotion for live migration within and between clusters, HCX for cross-site and cross-cloud migration, and Storage vMotion for non-disruptive storage relocation. Workload mobility is a foundational capability that enables data center consolidation, disaster avoidance, and cloud repatriation.

**DPU (Data Processing Unit):** A Data Processing Unit is a specialized processor designed to offload infrastructure functions such as networking, storage, and security from the host CPU. VCF 9 supports optional DPU offload through the NSX Enhanced Data Path, which can deliver up to three times the switching performance compared to software-only implementations. DPU support enables organizations to reclaim host CPU capacity for application workloads while improving network throughput and latency.

**Confidential Computing:** Confidential computing refers to hardware-level technologies that protect data while it is being processed, using trusted execution environments that isolate workloads from the host infrastructure and from other tenants. VCF 9 supports AMD SEV-SNP and Intel TDX confidential computing technologies, enabling organizations to run sensitive workloads with hardware-enforced isolation that protects against even privileged access from the infrastructure administrator.

**Geo-Fencing:** Geo-fencing in VCF 9 is a policy mechanism that restricts workload placement and data residency to specified geographic locations or data centers. Geo-fencing policies are enforced at the platform level, ensuring that workloads subject to data sovereignty requirements cannot be migrated or replicated outside their designated geographic boundaries. This capability is essential for organizations operating under GDPR, DORA, and other data residency regulations.

---

*Chapter 2 continues with a deep dive into the VCF 9 architecture, exploring the management domain, workload domain design patterns, and the fleet management model in detail.*


---

# Chapter 2: What's New in VMware Cloud Foundation 9

VMware Cloud Foundation 9.0, generally available as of June 17, 2025, is not an incremental update. It is an architectural reset. Every major subsystem---compute, storage, networking, automation, operations, lifecycle management, and the developer toolchain---has been rethought, restructured, or outright replaced. If you have spent years building muscle memory around SDDC Manager workflows, Cloud Builder spreadsheets, and Aria Suite product names, VCF 9 will feel unfamiliar at first. That unfamiliarity is deliberate. Broadcom has rebuilt the platform around two gravitational centers: *operations* and *consumption*. Everything else---the hypervisor, the distributed storage, the network virtualization layer---is now positioned as core innovation that those two centers orchestrate, rather than as standalone products that administrators stitch together through manual integration.

This chapter walks through every significant change in VCF 9.0 and its early maintenance releases, organized so that each feature receives the treatment it deserves: what it is, how it works, and why it matters to the practitioners who will deploy and operate it. Whether you are evaluating the upgrade from VCF 5.2, planning a greenfield deployment, or simply trying to map old terminology to new, this chapter is your definitive reference.

---

## The Philosophy Shift: Operations and Consumption as the Center of Gravity

Before we examine individual features, it is worth pausing on the design philosophy that animates VCF 9. In prior releases, VMware Cloud Foundation was, at its core, a lifecycle and deployment wrapper around a collection of best-of-breed products: vSphere for compute, vSAN for storage, NSX for networking, and a loosely coupled set of management tools (vRealize / Aria) that sat alongside the stack. The integration was real but often felt bolted on. SDDC Manager handled Day 0 and Day 1. Aria handled Day 2. vCenter handled the hypervisor. Customers toggled between three, four, sometimes five different management consoles to accomplish what should have been a single workflow.

VCF 9 inverts that model. The platform now presents two primary surfaces to its users:

1. **VCF Operations** --- the *operations plane* --- where infrastructure administrators build, monitor, patch, secure, and govern the private cloud.
2. **VCF Automation** --- the *consumption plane* --- where application teams, DevOps engineers, and line-of-business owners request and consume infrastructure through self-service catalogs, policy-driven guardrails, and Infrastructure as Code pipelines.

Everything beneath those two planes---ESXi hosts, vSAN datastores, NSX segments, vCenter instances---still exists and still matters, but the administrative center of gravity has shifted upward. You no longer start your day in SDDC Manager. You start it in VCF Operations. You no longer hand-craft networks in the NSX Manager UI for every tenant. You define VPC policies in VCF Automation and let the platform instantiate them. This is the single most important conceptual shift in VCF 9, and every feature discussed in this chapter flows from it.

---

## The New Hierarchy: Fleet, Instance, Private Cloud

### What It Is

VCF 9 introduces a formal, five-level hierarchy for describing your infrastructure:

| Level | Definition |
|---|---|
| **VCF Private Cloud** | The highest organizational boundary. A private cloud contains one or more fleets and represents the entirety of an organization's VCF footprint. |
| **VCF Fleet** | A set of VCF instances managed by a single deployment of fleet-level management components (VCF Operations and VCF Automation). A fleet is the governance and shared-services umbrella. |
| **VCF Instance** | A discrete VCF deployment containing its own management domain, its own SDDC Manager back end, and optionally one or more workload domains. Instances are often aligned to a physical site or region. |
| **VCF Domain** | A lifecycle and workload isolation boundary within an instance. Every instance has exactly one management domain; additional VI workload domains are optional. |
| **vSphere Cluster** | The familiar grouping of ESXi hosts within a domain, providing compute, storage, and network resources. |

### How It Works

The hierarchy is more than just naming. It defines *who owns what*. Fleet-level management components---VCF Operations, VCF Automation, the Identity Broker---sit above individual instances. They provide centralized certificate management, password rotation, license administration, configuration drift detection, and lifecycle orchestration across every instance in the fleet. But they do not merge instance-level control planes. Each instance retains its own vCenter, its own NSX Manager, and its own SDDC Manager back end. This is a critical architectural distinction: the fleet centralizes governance without collapsing operational autonomy.

Fleet topologies are flexible. A single-site organization might run one fleet containing one instance. A global enterprise might run one fleet spanning instances in Frankfurt, Singapore, and Virginia---or separate fleets per region with distinct governance boundaries. The SSO trust boundary defines the outer edge of a fleet; instances within a fleet share an SSO domain, while separate fleets maintain independent identity fabrics.

### Why It Matters

For years, customers managing multiple VCF deployments had no formal construct to represent "all of my VCF infrastructure." Each SDDC Manager was an island. Patching ten sites meant logging into ten consoles. The fleet construct eliminates that fragmentation. It gives infrastructure architects a vocabulary that maps cleanly to organizational structures---regions, business units, compliance zones---and it gives operations teams a single pane through which to enforce consistency at scale.

---

## Terminology Changes: A Complete Reference

VCF 9 renames or replaces several foundational components. The following table consolidates every major terminology change:

| VCF 5.x / Prior Name | VCF 9.0 Name | Notes |
|---|---|---|
| Cloud Builder | **VCF Installer** | New appliance; UI-driven workflow replaces the Deployment Parameters Workbook (Excel spreadsheet). |
| SDDC Manager (UI) | **VCF Operations** (fleet management section) | The SDDC Manager UI is deprecated in VCF 9.0. SDDC Manager persists as a back-end component but will be fully deprecated in a future release. |
| VMware Aria Operations | **VCF Operations** | Monitoring, observability, cost management, and fleet governance consolidated under one brand. |
| VMware Aria Automation | **VCF Automation** | Self-service cloud consumption, catalog, IaC, and tenant management. |
| VMware Aria Suite Lifecycle | **VCF Fleet Management** | Lifecycle management of fleet-level components. |
| VMware Aria Operations for Logs | **VCF Operations for Logs** | Centralized log analytics, now accessed from within VCF Operations. |
| VMware Aria Operations for Networks | **VCF Operations for Networks** | Network observability and planning, also accessed from within VCF Operations. |
| vCloud Director (VCD) | **VCF Automation** (tenant model) | Service provider multi-tenancy migrated to the VCFA provider/org model. |
| VMware Site Recovery Manager | **VMware Live Recovery** | DR orchestration rebranded and expanded with vSAN-native replication. |
| PowerCLI (VMware.PowerCLI) | **VCF PowerCLI (VCF.PowerCLI)** | Module renamed; new versioning aligned to VCF releases. |

Understanding these name changes is essential not just for navigating the new UI but for reading release notes, searching knowledge bases, and communicating with VMware support. When a colleague mentions "Aria Operations," they mean VCF Operations. When documentation references "Cloud Builder," it means VCF Installer. The sooner your team internalizes this mapping, the smoother the transition will be.

---

## VCF Operations Console

### What It Replaced

In VCF 5.x, Day 2 operations were fragmented across SDDC Manager (lifecycle, certificates, passwords, network pools), Aria Operations (monitoring, capacity, cost), Aria Operations for Logs (log analytics), Aria Operations for Networks (network visibility), and vCenter (host management, cluster operations). Administrators experienced the infamous "swivel-chair effect," toggling between consoles during outages and change windows.

### What It Is

VCF Operations in VCF 9 is the *single, unified operations console* for the private cloud. It is not simply a rebranded Aria Operations. It is an expanded platform that absorbs functions previously scattered across SDDC Manager and multiple Aria products, presenting them through a single UI with shared navigation, shared identity, and shared context.

### Capabilities in Depth

**Fleet Management.** VCF Operations provides a fleet-level view of all instances, domains, and clusters. From this view, administrators can manage licenses centrally, enforce configuration baselines, rotate certificates across every component in the fleet without service disruption, and manage account passwords across all VCF components with a consolidated status dashboard. Scheduled drift detection for vCenter and cluster objects proactively identifies configuration deviations from the desired state, flagging them before they become incidents.

**Lifecycle Management.** The upgrade planner and patch planner now live in VCF Operations. Administrators select target versions for individual VCF core components---ESXi, vCenter, NSX, SDDC Manager---and the planner calculates dependencies, validates compatibility, and orchestrates the upgrade sequence. This is a significant shift from VCF 5.x, where lifecycle operations were initiated in SDDC Manager.

**Observability Workbench.** VCF Operations integrates metrics, logs, and network telemetry into a single investigative workflow. During an incident, an administrator can begin with a performance anomaly detected by the metrics engine, pivot to correlated log entries surfaced by VCF Operations for Logs, and then inspect the affected network path through VCF Operations for Networks---all without leaving the console. Health and diagnostics findings surface VMSA/CVE exposure, known-issue KB matches, and trending anomalies in real time.

**Security Operations.** SecOps dashboards provide visibility into authentication events, user activity, security advisories, and compliance posture. The platform embeds sovereign-by-design capabilities including data residency tags, geo-fencing, and automated certificate rotation, allowing organizations to satisfy regulatory requirements as part of their operational workflow rather than as an afterthought.

**Network Operations.** VCF Operations is now the observability portal for NSX. Transport nodes, edge clusters, logical switches, and distributed firewall rule hit counts are all visible and searchable from within VCF Operations, providing infrastructure teams with a network-aware operations experience.

### Why It Matters

The consolidation of operations into a single console is the most tangible manifestation of VCF 9's philosophy shift. It reduces tool sprawl, eliminates context-switching during critical incidents, and creates a single audit trail for all administrative actions. For organizations subject to regulatory oversight, the ability to demonstrate that all operations---lifecycle, security, configuration---flow through one governed console is a significant compliance advantage.

---

## VCF Automation (VCFA)

### What It Is

VCF Automation is the consumption layer of VCF 9. It replaces both VMware Aria Automation and, for service provider use cases, VMware Cloud Director (VCD). It enables IT teams and cloud service providers to deliver a self-service private cloud for AI workloads, Kubernetes clusters, and traditional VM-based applications.

### The Provider and Organization Model

VCFA introduces a multi-tenancy model built around *provider* and *organization* (tenant) constructs:

- The **Provider Organization** is operated by the infrastructure team. It owns the physical resources, defines cloud zones, creates catalog items, and establishes governance policies. The provider maps VCF compute, storage, and network resources into consumable abstractions.
- **Tenant Organizations** represent internal business units or, in service-provider scenarios, external customers. Each organization can connect to its own external identity provider via OIDC, maintaining complete identity isolation. Organizations consume resources through a self-service portal, subject to quotas, placement policies, and budget constraints defined by the provider.

This model is familiar to administrators who have worked with VCD's provider/tenant architecture, but it is now natively integrated into VCF rather than running as a separate product with its own lifecycle.

### Infrastructure as Code and the Terraform Provider

The Terraform Provider for VCFA (TP4VCFA) enables full automation of both provider-side and tenant-side configuration. Provider modules handle cloud zone definitions, catalog publishing, and quota enforcement. Tenant modules handle VM and Kubernetes deployment, network configuration, and day-two operations. This means that the entire private cloud---from infrastructure provisioning to workload deployment---can be expressed as version-controlled code.

### Argo CD and GitOps Integration

VCF 9 ships with a Broadcom-built Argo CD Operator, available as part of the vSphere Supervisor Release 9.0.0.0100. The operator enables GitOps-driven management of Kubernetes clusters, VM workloads, and vSphere Pods. The workflow is familiar to any GitOps practitioner: developers commit application code or infrastructure definitions to a Git repository; Argo CD continuously monitors those repositories; upon detecting a change, Argo CD applies the desired state to the target clusters automatically. This closes the loop between version control and infrastructure reality, enabling automated, auditable, version-controlled delivery pipelines.

### Event Broker and Extensibility

VCF Automation retains and extends the event broker model from Aria Automation. Event topics---such as Compute Allocation, Compute Post Provision, and Deployment Requested---can trigger extensibility subscriptions that execute Orchestrator workflows or Action-Based Extensibility (ABX) actions. Subscriptions can be scoped to individual projects or shared across all projects in an organization. Blockable subscriptions enable approval gates, ITSM ticket creation, IP address management integration, and custom validation logic, making the platform extensible without forking the core code.

### Budget Constraints and Cost Governance

VCFA introduces project-level budget constraints. A project constraint is a key-value governance definition that controls which resources a deployment request may consume or must avoid. Combined with VCF Operations' embedded cost control (discussed later in this chapter), this creates a closed-loop governance model: the provider defines budgets, VCFA enforces them at deployment time, and VCF Operations reports on actual consumption against those budgets.

### Why It Matters

VCFA transforms VCF from an infrastructure platform into a cloud operating model. Application teams no longer file tickets and wait for infrastructure administrators to provision VMs. They browse a catalog, select a blueprint, and deploy within guardrails that the infrastructure team has defined. For service providers, the provider/org model provides native multi-tenancy without the operational overhead of a separate VCD stack. For enterprises embracing GitOps, the Argo CD integration means that VCF infrastructure can participate in the same CI/CD pipelines that manage application code.

---

## vSphere 9: The Hypervisor Evolves

### Virtual Hardware Version 22

**What It Is.** vSphere 9 introduces Virtual Hardware Version 22 (vmx-22), the latest revision of the virtual machine hardware abstraction layer. The headline specifications are dramatic:

- **960 vCPUs** per virtual machine (up from 768 in v21)
- **24 TB of memory** per virtual machine (up from 16 TB in v21)
- Native support for AMD Zen 5 (Turin) and Intel Granite Rapids processors
- NVMe 1.3c virtual controller support
- Enhanced PCI passthrough capabilities

**How It Works.** When you create or upgrade a VM to hardware version 22, ESXi 9 exposes the expanded resource ceilings to the guest operating system. The new hardware version also enables platform features that require guest-visible changes, such as updated virtual NVDIMM support and enhanced device emulation.

**Why It Matters.** Monster VMs are no longer an edge case. SAP HANA, Oracle in-memory databases, large-scale AI training frameworks, and high-performance computing workloads increasingly demand resource allocations that would have required bare metal just a few years ago. Virtual Hardware v22 ensures that vSphere remains a viable platform for these workloads without compromising on the operational benefits of virtualization---live migration, snapshotting, resource pooling, and centralized management.

### NVMe Memory Tiering: Production-Ready

**What It Is.** NVMe Memory Tiering allows ESXi to use local NVMe flash devices as a secondary memory tier beneath DRAM. The hypervisor's memory manager transparently migrates cold pages from DRAM to NVMe and promotes hot pages back, expanding effective memory capacity at a fraction of the cost of additional DRAM DIMMs.

**How It Works.** The feature leverages ESXi's existing memory resource management framework. When enabled, ESXi partitions local NVMe devices as tiered memory. The hypervisor continuously monitors page access frequency and uses heuristic-based promotion and demotion algorithms to keep frequently accessed pages in DRAM while relegating infrequently accessed pages to the NVMe tier. Virtual machine profiles define performance expectations, allowing administrators to tune the aggressiveness of tiering based on workload characteristics.

NVMe Memory Tiering was introduced as a technology preview in vSphere 8.0 Update 3. In vSphere 9, it is fully production-ready, with support from major server vendors including Lenovo and Dell, and with comprehensive monitoring integration in VCF Operations.

**Why It Matters.** Memory is often the most expensive component in a server, and it is frequently the constraint that limits VM density. NVMe Memory Tiering breaks that constraint. For workloads with large memory footprints but moderate access locality---virtual desktop infrastructure, file servers, development environments, and certain database workloads---NVMe tiering can increase effective memory capacity by 2x to 4x without proportional cost increases. This translates directly to fewer hosts, lower licensing costs, and higher ROI.

### ESXi Image-Based Management and Stateless Hosts

**What It Is.** ESXi 9 continues the evolution toward a fully image-based, declarative host management model. vSphere Lifecycle Manager (vLCM) images define the desired state of an ESXi host---including the ESXi version, firmware levels, and driver versions---as a single, versioned artifact. In VCF 9, all ESXi clusters must use vLCM images; legacy baseline-based management is no longer supported.

**How It Works.** Administrators define a target image in vCenter. vLCM compares the target image against the current state of each host in the cluster, calculates the required changes, and orchestrates a rolling remediation. Hosts can be configured for stateless caching, where the image is stored on a local disk but the canonical state is always the vLCM image, or for stateful installs, where the image is applied persistently. The elimination of baseline-based management simplifies the upgrade matrix and ensures that every host in a cluster runs an identical software stack.

**Why It Matters.** Configuration drift between hosts has been a persistent source of operational incidents in vSphere environments. Image-based management eliminates this class of problem by enforcing a single, auditable desired state. For large-scale deployments with hundreds or thousands of hosts, the operational savings are substantial: one image definition replaces hundreds of individual patching workflows.

### Deprecated and Removed Virtual Devices

vSphere 9 continues the platform's modernization of virtual device emulation. The LSI SAS virtual SCSI controller, long the default for new VMs, is being phased out in favor of the VMware Paravirtual (PVSCSI) controller, which delivers significantly better I/O performance. vSphere 9 can automatically replace LSI SAS controllers with PVSCSI during hardware upgrades, simplifying the transition. Legacy device emulations---including older virtual NIC types and deprecated storage controllers---have been removed or deprecated. Administrators planning upgrades from vSphere 8 should audit their VM inventories for deprecated devices before initiating the migration. In particular, the CIM/SFCB and OpenSLP stacks have been fully removed from ESXi 9, and Intel Optane PMEM is no longer supported.

---

## NSX Enhanced Data Path: 3x Network Performance

### What It Is

NSX Enhanced Data Path (EDP) is a high-performance network data plane that replaces the standard NSX virtual switch for packet processing. In VCF 9, EDP Standard mode is the *default* switch mode when configuring NSX, meaning every new VCF 9 deployment benefits from enhanced networking performance out of the box.

### How It Works

The performance improvements stem from three architectural changes:

1. **Kernel Bypass and DPDK.** EDP uses Data Plane Development Kit (DPDK) polling-mode drivers instead of traditional interrupt-driven packet processing. Rather than waiting for the kernel to signal that a packet has arrived, EDP continuously polls the NIC for new packets, eliminating interrupt overhead and reducing latency to microsecond levels.

2. **Direct Data Forwarding.** Packets bypass portions of the traditional virtual switch logic, following a streamlined forwarding path that reduces per-packet CPU cycles.

3. **Multi-Core Parallelization.** EDP distributes packet processing across multiple CPU cores, avoiding the single-core bottlenecks that can constrain throughput in interrupt-driven models.

The result, validated in real-world deployments, is up to **3x more switching performance** compared to the standard NSX data path.

### DPU Offload

For environments that require even greater performance or that need to reclaim CPU cycles for workload processing, EDP supports offloading network operations to Data Processing Units (DPUs, also known as SmartNICs). When a DPU is present, NSX moves overlay encapsulation/decapsulation, firewall rule evaluation, and other network functions from the host CPU to the DPU's embedded processor. This delivers higher throughput at lower latency with minimal CPU overhead on the hypervisor, effectively making networking "free" from the perspective of workload compute.

### When It Matters

EDP is mandatory in VCF 9---you must have an EDP-compatible network adapter. This requirement reflects Broadcom's confidence that the performance benefits justify the hardware dependency. Workloads that benefit most include high-frequency trading platforms, real-time analytics pipelines, AI inference services, large-scale east-west traffic between microservices, and any application where network latency directly impacts user experience or business outcomes. Even for general-purpose workloads, the reduced CPU overhead translates to higher VM density and lower infrastructure costs.

---

## VPC Networking in VCF

### What It Is

VMware Virtual Private Cloud (VPC) brings the public-cloud networking paradigm to VCF. A VPC is a secure, isolated multi-tenant network environment hosted within a VCF private cloud. It enables different teams, projects, or tenants to operate in logically separated network spaces with self-service network provisioning, all while sharing the same physical infrastructure.

### How It Works

The VPC model in VCF 9 is built on NSX Projects and VPC Gateways:

- An **NSX Project** defines a tenant. It groups networking and security objects, enables delegation of administrative controls, and enforces resource quotas. Each project operates in its own administrative namespace.
- Within a project, one or more **VPCs** are created. Each VPC owns its own VPC Gateway, which routes packets between the VPC's subnets. Users can create on-demand subnets defined as *private* (isolated) or *public* (routable to external networks).
- A **Transit Gateway** within each project connects all VPC Gateways together, enabling controlled inter-VPC communication.

Available network services within a VPC include Network Address Translation (NAT), Gateway Firewall, and the vDefend Distributed Firewall. VCF 9 makes deployments VPC-ready out of the box, and VPC configuration can be initiated directly from vCenter by selecting VPC configuration options in the network tab.

### Edgeless Connectivity

One of the most significant innovations in VCF 9's VPC implementation is **distributed network connectivity without NSX Edge VMs**. The VPC fabric can reach the physical network directly from the host, bypassing the need for Edge node deployment and advanced routing configuration. This simplifies the external connectivity model dramatically, reducing operational complexity and eliminating a common performance bottleneck.

### Why It Matters

VPC networking is the bridge between private cloud operations and the public cloud experience that application teams increasingly expect. Instead of filing a ticket requesting a new VLAN and waiting for a network administrator to configure it, a development team creates a VPC, defines its subnets, attaches firewall policies, and begins deploying workloads---all within minutes, all governed by policies the infrastructure team has defined. For organizations operating hybrid or multi-cloud architectures, VPC provides a familiar abstraction that aligns private cloud networking with AWS VPC or Azure VNet semantics, reducing cognitive overhead for teams that work across environments.

---

## vSAN: Global Cluster-Wide Deduplication

### What It Is

vSAN in VCF 9 introduces software-based, cluster-wide global deduplication for the Express Storage Architecture (ESA). Unlike previous vSAN deduplication, which operated per disk group, global deduplication identifies and removes redundant 4 KB data blocks across *the entire cluster*, dramatically improving data reduction ratios.

### How It Works

The deduplication engine operates as a **post-process** mechanism. After data is written to vSAN, the deduplication engine runs in the background during periods of low CPU utilization, scanning for duplicate 4 KB blocks across all hosts in the cluster. When duplicates are identified, redundant copies are eliminated and replaced with references to a single canonical block. The deduplicated data is then sharded using a novel distribution algorithm that spreads deduplicated blocks across hosts, delivering both scalability and resilience.

This post-process architecture is a deliberate design choice. Inline deduplication---which identifies duplicates at write time---imposes CPU overhead on the write path and can introduce latency. By deferring deduplication to background processing, vSAN preserves write performance while still achieving aggressive data reduction.

### What Changed from Prior Versions

In vSAN 7.x and 8.x with OSA (Original Storage Architecture), deduplication operated at the disk-group level. The deduplication domain was limited to the capacity devices behind a single disk group on a single host. This constrained the deduplication ratio because identical blocks on different hosts or in different disk groups were invisible to each other.

vSAN ESA in VCF 9 eliminates this limitation. The deduplication domain is now the entire cluster. As the cluster grows---more hosts, more capacity---the deduplication domain grows with it, and deduplication ratios typically improve. In benchmarks, VCF 9 global deduplication achieves up to **8x data reduction**, compared to the 2x typical of per-disk-group deduplication in older architectures.

### RPQ Note

Global deduplication is available for vSAN ESA clusters and is included in the VCF license. It does not require a separate license or entitlement. However, organizations running vSAN OSA will not benefit from this feature; the cluster-wide deduplication engine is an ESA-exclusive capability. If your hardware does not meet ESA requirements, consult your VMware account team about a hardware refresh program or Request for Product Qualification (RPQ) to explore options.

### Why It Matters

Storage capacity is a direct cost driver. An 8x reduction in storage footprint means that a cluster that previously required 100 TB of raw flash can achieve the same effective capacity with 12.5 TB. For environments with significant data redundancy---VDI, development/test, template-heavy deployments---the savings are transformational. And because the deduplication runs post-process with minimal CPU impact, it delivers these savings without compromising workload performance.

---

## Native vSAN-to-vSAN Replication, Immutable Snapshots, and VMware Live Recovery

### What It Is

VCF 9 introduces native vSAN-to-vSAN replication with deep, immutable snapshots, integrated with VMware Live Recovery for full disaster recovery and cyber recovery orchestration. This eliminates the need for third-party replication appliances or array-based replication for many use cases.

### How It Works

**Native Replication.** vSAN replicates data at the VM level from a source vSAN cluster to a target vSAN cluster. Unlike array-based replication, which operates at the LUN or volume level, VM-level replication provides granular control over which VMs are protected, their RPO targets, and their recovery priorities. Enhanced vSphere Replication provides automated load balancing and scaling to achieve higher performance, supporting an RPO as low as **1 minute** when enabled with VMware Live Recovery.

**Immutable Snapshots.** Replicated data can be stored as immutable snapshots in a vSAN-based data vault. Immutable snapshots cannot be modified, encrypted by ransomware, or deleted before their retention period expires. With VCF 9 and VMware Live Recovery 9.0.3, organizations can build an on-premises isolated recovery environment and store up to 200 immutable snapshots per protected VM, with configurable RPO between one minute and 24 hours.

**VMware Live Recovery.** VMware Live Recovery (formerly VMware Site Recovery Manager) provides full DR orchestration: automated failover, failback, recovery plan testing, and compliance reporting. The integration with vSAN-native replication simplifies the architecture---no separate replication appliances, no array-level coordination---while delivering enterprise-grade recovery capabilities. A single appliance handles lifecycle management, reducing administrative overhead.

### Why It Matters

Ransomware has made immutable recovery points a board-level priority. vSAN-native replication with immutable snapshots provides a *platform-integrated* answer to this challenge, eliminating the need to bolt on third-party backup and replication products for basic DR and cyber recovery scenarios. The 1-minute RPO ensures that even the most transaction-intensive workloads can be protected with minimal data loss. And because the entire solution---replication, snapshots, orchestration---is integrated into VCF, it participates in the same lifecycle management, monitoring, and governance workflows as the rest of the platform.

---

## Expanded Principal Storage: NFS and Fibre Channel in VCF 9.0.1

### What It Is

Historically, VCF greenfield deployments required vSAN as the principal storage for the management workload domain. VCF 9.0 expanded this by supporting NFSv3 and Fibre Channel VMFS datastores as principal storage for the management domain in greenfield deployments. VCF 9.0.1 extended this further with support for stretched management domains on Fibre Channel-based VMFS principal storage.

### How It Works

During greenfield deployment with the VCF Installer, administrators can now select NFSv3 or Fibre Channel VMFS as the principal storage type for the management domain. The installer validates the storage configuration and proceeds with deployment without requiring any vSAN infrastructure. Additional principal storage types---including iSCSI, NFS v4.1, FCoE, and NVMe over Fabrics (FC, TCP, RDMA)---can be configured using alternative deployment methods, though they are not yet available through the standard greenfield workflow.

### Why It Matters

This is a transformational change for organizations with significant investments in external storage arrays. Many enterprises have purchased NetApp, Pure Storage, Dell PowerStore, or Hitachi Vantara arrays with multi-year support contracts. Previously, deploying VCF meant either maintaining a parallel vSAN infrastructure for the management domain or foregoing VCF entirely. With NFS and FC principal storage support, these organizations can adopt VCF without stranding their storage investments. The "VCF is vSAN-only" narrative is officially over, and VCF 9 is now described by Broadcom as "ready for all storage."

---

## VCF PowerCLI 9.0

### The Rename

The PowerShell automation module has been renamed from **VMware.PowerCLI** to **VCF.PowerCLI**, aligning the module name with the VCF release versioning and branding. The module is published to the PowerShell Gallery as `VCF.PowerCLI`.

### 70% Faster

Core cmdlets have been rewritten as .NET-based compiled classes, eliminating the overhead of interpreted PowerShell script modules. The most visible improvement is module import time: the legacy VMware.PowerCLI module took over 60 seconds to import; VCF.PowerCLI imports in approximately 18 seconds. Across the board, execution performance has improved by up to 70% in common automation scenarios.

### The Initialize/Invoke Pattern

VCF PowerCLI 9.0 introduces auto-generated SDK cmdlets that follow a consistent two-verb pattern:

- **Initialize-** cmdlets prepare data structures (request bodies) for API calls. They construct the objects that the API expects, with parameter validation and type checking.
- **Invoke-** cmdlets execute the actual operations on the server side, sending the prepared request and returning the response.

This pattern separates data construction from execution, making scripts easier to read, debug, and compose. It also enables a "prepare and review" workflow where administrators can inspect the request body before committing the operation.

### New Modules

VCF PowerCLI 9.0 ships with new modules for NSX and SDDC Manager, expanding the automation surface to cover network configuration and lifecycle operations that previously required direct API calls or the NSX Manager UI.

### Why It Matters

PowerCLI is the automation backbone for thousands of VMware environments. The 70% performance improvement alone justifies the upgrade for teams running large-scale automation. The Initialize/Invoke pattern aligns PowerCLI with modern API design practices, making it more approachable for developers who are accustomed to working with REST APIs and SDKs rather than monolithic cmdlets.

---

## Unified SDK, OpenAPI 3.0, VI JSON, and the API Changelog

### Unified VCF SDK

VCF 9 consolidates previously separate component SDKs---for vCenter, NSX, vSAN, SDDC Manager, and others---into a single, unified VCF SDK available for both Python and Java. All packages are published to Maven Central (Java) and PyPI (Python), and the source is available on the VMware GitHub repository. This means developers no longer need to hunt across multiple repositories, manage conflicting dependency versions, or learn different SDK conventions for each VCF component.

### OpenAPI 3.0

Approximately 90% of VCF APIs now conform to the OpenAPI 3.0 specification. vCenter 9.0 adds OpenAPI 3.0 support for all vCenter and vSAN APIs, complementing the existing vCenter REST APIs. OpenAPI specifications define APIs in a standardized, machine-readable format (YAML/JSON), enabling automatic generation of client libraries, documentation, and testing harnesses in any language.

### VI JSON Protocol

The VI JSON protocol converts legacy SOAP-based vSphere APIs into a modern, REST-style experience. Rather than generating WSDL stubs and marshaling XML, developers can interact with the full breadth of vSphere APIs using JSON payloads over HTTP. This dramatically lowers the barrier to entry for integrating with vSphere from modern application frameworks, serverless functions, and CI/CD pipelines.

### API Changelog

For the first time, VCF publishes a formal API changelog with each release. The changelog provides a consolidated summary of all modifications across every VCF component: newly introduced APIs, updated APIs, deprecated APIs, removed APIs, and structural modifications. This is a seemingly small change with outsized impact. Previously, determining what had changed in the API layer between releases required reading multiple sets of release notes, comparing OpenAPI specifications manually, or discovering changes through trial and error. The changelog makes API evolution transparent and predictable.

### Why It Matters

The developer experience around VMware APIs has historically been a source of friction. The proliferation of SOAP, REST, and custom protocols across different products created a steep learning curve. VCF 9's unified SDK, universal OpenAPI 3.0 adoption, VI JSON bridge, and formal changelog collectively represent the most significant improvement in VMware's developer experience in over a decade. For organizations building automation, integrations, or custom management tools, VCF 9 is dramatically easier to program against than any prior release.

---

## Lifecycle Overhaul: Component-Level Patching and BOM-Driven Upgrades

### What It Is

VCF 9 introduces a flexible, Bill of Materials (BOM)-driven lifecycle management model. Rather than upgrading all components in lockstep to a single VCF version, administrators can now select specific target versions for individual VCF core components---ESXi, vCenter, NSX Manager, SDDC Manager---within a domain.

### How It Works

The **upgrade planner** in VCF Operations presents a matrix of available target versions for each component. Administrators select the desired target version for each component independently, subject to compatibility constraints that the planner enforces automatically. The planner calculates the correct upgrade sequence, validates prerequisites, and orchestrates the rollout.

The **patch planner** enables updating individual components with maintenance patches in any order. Patch order only matters for major and minor version upgrades; maintenance patches can be applied independently, giving administrators the flexibility to address critical vulnerabilities immediately without waiting for a full upgrade cycle.

For fleet-level components, the upgrade sequence follows a defined order: fleet-level management components (VCF Operations, Fleet Management) are updated first, followed by instance-level core components (SDDC Manager, NSX, vCenter, ESXi) within each instance.

### Why It Matters

The rigid, all-or-nothing upgrade model of prior VCF versions was one of the platform's most common pain points. Upgrading VCF 4.x or 5.x often meant coordinating a "big bang" update that touched every component in a single maintenance window. If one component failed compatibility validation, the entire upgrade stalled. BOM-driven lifecycle management eliminates this problem. You can patch ESXi to address a CVE on Monday, upgrade NSX to enable a new feature on Wednesday, and upgrade vCenter when your change advisory board approves it next month. Each component moves independently within the guardrails of the compatibility matrix.

---

## The 6+1 Support Model and New Release Cadence

### What Changed

VCF 9 introduces a new support model and release cadence:

**Support Model: 6+1.** The previous 5+2 model provided five years of general support with the option to purchase two years of extended support. The new 6+1 model provides **six years** of support for each major release, with the option to purchase **one additional year** of extended support. While the total potential support period decreases by one year (seven vs. eight), the guaranteed general support period increases by one year (six vs. five), which is the metric most organizations care about.

**Release Cadence: Three-Year Majors, Nine-Month Minors.** Major releases will now ship every three years (up from two), and minor releases will ship every nine months (up from six). Each major release will have four minor releases (e.g., VCF 9.0, 9.1, 9.2, 9.3). Initial minor releases receive 27 months of support each; the final minor release in a major version receives 45 months of support, giving customers a generous window to plan their upgrade to the next major.

### Why It Matters

The shift to three-year major cycles and nine-month minor cycles directly addresses the most common feedback from VCF customers: "We cannot upgrade this frequently." The previous two-year major cycle, combined with six-month minor releases, meant that organizations often fell behind, running unsupported versions because they could not schedule upgrade windows frequently enough. The new cadence gives more time between releases, longer support windows, and more flexibility to choose an upgrade path that aligns with organizational change-management processes.

For planning purposes, the VCF 9 timeline looks approximately like this: VCF 9.0 (June 2025), VCF 9.1 (approximately Q1 2026), VCF 9.2 (approximately Q4 2026), VCF 9.3 (approximately Q3 2027), with VCF 10.0 expected around 2028.

---

## Embedded Cost Control and Governance

### What It Is

VCF 9 embeds cost management and governance directly into the platform, eliminating the need for third-party cost-management tools for most private cloud use cases.

### How It Works

**Chargeback and Showback.** VCF Operations provides dashboards that translate resource consumption---CPU hours, memory GB-hours, storage GB-days, network throughput---into invoice-ready monetary amounts for each tenant, project, or business unit. Showback reports provide transparency without billing enforcement; chargeback integrates with financial systems for actual cost recovery.

**Cost Predictability.** Capacity forecasting models project future resource consumption and associated costs based on historical trends, planned deployments, and growth assumptions. These projections enable finance teams to budget for infrastructure with confidence rather than relying on rough estimates.

**Budget Alerts and Compliance.** Administrators define budget thresholds at the project, organization, or fleet level. When consumption approaches or exceeds the budget, alerts are triggered. Usage forecasts predict when a budget will be exhausted, enabling proactive intervention before overspend occurs.

**Business Intents.** Business Intents are governance definitions that control where workloads are permitted to reside based on compliance requirements, licensing constraints, and organizational policy. For example, a business intent might require that all GDPR-regulated workloads run on hosts in the Frankfurt instance, or that development workloads avoid hosts licensed for production use.

### Why It Matters

In most enterprises, infrastructure costs are opaque. Business units consume resources without visibility into the costs they generate, and finance teams lack the data to hold them accountable. VCF 9's embedded cost control closes this gap. By providing cost visibility at the project and tenant level, with budget enforcement and predictive analytics, the platform enables a FinOps operating model without requiring a separate FinOps tool. For organizations that have struggled to justify private cloud investments against public cloud alternatives, the ability to demonstrate precise cost-per-workload metrics is a powerful argument for keeping workloads on-premises.

---

## From VCF 5.2 to VCF 9.0: A Narrative Comparison

To fully appreciate the magnitude of VCF 9, it helps to walk through the same set of administrative tasks as they would be performed in VCF 5.2 versus VCF 9.0. This is not a feature checklist comparison---it is a story about how the daily experience of operating a private cloud has changed.

### Day 0: Deploying a New VCF Instance

**VCF 5.2.** You download the Cloud Builder OVA and deploy it to a temporary vCenter or directly to an ESXi host. You open the Deployment Parameters Workbook---a Microsoft Excel spreadsheet with dozens of tabs and hundreds of cells---and painstakingly fill in hostnames, IP addresses, credentials, network pool definitions, and DNS records. You validate the workbook, upload it to Cloud Builder, and initiate the bringup process. If a validation error occurs, you return to the spreadsheet, correct the value, re-upload, and re-validate. The process is functional but fragile; a single typo in the workbook can derail the deployment.

**VCF 9.0.** You deploy the VCF Installer appliance. Instead of an Excel workbook, you are presented with a UI-driven workflow that guides you through each configuration step with real-time validation and contextual help. You select your principal storage type---vSAN, NFS, or Fibre Channel---directly in the wizard. The installer validates connectivity, DNS resolution, and credential access as you proceed, catching errors immediately rather than deferring them to bringup. The experience is closer to a cloud provider's onboarding wizard than to an enterprise infrastructure deployment tool.

### Day 1: Configuring Networking for a New Tenant

**VCF 5.2.** You log into NSX Manager and manually create a Tier-1 gateway, define segments, configure DHCP, attach firewall rules, and coordinate with the network team for physical connectivity. If you need to repeat this for another tenant, you repeat the entire manual process or write custom automation.

**VCF 9.0.** You define an NSX Project for the tenant and create one or more VPCs within it. Each VPC gets its own gateway automatically. The tenant's administrator can create on-demand subnets---public or private---through the VCF Automation self-service portal or directly from vCenter. Firewall policies are inherited from the project template. External connectivity is available without deploying Edge VMs, using the new distributed connectivity model. What took hours and cross-team coordination in VCF 5.2 takes minutes and is fully self-service in VCF 9.

### Day 2: Monitoring and Troubleshooting

**VCF 5.2.** An alert fires in Aria Operations indicating high CPU ready time on a cluster. You investigate in Aria Operations, but the root cause appears to be network-related. You switch to Aria Operations for Networks to inspect the overlay. You find a log entry that might be relevant, so you switch to Aria Operations for Logs. Each tool has its own navigation, its own session, and its own query language. You lose context with every switch.

**VCF 9.0.** The same alert fires in VCF Operations. You click through to the affected cluster, see correlated log entries in the same view, pivot to the network topology for the affected VMs, and identify a misconfigured distributed firewall rule that is causing packet drops. You remediate the rule, verify the fix, and close the incident---all without leaving VCF Operations. The Observability Workbench provides a unified investigative surface that treats metrics, logs, and network telemetry as facets of the same data set.

### Lifecycle: Patching a Critical CVE

**VCF 5.2.** A critical ESXi CVE is published. You log into SDDC Manager, check for available bundles, and discover that the patch is bundled with a full VCF update that also includes new versions of vCenter and NSX. You cannot patch ESXi independently; you must upgrade the entire stack or apply the patch manually outside of VCF lifecycle management, which takes the environment out of the supported configuration matrix. You schedule a multi-hour maintenance window, coordinate across teams, and upgrade everything at once.

**VCF 9.0.** You open the patch planner in VCF Operations. You select the ESXi patch for the affected domain. The planner validates compatibility and confirms that the patch can be applied independently without upgrading vCenter or NSX. You schedule the remediation for the next available window, and the planner orchestrates a rolling host upgrade that completes with zero workload downtime. The vCenter and NSX upgrades can follow on their own schedule, when the change advisory board approves them.

### Automation: Deploying a Workload

**VCF 5.2.** An application team submits a request for three VMs with specific CPU, memory, and storage configurations. The request goes through an approval workflow in your ITSM tool. An infrastructure administrator logs into vCenter, creates the VMs manually or runs a PowerCLI script, assigns them to the correct network, and notifies the application team. Elapsed time: two to five business days.

**VCF 9.0.** The application team browses the VCF Automation catalog, selects a VM blueprint that matches their requirements, and submits the deployment. VCFA validates the request against the project's budget constraints, placement policies, and quota limits. The event broker triggers an approval workflow in ServiceNow via an extensibility subscription. Upon approval, VCFA provisions the VMs automatically, attaches them to the correct VPC subnet, and notifies the application team. Elapsed time: minutes to hours, depending on approval latency. The entire process is auditable, repeatable, and governed by policy.

### Developer Experience: Building Integrations

**VCF 5.2.** Your DevOps team needs to integrate VCF with their CI/CD pipeline. They discover that vCenter uses SOAP APIs, NSX uses a REST API with a custom authentication model, SDDC Manager uses its own REST API, and Aria products each have their own APIs with separate documentation. They spend weeks writing integration code that handles four different authentication mechanisms, three different payload formats, and zero formal changelog to track what changes between updates.

**VCF 9.0.** Your DevOps team installs the VCF SDK from PyPI. They authenticate once using the unified identity model. They call vCenter, NSX, and SDDC Manager APIs through a single, consistent SDK with OpenAPI 3.0 specifications and VI JSON support. When VCF 9.0.1 is released, they consult the API changelog to understand exactly what changed. They update their integration in an afternoon.

### Cost Governance: Tracking Infrastructure Spend

**VCF 5.2.** Finance asks for a breakdown of infrastructure costs by business unit. You export utilization data from Aria Operations, manually correlate it with hardware purchase costs and licensing fees in a spreadsheet, estimate cost allocations based on resource consumption ratios, and deliver a report three weeks later. The report is approximate, difficult to reproduce, and outdated by the time it is delivered.

**VCF 9.0.** You open the cost management dashboard in VCF Operations. Chargeback reports are generated automatically, broken down by organization, project, and deployment. Budget compliance is tracked in real time, with alerts when consumption approaches thresholds. You export the report as a PDF and deliver it to finance the same day it is requested. The data is precise, reproducible, and current.

---

## Looking Ahead

VCF 9 is not just a product release; it is a statement of strategic intent. By centering the platform on operations and consumption, Broadcom is signaling that VMware Cloud Foundation is no longer merely infrastructure software. It is a private cloud operating system---one that competes with public cloud not on price alone but on the completeness of its operational model, the depth of its governance capabilities, and the quality of its developer experience.

The features discussed in this chapter---fleet management, VPC networking, global deduplication, immutable snapshots, NVMe memory tiering, Enhanced Data Path networking, BOM-driven lifecycle management, the unified SDK, embedded cost control---are not isolated improvements. They are interconnected pieces of a coherent platform vision. Each feature makes the others more valuable. VPC networking becomes more powerful when governed by VCFA budget constraints. Global deduplication becomes more impactful when surfaced through VCF Operations' cost dashboards. The unified SDK becomes more useful when the API changelog makes API evolution predictable.

For practitioners preparing to deploy or migrate to VCF 9, the message is clear: this is the release that justifies the platform's ambition. The subsequent chapters of this book will take you through every deployment pathway, configuration workflow, and operational procedure in detail. But the conceptual foundation laid in this chapter---the philosophy shift, the hierarchy, the terminology, the feature depth---is essential context for everything that follows.

Welcome to VCF 9. The private cloud has grown up.


---

# Chapter 3: Architecture

## The Full-Stack SDDC Vision

VMware Cloud Foundation 9 represents the most ambitious architectural reset in VMware's private cloud history. Where previous releases evolved incrementally---adding a component here, bolting on an integration there---VCF 9 takes a fundamentally different approach. Every building block has been aligned to a single version number. vSphere 9, ESX 9, NSX 9, vSAN 9, VCF Operations 9, VCF Automation 9---the entire stack ships as one cohesive platform. This is not cosmetic rebranding. It is an architectural statement: the Software-Defined Data Center is no longer an assembly of loosely coupled products. It is a single product with a single lifecycle.

The full-stack vision of VCF 9 means that compute, storage, networking, security, and automation are designed, tested, and released together. When you deploy a VCF Instance, you are deploying a validated, interoperable stack where every API contract between components has been verified in combination. This eliminates the interoperability matrices that plagued earlier generations, where administrators had to cross-reference NSX compatibility with a specific vCenter build, or determine whether a vSAN release supported a particular ESXi patch level. In VCF 9, the answer is always the same: if it shipped together, it works together.

This architectural philosophy extends to licensing. Instead of the eleven separate license keys that customers managed in previous generations, VCF 9 consolidates to just two: "VMware Cloud Foundation (cores)" and "VMware vSAN (TiBs)." The licensing simplification is not a minor operational convenience---it reflects the deeper truth that VCF is a platform, not a bundle.

Security is baked into this full-stack approach as well. All VCF 9 components---vCenter, ESX, and NSX---run in FIPS 140-2 and FIPS 140-3 enabled mode by default, and this mode cannot be deactivated. Security is not an optional overlay; it is a foundational property of the platform.

Understanding the architecture of VCF 9 is prerequisite to every design decision that follows. Whether you are sizing a management domain, choosing a storage architecture, or planning a multi-region fleet, the architectural constructs described in this chapter form the grammar of every conversation you will have with your engineering teams, your leadership, and your VMware technical account manager.

---

## The Three-Tier Hierarchy: Private Cloud, Fleet, and Instance

VCF 9 introduces a formal organizational hierarchy that fundamentally changes how enterprises think about their private cloud estates. This hierarchy---Private Cloud at the top, Fleet in the middle, and Instance at the bottom---provides the scaffolding for governance, lifecycle management, and operational consistency at any scale.

### Private Cloud

The Private Cloud is the highest-level abstraction. It represents your entire VMware-based infrastructure estate, regardless of how many data centers, geographies, or business units it spans. Think of it as the organizational boundary within which all policy, strategy, and architectural standards originate. A global financial services firm with data centers in New York, London, Singapore, and Sydney has one Private Cloud. A regional healthcare provider with a single data center and a disaster recovery site also has one Private Cloud.

The Private Cloud construct is deliberately abstract. It does not map to a specific appliance, API endpoint, or management console. Rather, it provides the conceptual container within which Fleets and Instances are organized. When your CTO asks, "How large is our private cloud?"---the answer encompasses every Fleet beneath it.

### Fleet

A Fleet is where operational reality begins. A VCF Fleet is a logical grouping of one or more VCF Instances that share a single set of fleet-level management components: one VCF Operations deployment and one VCF Automation deployment. The Fleet is the unit of centralized lifecycle management, identity federation, certificate authority, licensing, and health monitoring.

This is an architectural decision with profound operational implications. Consider a multinational manufacturer with three regional data centers. If all three VCF Instances are placed in a single Fleet, they share one VCF Operations console for lifecycle management, one VCF Automation instance for self-service provisioning, and one unified identity provider configuration. Patching, upgrades, compliance reporting, and capacity planning all flow through a single pane of glass. The trade-off is that the Fleet management components must be reachable from every Instance in the Fleet, and any disruption to the Fleet management layer affects visibility and lifecycle operations across all Instances.

Alternatively, the manufacturer could create two Fleets---one for North America and Europe, another for Asia-Pacific---each with its own VCF Operations and VCF Automation. This provides operational isolation: the Asia-Pacific team can execute lifecycle operations independently, with separate change windows and separate identity configurations. The cost is duplication of management infrastructure and the loss of a single unified view.

The headline capability in VCF 9.0 that makes large-scale Fleet architectures practical is the increase of the maximum supported Round Trip Time (RTT) for fleet-to-instance communication to 300 milliseconds. In previous releases, the latency constraints meant that Fleet management components needed to be relatively close---within the same metropolitan area or connected by low-latency dedicated fiber. With 300ms RTT support, a Fleet can span continents. A VCF Operations instance in Frankfurt can manage VCF Instances in Singapore, provided the network path stays within the 300ms envelope. This is a transformative change for globally distributed enterprises.

However, within a single VCF Instance, the latency requirements remain tighter. The VCF Operations Collector requires a maximum of 50ms RTT to core management components such as vCenter, NSX Manager, and SDDC Manager. This constraint shapes where you place components and how you segment your Instances.

### Instance

A VCF Instance is the operational unit where workloads actually run. Each Instance contains its own management domain, one or more workload domains, and an SDDC Manager appliance that orchestrates domain-level operations. An Instance maps to a single physical or logical site---a data center, a colocation facility, or a campus.

The hierarchy you should internalize is: **Private Cloud > Fleet > Instance > Domain > Cluster**. Every design conversation should begin by establishing where in this hierarchy a decision is being made.

### Real-World Design Implications

Consider a large enterprise with the following requirements: a primary production site, a disaster recovery site 500 kilometers away, and a series of edge locations for retail point-of-sale systems. The architecture might look like this:

- **One Private Cloud** encompassing the entire estate.
- **One Fleet** managing the primary and DR sites (both within 50ms RTT of the Fleet management components).
- **Two Instances**: one at the primary site, one at the DR site, each with its own management domain, workload domains, and SDDC Manager.
- **Edge locations** handled as VCF Edge deployments (single-host or two-host), managed from the primary Fleet.

This topology gives centralized lifecycle management for the core infrastructure while allowing each site to operate independently at the Instance level. If the WAN link between sites fails, each Instance continues to run workloads; only fleet-level operations (lifecycle, automation, centralized monitoring) are interrupted until connectivity is restored.

A different enterprise---say a cloud service provider hosting multiple tenants---might choose a different pattern: one Fleet per major customer, providing complete operational isolation. Each Fleet has its own VCF Operations and VCF Automation, ensuring that one tenant's lifecycle operations never interfere with another's.

---

## VCF Instance Anatomy

Every VCF Instance is built from three fundamental constructs: the Management Domain, one or more Workload Domains, and NSX Edge Clusters. Understanding how these relate to each other is essential to sound architectural design.

### The Management Domain

The Management Domain is the first domain created during VCF deployment. It is the foundation upon which everything else is built. The Management Domain hosts all of the infrastructure management components for the Instance: SDDC Manager, the management vCenter Server, the management NSX Manager cluster, and---if the Fleet management components are co-located---VCF Operations and VCF Automation appliances.

The Management Domain is not optional, and it is not a place for tenant workloads. It is infrastructure for infrastructure. Every VCF Instance has exactly one Management Domain, and it must be treated with the same care as the control plane of a Kubernetes cluster. If the Management Domain becomes unavailable, you lose the ability to manage (though not necessarily to run) the workloads in your Workload Domains.

In VCF 9, the Management Domain requires a minimum of four ESX hosts when using vSAN as principal storage, or a minimum of two ESX hosts when using external storage (Fibre Channel VMFS or NFS). This two-host minimum with external storage is a significant change from earlier releases that required three or four hosts regardless of storage type, and it opens up VCF to smaller environments that could not previously justify the management overhead.

The management vCenter Server manages all ESX hosts within the Management Domain and provides the UI through which many day-two operations are now performed. In VCF 9, a significant number of operational tasks---network pool creation, NSX Edge deployment, host commissioning---have shifted from the SDDC Manager UI to the vCenter UI, reflecting a broader architectural trend toward consolidating the operational surface area.

### Workload Domains

Workload Domains are where your applications run. Each Workload Domain is an isolated unit of compute, storage, and networking. A Workload Domain includes its own vCenter Server instance (or shares one with other domains in specific configurations), one or more vSphere clusters of ESX hosts, and either a dedicated or shared NSX Manager cluster.

The isolation properties of Workload Domains are the key architectural benefit. Each domain has its own pool of storage, its own network configuration, and its own capacity allocation. This enables multi-tenancy at the infrastructure level: different business units, different compliance zones, or different application tiers can each occupy their own Workload Domain with distinct security policies, storage characteristics, and operational boundaries.

A Workload Domain can contain multiple vSphere clusters, allowing you to scale capacity by adding clusters within a domain rather than creating new domains. The decision of when to add a cluster versus when to create a new domain is a common design question. The general guidance is: add a cluster when you need more capacity within the same operational and security boundary; create a new domain when you need isolation---different NSX configurations, different storage policies, different administrative boundaries, or different compliance requirements.

VCF 9 supports up to 24 Workload Domains per Instance (plus the Management Domain, for a total of 25 domains per SDDC Manager). This is a generous limit that few organizations approach, but it is important to understand for capacity planning in service provider or large enterprise scenarios.

### NSX Edge Clusters: Shared vs. Dedicated

NSX Edge Clusters provide centralized networking services---north-south routing, load balancing, NAT, VPN, and gateway firewall---that cannot be distributed to the hypervisor layer. Every Workload Domain that requires external connectivity needs access to an NSX Edge Cluster.

The architectural decision between shared and dedicated NSX Edge Clusters is one of the most consequential networking choices in a VCF design.

**Shared Edge Clusters** allow multiple Workload Domains to share the same NSX Edge nodes, provided those domains share the same NSX Manager cluster. The Edge nodes run on a cluster that may also host workload VMs. This approach minimizes hardware consumption and is appropriate for environments where Workload Domains have modest north-south traffic requirements, where strict performance isolation is not required, and where simplifying management is a priority. A shared Edge Cluster is the right choice for most small-to-medium deployments and for non-production environments.

**Dedicated Edge Clusters** place NSX Edge appliances on a vSphere cluster that contains no other workloads. The Edge nodes get predictable, consistent access to CPU, memory, and network bandwidth. This design pattern is appropriate for environments with high north-south throughput requirements---large-scale web applications, data-intensive services, or any workload where Edge performance variability is unacceptable. The cost is additional hardware: a dedicated Edge cluster requires a minimum of two ESX hosts that serve no other purpose.

A common production pattern is to use a shared Edge Cluster for the Management Domain (whose north-south traffic is typically modest) and dedicated Edge Clusters for Workload Domains with demanding networking requirements. This balances cost against performance isolation.

VCF 9 introduces Virtual Private Cloud (VPC) support within vCenter, allowing administrators to create VPCs with private or publicly advertised subnets directly from the vCenter UI. This new construct sits atop the NSX Edge infrastructure and provides a familiar cloud-like networking model for application teams, while the infrastructure team retains control over the underlying Edge topology.

---

## Component Architecture and Relationships

Understanding how VCF's components relate to each other is essential for troubleshooting, capacity planning, and architectural decision-making. Each component has a specific role, and the connections between them define the operational behavior of the platform.

### VCF Installer

The VCF Installer is the entry point for every new VCF deployment. It is a purpose-built appliance that downloads and manages the binaries required to deploy the VCF platform and then orchestrates the deployment and configuration of all initial components: vCenter Server, NSX Manager, VCF Operations, VCF Automation, and SDDC Manager. The Installer handles the chicken-and-egg problem inherent in deploying a management platform: you need infrastructure to deploy infrastructure, and the Installer provides that bootstrap layer.

The Installer offers two deployment modes---Simple and High Availability---which determine the scale and resilience of the initial deployment. Once the initial deployment is complete, the Installer's role diminishes; ongoing lifecycle management is handled by VCF Operations and SDDC Manager.

### VCF Operations

VCF Operations is the fleet-level control plane for lifecycle management, monitoring, health assessment, licensing, certificate management, and identity federation. It replaces and extends the role that SDDC Manager played in previous VCF releases. Where SDDC Manager operated at the Instance level, VCF Operations operates at the Fleet level, providing a single management surface for all Instances in the Fleet.

VCF Operations is responsible for downloading software bundles, orchestrating upgrades across the fleet, monitoring the health of all VCF components, and providing the primary administrative UI for day-two operations. It includes an integrated Fleet Manager that coordinates lifecycle operations across Instances, ensuring that upgrades are applied consistently and in the correct order.

When deployed in High Availability mode, VCF Operations runs as a three-node cluster, providing resilience against the failure of any single node. The sizing of VCF Operations depends on the number of objects (hosts, VMs, clusters, domains) under management. Broadcom publishes sizing guidelines that scale from Extra Small (up to 700 objects) to Extra Large (up to 100,000 objects).

### VCF Automation

VCF Automation provides self-service cloud provisioning, governance, and multi-cloud management capabilities. It enables IT teams to define cloud templates, create approval workflows, and expose infrastructure-as-a-service catalogs to application teams and tenants. VCF Automation is the evolution of what was previously known as VMware Aria Automation (and before that, vRealize Automation).

VCF Automation 9 introduces two types of organizations: VM-Apps-Organization and All-Apps-Organization. The VM-Apps-Organization is functionally similar to the Aria Automation 8.x model and is the right choice for organizations focused on virtual machine-based workloads. The All-Apps-Organization is an entirely new architecture built on Kubernetes APIs, designed for organizations that need to manage both VM and container workloads through a unified automation framework.

Like VCF Operations, VCF Automation operates at the Fleet level. A single VCF Automation instance serves all VCF Instances within the Fleet, providing a consistent self-service experience regardless of which Instance hosts the underlying workloads.

### vCenter Server 9

vCenter Server 9 is the management plane for vSphere compute resources within each domain. Each domain (Management or Workload) has its own vCenter Server instance, which manages the ESX hosts and clusters in that domain. In VCF 9, vCenter's role has expanded significantly: network pool management, NSX Edge deployment, host lifecycle operations, and ESX image management have all moved into the vCenter UI.

A major architectural change in vCenter 9 is the removal of Enhanced Linked Mode (ELM). In previous releases, ELM allowed multiple vCenter instances to share a Single Sign-On domain and present a unified inventory. VCF 9 eliminates this in favor of the Fleet management model provided by VCF Operations. Identity federation is now handled at the Fleet level, and cross-Instance visibility is provided through VCF Operations rather than through vCenter-to-vCenter linking.

vCenter 9 also introduces the ESX Image Library, which allows administrators to create and manage ESX images at the vCenter level rather than per-cluster, and supports parallel host lifecycle operations across multiple clusters simultaneously.

### ESX 9

ESX 9 (the hypervisor formerly known as ESXi) is the compute foundation. Key architectural changes in ESX 9 include expanded Live Patch coverage---both user-space live patching and NSX transport node live patching can now be applied without an ESX reboot. This dramatically reduces the maintenance window impact for routine patching.

ESX 9 introduces NVMe memory tiering, where fast NVMe devices serve as a second, lower-cost memory tier. Compute-intensive workloads keep their hot working sets in DRAM while cold pages are transparently moved to NVMe. This capability is particularly valuable for memory-hungry workloads like in-memory databases and large-scale analytics.

The NSX configuration for ESX hosts is now integrated with vSphere Config Profiles, allowing unified configuration management for ESX, VDS, and NSX at the cluster level from a single management interface.

### NSX 9

NSX 9 provides the network virtualization layer. All overlay networking, microsegmentation, distributed firewalling, load balancing, and VPN services are delivered through NSX. In VCF 9, NSX integrates exclusively with the native vSphere Distributed Switch (VDS)---support for the standalone N-VDS has been removed entirely. This convergence simplifies the networking stack and eliminates a historical source of configuration complexity.

The NSX Manager cluster (either single-node or three-node) serves as the control plane for NSX within a domain or across multiple domains that share the same NSX Manager. NSX Edge nodes provide the centralized services (routing, NAT, load balancing) that cannot be distributed to the hypervisor.

### vSAN 9

vSAN 9 provides hyperconverged storage. The major architectural direction in vSAN 9 is the Express Storage Architecture (ESA), which is designed from the ground up for NVMe flash storage. vSAN ESA delivers two to five times better performance on the same hardware compared to the Original Storage Architecture (OSA), with up to four times better compression ratios. Global deduplication in ESA is policy-based, giving administrators granular control over where deduplication is applied.

vSAN 9 also introduces a unified storage dashboard that provides real-time visibility into vSAN, SAN, and NAS environments from a single console, reflecting the reality that most enterprises use a mix of storage technologies.

### How They Interconnect

The component relationships form a layered architecture. At the base, ESX 9 provides compute and hosts the vSAN 9 storage layer. NSX 9 provides networking that spans across ESX hosts, with its control plane (NSX Manager) running as VMs on the Management Domain. vCenter 9 manages the ESX hosts and their configurations within each domain. SDDC Manager orchestrates domain-level operations and communicates with vCenter and NSX Manager. VCF Operations sits above all of this, providing fleet-wide lifecycle management, monitoring, and governance. VCF Automation consumes the infrastructure exposed by VCF Operations and vCenter to deliver self-service capabilities to end users.

Data flows upward (telemetry, health, inventory) and commands flow downward (deployments, upgrades, configuration changes). Understanding this layered communication model is essential when troubleshooting: if VCF Operations cannot reach a vCenter, the problem might be in the network path, in the VCF Operations Collector, or in the vCenter appliance itself---and the troubleshooting approach differs for each.

---

## Deployment Modes

VCF 9 supports multiple deployment modes that accommodate environments ranging from enterprise production to lab experimentation.

### Simple (Single-Node) Deployment

The Simple deployment mode deploys a single instance of each management appliance: one vCenter Server, one SDDC Manager, one NSX Manager, one VCF Operations node, and one VCF Automation node. This results in approximately seven appliances for the core management stack.

Simple mode is appropriate for non-production environments, proofs of concept, labs, and development environments. It provides the full functional surface of VCF 9 while minimizing resource consumption. The trade-off is the absence of high availability: if any single management appliance fails, the corresponding management function is unavailable until the appliance is recovered.

### High Availability (Three-Node) Deployment

The HA deployment mode deploys three instances of each clusterable management component: three NSX Manager nodes, three VCF Operations nodes, and three VCF Automation nodes. This results in approximately thirteen management appliances. HA mode is the recommended and supported configuration for production environments. It provides resilience against the failure of any single appliance for each management function.

The resource requirements for HA mode are substantial. Thirteen management appliances, each requiring CPU, memory, and storage, consume a significant portion of the Management Domain's capacity. This is why the Management Domain is typically sized with four or more ESX hosts in production: the management overhead must be absorbed before any capacity is available for other purposes.

### Flexible Combinations

While the VCF Installer UI presents Simple and HA as two discrete options, the underlying platform supports mixed configurations. You can deploy NSX Manager in HA (three nodes) while keeping VCF Operations in Simple mode (one node), or vice versa. This flexibility allows you to right-size the resilience of each component based on your specific requirements and constraints. For example, an organization might deploy NSX Manager in HA mode (because a networking control plane outage affects all workloads) while keeping VCF Automation in Simple mode (because a temporary loss of self-service provisioning is tolerable).

### Two-Node Management Domain with External Storage

For environments with external storage (Fibre Channel VMFS or NFS), the Management Domain can be deployed with as few as two ESX hosts. This dramatically reduces the entry cost for VCF adoption, particularly for organizations that have existing SAN or NAS investments and do not want to deploy vSAN for the management layer. The two-node configuration still supports HA at the vSphere level (vSphere HA can protect management VMs), though the failure of one host leaves no headroom for maintenance.

### Minimum Lab Footprint

For learning and experimentation, VCF 9 can be deployed on remarkably modest hardware. William Lam's widely referenced lab configurations demonstrate that VCF 9 can run on a single physical ESX host using nested virtualization, with the ESX hosts that form the VCF Instance running as VMs on the physical host. This is not a supported production configuration, but it makes VCF accessible to architects, engineers, and students who want to explore the platform without enterprise-scale hardware. The minimum resources for a nested lab deployment are approximately 256 GB of RAM and sufficient NVMe or SSD storage to accommodate the management appliances and a small number of workload VMs.

---

## Required VLAN and VMkernel Networking Design

VCF 9 requires a carefully planned Layer 2 and Layer 3 network design. The networking infrastructure must be in place before the VCF Installer runs, and mistakes in network design are among the most common causes of deployment failure. The following VLANs and VMkernel networks are required.

### Management Network

The management VLAN carries ESX host management traffic, vCenter management traffic, SDDC Manager communications, NSX Manager API traffic, and all inter-appliance communication for the management stack. Every ESX host has a VMkernel port on this VLAN. This is typically a routed network with access to DNS, NTP, Active Directory, and other infrastructure services. An MTU of 1500 is sufficient for management traffic, though many designs standardize on 9000 across all VLANs for consistency.

### vMotion Network

The vMotion VLAN carries live migration traffic between ESX hosts. vMotion is latency-sensitive and bandwidth-intensive, particularly during large-scale migrations or DRS rebalancing events. Each ESX host has a VMkernel port on this VLAN. IP addresses are assigned automatically from a network pool defined in vCenter. An MTU of 9000 (jumbo frames) is strongly recommended and is effectively required for acceptable vMotion performance in production.

### vSAN Network

If vSAN is used as principal storage, a dedicated VLAN carries all vSAN inter-host traffic---data replication, resyncing, and metadata exchange. vSAN traffic is both latency-sensitive and throughput-intensive; in an ESA deployment with NVMe drives, the network can be the bottleneck if not properly sized. Each ESX host has a VMkernel port on this VLAN. An MTU of 9000 is required. For vSAN ESA deployments, 25 GbE is the recommended minimum per host; 100 GbE is preferred for large clusters or performance-sensitive workloads.

### NFS Network (If Applicable)

When NFS is used as principal storage, a dedicated VLAN carries NFS traffic between ESX hosts and the NFS storage array. This VLAN should be isolated from other traffic types to ensure consistent storage performance. MTU 9000 is recommended.

### NSX Host Overlay (Host TEP) Network

The NSX overlay VLAN carries encapsulated tenant network traffic between ESX hosts. Each ESX host has a Tunnel Endpoint (TEP) interface on this VLAN. The minimum MTU for the overlay VLAN is 1600 (to accommodate the Geneve encapsulation header), but an MTU of 9000 is strongly recommended to avoid fragmentation and maximize throughput. This MTU must be supported end-to-end through every physical switch and router in the path.

IP addresses for host TEPs can be assigned via DHCP or from a static IP pool. In production deployments, static IP pools are generally preferred for predictability and troubleshooting simplicity.

### NSX Edge Overlay (Edge TEP) Network

NSX Edge nodes have their own TEP interfaces, which must be on a separate VLAN from the host TEPs. This separation is architecturally required when the Edge TEP VLAN and host TEP VLAN need to be routable to each other via an external router. The Edge TEP VLAN has the same MTU requirements as the host TEP VLAN (minimum 1600, recommended 9000).

### Edge Uplink Networks

NSX Edge nodes connect to the physical network through uplink interfaces. These uplinks carry north-south traffic between the overlay network and the physical network. Typically, two uplink VLANs are configured for redundancy, connecting to separate Top-of-Rack switches or router interfaces. These VLANs carry routed traffic and must be configured with appropriate BGP or static routing to the upstream network infrastructure.

### Design Principles

Several principles apply across all VCF networking:

- **Jumbo frames end-to-end.** Configure MTU 9000 on all VLANs (management, vMotion, vSAN, overlay, Edge TEP, Edge uplink) and verify jumbo frame support on every physical switch, router, and interconnect in the path. A single device that does not support jumbo frames will silently drop oversized frames, causing intermittent and difficult-to-diagnose failures.
- **No VLAN overlap.** Each traffic type must have its own dedicated VLAN. Sharing VLANs between traffic types (for example, combining vMotion and vSAN on the same VLAN) is not supported and will cause deployment validation failures.
- **DNS and NTP first.** Forward and reverse DNS resolution must be functional for all management appliances before deployment begins. NTP must be synchronized across all hosts and appliances. Clock skew and DNS failures are the two most common root causes of deployment and lifecycle operation failures.
- **Physical switch configuration.** All VLANs must be trunked to the physical ports connected to ESX hosts. Port channel or LACP configurations must match the VDS uplink teaming policy. Mismatched teaming configurations are a frequent source of intermittent connectivity issues.

---

## Storage Architecture Decision Framework

VCF 9 supports four principal storage types: vSAN ESA, vSAN OSA, Fibre Channel VMFS, and NFS v3. The choice of storage architecture affects host requirements, network design, operational procedures, and cost. The following framework helps guide this decision.

### vSAN Express Storage Architecture (ESA)

vSAN ESA is the strategic direction for hyperconverged storage in VCF. It is designed from the ground up for NVMe flash storage and modern server hardware. ESA delivers two to five times better performance compared to OSA on equivalent hardware, with up to four times better data reduction (compression and deduplication). ESA's global deduplication is policy-based, giving administrators control over where deduplication is applied and where it is not.

**When to choose ESA:**
- All new greenfield deployments where NVMe-compatible hardware is available.
- Environments that prioritize storage performance and efficiency.
- Deployments where minimizing the host count for a given workload capacity is important (ESA's better data reduction means fewer hosts for the same effective capacity).
- vSAN stretched cluster deployments, where ESA's ability to compress data before cross-site replication reduces WAN bandwidth requirements.

**Minimum requirements:** Three ESX hosts with NVMe storage devices on the vSAN Hardware Compatibility List.

### vSAN Original Storage Architecture (OSA)

vSAN OSA is the legacy hyperconverged storage architecture that supports both SSD and HDD disk groups. It remains supported in VCF 9 for environments with existing OSA-compatible hardware or where NVMe hardware is not yet available.

**When to choose OSA:**
- Brownfield environments with existing OSA disk groups that are being upgraded to VCF 9.
- Environments where NVMe hardware is not available or not cost-justified.
- Note that all new deployments should prefer ESA when hardware permits. OSA is a maintenance-mode architecture; future innovation will focus on ESA.

**Minimum requirements:** Three ESX hosts with compatible SSD and/or HDD devices configured in disk groups.

### Fibre Channel VMFS

VCF 9 introduced support for Fibre Channel VMFS as principal storage for both the Management Domain and Workload Domains during greenfield deployments. This is a landmark change that opens VCF to organizations with significant SAN investments.

**When to choose Fibre Channel:**
- Organizations with existing Fibre Channel SAN infrastructure (arrays, fabric switches, expertise) that want to leverage those investments.
- Environments that require the two-host minimum for the Management Domain (FC allows a two-host management cluster, reducing entry cost).
- Workloads that require specific storage array features (snapshots, replication, deduplication) provided by the external array.
- Environments where separating compute from storage is an architectural requirement (for example, when compute and storage refresh cycles are independent).

**Considerations:** Fibre Channel requires additional infrastructure (SAN fabric, zoning, array management) that is outside VCF's lifecycle management. The storage array is managed independently, and VCF has no visibility into array-level operations.

### NFS v3

NFS v3 is supported as principal storage, providing a network-attached storage option that avoids the need for Fibre Channel infrastructure.

**When to choose NFS:**
- Organizations with existing NFS-capable storage arrays (NetApp, Dell, etc.).
- Environments that want external storage without the complexity of Fibre Channel fabric.
- Small deployments where the two-host Management Domain minimum is desired but Fibre Channel infrastructure is not available.
- Environments where storage administration is handled by a separate team with NAS expertise.

**Considerations:** NFS traffic should be on a dedicated VLAN with MTU 9000. NFS performance is dependent on the network path between ESX hosts and the NFS array; network congestion or misconfiguration directly impacts storage performance.

### Decision Summary

| Criterion | vSAN ESA | vSAN OSA | Fibre Channel | NFS v3 |
|---|---|---|---|---|
| Minimum hosts (management) | 3 | 3 | 2 | 2 |
| Hardware requirement | NVMe | SSD/HDD | FC HBA + SAN | NIC + NAS |
| Performance tier | Highest | Moderate | Array-dependent | Array-dependent |
| Data reduction | Best (4x) | Good | Array-dependent | Array-dependent |
| Lifecycle integration | Full | Full | Storage external | Storage external |
| Strategic direction | Primary | Maintenance | Supported | Supported |
| Network dependency | vSAN VLAN | vSAN VLAN | FC fabric | NFS VLAN |

For most new deployments, vSAN ESA is the recommended choice. It provides the highest performance, the best data reduction, and full lifecycle integration with VCF. External storage (FC or NFS) is the right choice when existing infrastructure investments justify it, when the two-host management minimum is needed, or when specific array features are required.

---

## Design Maximums and Scalability Limits

Every architecture must be designed within the supported limits of the platform. VMware publishes configuration maximums through the Configuration Maximums tool at configmax.broadcom.com, which provides the tested, recommended, and fully supported limits for every VMware product. The following are the key scalability boundaries that shape VCF 9 designs.

### VCF Instance Limits

- **Domains per Instance:** 25 total (1 Management Domain + up to 24 Workload Domains).
- **Concurrent cluster creation operations per Instance:** 10 (applicable per single domain or across multiple Workload Domains).
- **Hosts per cluster:** Up to 96 hosts per vSphere cluster (subject to storage type---vSAN clusters have their own maximums).
- **Clusters per domain:** Multiple clusters per domain are supported, bounded by the vCenter maximum of 15 clusters per vCenter instance for VCF-managed environments.

### vSAN Limits

- **Hosts per vSAN cluster:** Up to 96 hosts per vSAN ESA cluster; up to 64 hosts per vSAN OSA cluster.
- **vSAN stretched cluster:** Supported with ESA or OSA, with specific host count requirements per site.
- **vSAN file shares:** Up to 500 file shares per cluster, with up to 100 SMB shares.

### NSX Limits

- **NSX Manager cluster:** 1 or 3 nodes (Simple or HA).
- **Transport zones, logical switches, and security groups:** Consult configmax.broadcom.com for the specific release; NSX 9 supports up to 10,000 security groups and 10,000 IPSets in a 10-node Extra Large cluster deployment of VCF Operations for Networks.

### VCF Operations Sizing

VCF Operations sizing determines how many objects (hosts, VMs, clusters, datastores, switches) can be managed:

| Node Size | Maximum Objects |
|---|---|
| Extra Small | 700 |
| Small | 10,000 |
| Medium | 30,000 |
| Large | 44,000 |
| Extra Large | 100,000 |

### Fleet-Level Limits

- **Fleet-to-Instance RTT:** Maximum 300ms.
- **Intra-Instance RTT (Collector to management components):** Maximum 50ms.
- **Instances per Fleet:** Consult current Broadcom documentation for the supported maximum; architectural discussions frequently reference deployments with tens of Instances per Fleet.

### Using configmax.broadcom.com

The Configuration Maximums tool is interactive and version-specific. Always select the exact VCF release (9.0, 9.0.1, 9.0.2, etc.) when checking limits, as maximums can change between point releases. The tool covers vCenter, ESX, NSX, vSAN, and VCF-specific limits. Bookmark it. Consult it during every design review. Never rely on limits memorized from a previous release.

---

## VMware Validated Solutions (VVS)

VMware Validated Solutions are prescriptive, tested, and documented architectures that extend the core VCF platform to address specific use cases. They are not products to be purchased separately; they are architectural blueprints---detailed design guides, implementation procedures, and operational runbooks---that show you how to combine VCF with additional VMware capabilities to solve real-world problems.

### What VVS Provides

Each Validated Solution includes four layers of guidance:

1. **Design:** Detailed design decisions with rationale, covering component placement, sizing, networking, and integration points.
2. **Implementation:** Step-by-step procedures for deploying the solution on a VCF platform.
3. **Configuration:** Post-deployment configuration, including policies, alerts, dashboards, and integrations.
4. **Operations:** Ongoing operational procedures, including monitoring, troubleshooting, and lifecycle management.

The solutions are tested against specific VCF releases and are updated as new VCF versions ship. For VCF 9, the Validated Solutions have been updated to reflect the new Fleet/Instance hierarchy, the VCF Operations and VCF Automation components, and the architectural changes in vCenter 9, NSX 9, and vSAN 9.

### Available Validated Solutions for VCF 9

The following Validated Solutions are available for VCF 9, organized by category:

**Identity and Security**

- **Identity and Access Management for VMware Cloud Foundation.** Provides detailed design and implementation guidance for integrating Active Directory as an identity provider and authentication source across the VCF platform. This is a foundational solution that most production deployments should implement.
- **Lateral Security for VMware Cloud Foundation with VMware vDefend.** Addresses microsegmentation and east-west security using VMware's vDefend capabilities, enabling zero-trust networking within and across Workload Domains.

**Monitoring and Operations**

- **Health Reporting and Monitoring for VMware Cloud Foundation.** Provides guidance on monitoring the operational state of your VCF environment through custom dashboards, alerts, and notifications. This solution helps you build proactive monitoring that catches issues before they become outages.
- **Intelligent Operations Management for VMware Cloud Foundation.** Delivers a centralized monitoring and alerting platform for proactive management of system failures across the entire VCF estate.
- **Intelligent Logging and Analytics for VMware Cloud Foundation.** Provides scalable log management with dashboards for analyzing log data from all VCF components. Essential for troubleshooting, compliance, and security forensics.
- **Intelligent Network Visibility for VMware Cloud Foundation.** Addresses network analysis and optimization, helping you build and maintain a highly available and secure network infrastructure.

**Disaster Recovery and Resilience**

- **Site Protection and Disaster Recovery for VMware Cloud Foundation.** Provides guidance for configuring disaster recovery of SDDC management components and workloads using VMware Live Recovery across dual-Instance deployments. This solution covers the protection of VCF Automation, VCF Operations, and fleet management components.
- **On-Premises Ransomware Recovery for VMware Cloud Foundation.** Addresses the recovery of business-critical workloads within an Isolated Recovery Environment following a ransomware event. Given the current threat landscape, this solution has moved from "nice to have" to "essential" for most enterprises.

**Workloads and Developer Infrastructure**

- **Developer Ready Infrastructure for VMware Cloud Foundation.** Provides design, implementation, and operational guidance for a Workload Domain configured to run container workloads using vSphere with Tanzu (Supervisor clusters). This is the starting point for organizations adopting Kubernetes on VCF.
- **Private AI Ready Infrastructure for VMware Cloud Foundation.** Delivers guidance for building enterprise private AI GPU-enabled infrastructure, enabling data scientists and ML engineers to leverage GPU resources within VCF Workload Domains.

**Automation and Networking**

- **Private Cloud Automation for VMware Cloud Foundation.** Provides guidance on using VCF Automation for cloud provisioning and self-service, including template design, approval workflows, and governance policies.
- **Advanced Load Balancing for VMware Cloud Foundation.** Covers the deployment and configuration of NSX Advanced Load Balancer (formerly Avi Networks) as the load balancing solution for workloads running on VCF.

### How to Use VVS

The Validated Solutions are published on Broadcom's TechDocs portal at techdocs.broadcom.com and on the VMware Validated Solutions GitHub repository (github.com/vmware/validated-solutions-for-cloud-foundation). The GitHub repository includes automation scripts, PowerShell modules, and configuration templates that accelerate implementation.

The recommended approach is to:

1. **Start with the design guide** for each solution you plan to implement. Understand the architectural decisions and their rationale before touching any configuration.
2. **Map the design to your environment.** The Validated Solutions are written for a reference architecture; your environment will differ. Identify where you need to adapt the design (different VLAN IDs, different IP ranges, different naming conventions) while preserving the architectural intent.
3. **Follow the implementation guide sequentially.** The steps are ordered for a reason; skipping ahead or reordering steps frequently leads to configuration errors that are difficult to diagnose.
4. **Implement the operational procedures.** The value of a Validated Solution is not just in the initial deployment---it is in the ongoing operational practices that keep the solution healthy over time.

Validated Solutions are not optional extras. They represent VMware's accumulated expertise in deploying these capabilities on VCF at scale. Ignoring them in favor of ad hoc implementation is a false economy that typically results in longer deployment times, more support tickets, and architectures that are harder to upgrade.

---

## Bringing It All Together

The architecture of VCF 9 is designed around a simple but powerful principle: structure at every level. The Private Cloud/Fleet/Instance hierarchy provides organizational structure. The Management Domain/Workload Domain/Edge Cluster model provides operational structure. The component architecture---VCF Installer, VCF Operations, VCF Automation, vCenter, ESX, NSX, vSAN---provides functional structure. The deployment modes provide flexibility structure. The VLAN and VMkernel design provides network structure. The storage decision framework provides infrastructure structure. The configuration maximums provide boundary structure. And the Validated Solutions provide implementation structure.

As a VMware Technical Account Manager, I have seen architectures succeed when they respect these structures and fail when they ignore them. The organizations that invest time in understanding the hierarchy, designing their VLANs before opening the Installer, choosing their storage architecture based on workload requirements rather than habit, and implementing Validated Solutions rather than improvising---these are the organizations that run VCF successfully at scale for years.

The architecture described in this chapter is the foundation for everything that follows in this book. Subsequent chapters will build on these concepts as we explore networking in depth, storage design, lifecycle management, automation, and operational best practices. Return to this chapter when you need to ground a specific design decision in the broader architectural context.

VCF 9 is the most capable and the most opinionated private cloud platform VMware has ever shipped. Understanding its architecture is not optional---it is the prerequisite for every decision you will make.


---

# Chapter 4: Compute -- vSphere 9 as the Engine of VCF

Every VMware Cloud Foundation deployment begins and ends with compute. Networking can be elegant, storage can be fast, automation can be clever -- but none of it matters if the hypervisor cannot run the workloads your business demands. With VCF 9.0, the compute layer is vSphere 9, and it represents the most significant generational leap in VMware's hypervisor since the introduction of the Distributed Resource Scheduler over two decades ago. This chapter examines every dimension of that leap: the expanded virtual hardware envelope, the NVMe memory tiering system that rewrites capacity planning assumptions, the clustering and availability changes that finally retire a long-standing architectural crutch, the CPU compatibility decisions that will force uncomfortable conversations in brownfield environments, GPU acceleration for the AI era, Kubernetes-native workload management, and the performance benchmarks that put numbers behind the marketing.

If you have been running vSphere 7 or 8 in production, some of what follows will feel like natural evolution. Other parts will feel like a sharp turn. Both reactions are correct.

---

## 4.1 vSphere 9 in Context: The Compute Contract with VCF

VCF is an opinionated platform. It prescribes how networking, storage, and compute interlock, and it does so to deliver lifecycle consistency across domains. Within that contract, vSphere 9 is the compute substrate -- the layer responsible for CPU scheduling, memory management, device virtualization, VM isolation, and the cluster-level intelligence that keeps workloads running when hardware fails.

In VCF 9.0, vSphere 9 is not optional. You do not pick a hypervisor version at deployment time; SDDC Manager provisions ESXi 9.0 hosts and vCenter 9.0 instances as part of the bring-up workflow. This tight coupling is deliberate. It ensures that every feature discussed in this chapter -- from Virtual Hardware Version 22 to NVMe memory tiering -- is available uniformly across every workload domain in your environment.

For practitioners coming from standalone vSphere deployments, this is the key mental shift: in VCF, the hypervisor is a platform component, not a standalone product. You consume it through SDDC Manager, you patch it through vSphere Lifecycle Manager images, and you monitor it through VCF Operations. The days of SSH-ing into a host and running esxcli commands to apply a one-off patch are over -- or at least, they should be.

---

## 4.2 Virtual Hardware Version 22: The Monster VM Gets Bigger

### 4.2.1 The New Maximums

Every major vSphere release introduces a new virtual hardware version, and vSphere 9.0 brings Version 22. The headline numbers are impossible to ignore:

- **Maximum vCPUs per VM: 960** (up from 768 in Hardware Version 21)
- **Maximum RAM per VM: 24 TB** (up from 16 TB in Hardware Version 21)
- **NVMe 1.3c controller support** with enhanced command set
- **TPM 2.0 library specification Revision 1.59** for virtual Trusted Platform Modules
- **New ISA exposure** for Intel Granite Rapids and AMD Turin (5th-generation EPYC) processors

These are not theoretical numbers thrown onto a spec sheet to win a features comparison. They reflect the actual workload demands that VMware's engineering teams are seeing from customers running SAP HANA, Oracle Database, large-scale in-memory analytics, and increasingly, monolithic AI training jobs that refuse to be decomposed into smaller units.

### 4.2.2 When You Actually Need 960 vCPUs

Let me be direct: the vast majority of VCF deployments will never provision a VM with 960 vCPUs. The median enterprise VM still runs 2 to 4 vCPUs. But the customers who need this capability need it desperately.

Consider a SAP HANA scale-up deployment on a four-socket Intel Granite Rapids server with 480 physical cores (960 threads). In previous vSphere versions, you could not present the full server's compute capacity to a single VM without leaving headroom for the hypervisor itself. Hardware Version 22 removes that constraint. You can now create a VM that maps one-to-one with the physical topology of the largest servers on the market.

The 24 TB RAM ceiling follows the same logic. AMD Turin supports up to 24 TB of addressable memory per socket in certain configurations, and Intel's Granite Rapids Xeon processors push similar boundaries. Hardware Version 22 ensures the hypervisor does not become the bottleneck.

### 4.2.3 New Device Support and the NVMe 1.3c Controller

Hardware Version 22 introduces NVMe 1.3c controller emulation, which matters for two reasons. First, modern guest operating systems -- Windows Server 2025, RHEL 9.x, Ubuntu 24.04 LTS -- expect NVMe as the default storage interface. The paravirtual SCSI (PVSCSI) adapter that served VMware well for fifteen years is increasingly treated as a legacy device by OS vendors. Second, NVMe 1.3c brings command set improvements that enable better queue management and error handling inside the guest, which translates to measurable IOPS improvements on high-throughput storage workloads.

VMware Tools 13, which ships alongside vSphere 9, includes updated NVMe and network drivers optimized for Hardware Version 22. It also adds Direct3D 11.0 rendering support for virtual graphics and ensures compatibility with Windows Server 2025 and Windows 11 24H2 guests.

### 4.2.4 The Compatibility Cliff

Here is the part that matters most in brownfield environments: **Virtual Hardware Version 22 VMs can only run on ESXi 9.0 or later hosts.** There is no backward compatibility. If you upgrade a VM to Hardware Version 22 and then need to migrate it to a cluster still running ESXi 8.0, the migration will fail.

This creates a hard boundary in mixed-version environments. My recommendation is straightforward: do not upgrade VM hardware versions until every cluster that might host that VM is running ESXi 9.0. In a VCF context, this means completing the workload domain upgrade before touching individual VM configurations. SDDC Manager enforces this sequence for infrastructure VMs, but tenant VMs are your responsibility.

The EVC (Enhanced vMotion Compatibility) baseline for Hardware Version 22 requires the Granite Rapids or Turin CPU feature set at minimum. Plan your EVC mode settings carefully during the upgrade window.

---

## 4.3 NVMe Memory Tiering: Cheap RAM That Actually Works

### 4.3.1 The Problem Statement

Memory is expensive. It has always been expensive, and the per-gigabyte cost of DDR5 is not declining as fast as the memory appetite of modern workloads is growing. For years, VMware addressed this with memory overcommitment techniques -- transparent page sharing, ballooning, compression, and swap. These work, but they introduce performance cliffs. When a host starts swapping to disk, VM performance degrades catastrophically.

NVMe memory tiering, which reaches General Availability in vSphere 9.0, offers a fundamentally different approach. Instead of treating NVMe as a swap device of last resort, the hypervisor treats it as a second tier of memory -- slower than DRAM, but orders of magnitude faster than traditional swap to a SAN or even a local SSD used as a swap datastore.

### 4.3.2 How It Works: Hot Pages, Cold Pages, and Transparent Migration

The mechanism is elegant in concept and demanding in implementation. ESXi 9.0 maintains a two-tier memory hierarchy:

- **Tier 0 (DRAM):** The fast tier. This is your server's installed DDR5 memory.
- **Tier 1 (NVMe):** The capacity tier. This is a local NVMe device that ESXi claims exclusively for memory tiering -- it is not available as a datastore.

The hypervisor continuously monitors memory access patterns at the page level. Pages that are actively being read or written -- "hot" pages -- are kept in DRAM. Pages that have not been accessed recently -- "cold" pages -- are candidates for migration to the NVMe tier. When a VM accesses a cold page that resides on NVMe, the hypervisor transparently migrates it back to DRAM and demotes a different cold page to make room.

The critical design principle is that this is entirely transparent to the guest operating system. The VM sees a single, flat memory address space. It does not know that some of its pages are on NVMe. There is no guest-side driver, no application modification, and no memory-aware NUMA tuning required inside the VM. The hypervisor handles everything.

Page migration only occurs under memory pressure. If the host has sufficient free DRAM to accommodate all VM memory, the NVMe tier sits idle. The system avoids unnecessary page movement to minimize compute overhead -- moving pages consumes CPU cycles, and the tiering engine is designed to be conservative about spending them.

### 4.3.3 Capacity Planning: The DRAM-to-NVMe Ratio

The default ratio in VCF 9.0 is **1:1** -- for every terabyte of DRAM, you add one terabyte of NVMe tiering capacity. This effectively doubles your host's addressable memory. Broadcom's best-practice recommendation is to keep this default ratio for general-purpose workloads.

The ratio is configurable as an advanced host parameter, and you can push it as far as **1:4** -- meaning a host with 1 TB of DRAM could present up to 5 TB of total addressable memory (1 TB DRAM + 4 TB NVMe). However, I want to be clear about the tradeoff: as you increase the NVMe proportion, you increase the probability that hot pages will need to be fetched from the slower tier. At a 1:4 ratio, you are betting that no more than 20% of your aggregate VM memory is actively hot at any given moment. That is a safe bet for file servers and lightly used infrastructure VMs. It is a dangerous bet for database workloads.

**The golden rule for sizing:** the total active (hot) memory across all VMs on a host should not exceed 50% of the DRAM capacity. This gives the tiering engine enough headroom to absorb access pattern changes without triggering performance degradation.

Additional constraints to keep in mind:

- The NVMe tier partition cannot exceed **4 TB** per host.
- The NVMe device is claimed exclusively by the memory tiering subsystem. It cannot be shared with vSAN, VMFS, or any other storage function.
- Memory tiering is compatible with vMotion -- a VM can be migrated between tiering-enabled hosts, and the tiering state is rebuilt transparently on the destination.
- Memory tiering supports VM-level and host-level encryption.

### 4.3.4 Ideal Workloads and What It Is Not For

NVMe memory tiering shines in environments with large populations of VMs that have variable memory activity -- VDI desktops, web servers, development environments, file and print servers. These workloads tend to have a small hot working set relative to their total allocated memory.

It is **not** a good fit for latency-sensitive, memory-intensive workloads where the entire memory footprint is actively accessed: real-time trading systems, in-memory databases with full-table scans, and high-frequency analytics. For these workloads, every page fetch from NVMe introduces microseconds of latency that compound into measurable performance degradation.

### 4.3.5 Production Readiness: vSphere 9 vs. vSphere 8

Memory tiering was introduced as a tech preview in vSphere 8.0 Update 3 and required manual configuration with limited monitoring. In vSphere 9.0, it reaches full GA status with significant improvements:

- Integrated monitoring in vCenter with per-host and per-VM tiering metrics
- vMotion awareness -- tiering state is preserved across migrations
- Encryption support for tiered pages
- Improved resiliency against NVMe device failures (graceful degradation to DRAM-only mode)
- Support in VCF lifecycle workflows -- SDDC Manager can configure tiering as part of cluster provisioning

If you experimented with tiering in vSphere 8 and were not confident enough to put it into production, vSphere 9 is the release where it becomes real.

---

## 4.4 DRS and HA in the VCF Context

### 4.4.1 The Death of vCLS

The most architecturally significant change to vSphere clustering in version 9.0 is the deprecation of vSphere Cluster Services (vCLS). Since vSphere 7 Update 1, DRS and HA functionality depended on small agent VMs that ran on every cluster. These vCLS VMs consumed resources, occasionally failed to deploy, interfered with host maintenance modes, and generated support tickets far out of proportion to their size.

In vSphere 9.0, vCLS is deprecated. The functionality that these VMs provided -- primarily a distributed key-value store for cluster state -- has been replaced by an embedded key-value store built directly into the ESXi 9.0 host. Each ESXi host now maintains cluster configuration and state data natively, without requiring agent VMs.

The practical impact is immediate:

- **No more vCLS VM deployment failures** blocking DRS initialization on new clusters.
- **No more resource consumption** by agent VMs (typically 3 VMs per cluster, each consuming a small amount of CPU and memory, but multiplied across dozens of clusters, the overhead was non-trivial).
- **Simplified maintenance mode** workflows -- you no longer need to wait for vCLS VMs to evacuate or worry about their anti-affinity rules.
- **Recommended action:** switch existing vCLS to retreat mode after upgrading to vSphere 9.0 to reclaim resources immediately.

Disabling vCLS has no impact on DRS or HA functionality in vSphere 9.0. This is not a feature removal -- it is an architectural improvement that eliminates a well-known operational pain point.

### 4.4.2 DRS Enhancements for GPU Workloads

DRS in vSphere 9.0 introduces GPU-aware placement intelligence. When a VM requests a vGPU profile, DRS considers not only CPU and memory utilization but also GPU availability and profile compatibility when making initial placement and load-balancing decisions.

Administrators can now reserve GPU capacity for future use through DRS policies, which is critical for AI/ML environments where GPU resources are scarce and expensive. This prevents a situation where DRS migrates a non-GPU workload onto a GPU-equipped host, consuming CPU and memory resources that should be reserved for the next GPU VM.

### 4.4.3 HA Improvements

vSphere HA in VCF 9.0 benefits from the same distributed state architecture that replaced vCLS. Cluster health state is now maintained in the embedded key-value store, which improves HA response times and eliminates a class of split-brain scenarios that occurred when vCLS VMs became unavailable.

The deployment and configuration of HA has been streamlined, with fewer manual steps required when enabling HA on a new cluster provisioned through SDDC Manager. In practice, HA configuration is handled as part of the workload domain provisioning workflow -- you define your admission control policy in the cluster specification, and SDDC Manager applies it consistently.

---

## 4.5 ESXi 9 Image Management: ImageDB and the End of Baselines

### 4.5.1 The Mandatory Shift to Image-Based Lifecycle Management

If you are still managing ESXi hosts using baselines -- the legacy approach inherited from vSphere Update Manager -- vSphere 9.0 forces your hand. **Baselines are deprecated in vSphere 9.0.** Clusters and standalone hosts managed by a vCenter 9.0 instance must use vSphere Lifecycle Manager (vLCM) images.

This is not a soft deprecation with a multi-release grace period. Broadcom has signaled that baseline support will be removed in the next major release. If your operational processes depend on manually curated baselines and scheduled remediation windows, now is the time to migrate.

### 4.5.2 How Image-Based Management Works

A vLCM image is a desired-state specification that defines the complete software stack for every host in a cluster:

- **ESXi base version** (e.g., ESXi 9.0.0)
- **VMware add-on components** (drivers, agents)
- **Vendor-specific components** (OEM drivers, firmware bundles from Dell, HPE, Lenovo, etc.)
- **Third-party components** (monitoring agents, security modules)

When you define an image for a cluster, vLCM compares the desired state against the actual state of every host and reports compliance. Remediation applies the image atomically -- the host reboots into the exact software specification you defined.

### 4.5.3 ImageDB and Stateless Operations

ESXi 9.0 maintains an internal image database (ImageDB) that tracks the installed software state of the host. This database is the ground truth for compliance checks -- when vLCM queries a host, it reads from ImageDB rather than scanning the filesystem.

For stateless deployments using Auto Deploy, vLCM images can be assigned to clusters with stateless hosts. When a stateless host boots, it pulls its image from the Auto Deploy server and populates ImageDB from the image specification. If the Auto Deploy server is unavailable, the host can boot from a cached image stored locally (stateless caching), and ImageDB is populated from the cache.

The practical benefit is cluster-wide host homogeneity. Every host in a cluster runs the exact same software stack, which eliminates the drift that inevitably occurs when hosts are patched individually over time. In a VCF environment, this homogeneity is not just a best practice -- it is a requirement. SDDC Manager enforces image compliance as part of its health checks and will flag non-compliant hosts during lifecycle operations.

---

## 4.6 CPU and Hardware Requirements: Who Gets Left Behind

### 4.6.1 Supported Processor Families

vSphere 9.0 supports the following processor families (this is not exhaustive, but covers the most common deployment scenarios):

**Intel:**
- Xeon Scalable 4th Generation (Sapphire Rapids) and later
- Xeon Scalable 5th Generation (Emerald Rapids)
- Xeon 6 (Granite Rapids)

**AMD:**
- EPYC 4th Generation (Genoa) and later
- EPYC 5th Generation (Turin)

### 4.6.2 Dropped Processor Families

The following processor families are **discontinued** in vSphere 9.0 and cannot be used:

**Intel (notable removals):**
- Xeon Scalable 1st Generation (Skylake-SP) -- Platinum, Gold, Silver, Bronze
- Xeon Scalable 2nd Generation (Cascade Lake-SP)
- Xeon D-1500 series
- Xeon E5 v4 (Broadwell) and earlier

**AMD (notable removals):**
- EPYC 1st Generation (Naples)
- EPYC 2nd Generation (Rome)

### 4.6.3 The Reasoning and the Brownfield Problem

Broadcom's rationale for aggressive CPU deprecation is twofold. First, older processors lack hardware security features (SGX improvements, SEV-SNP, TDX) that vSphere 9.0's confidential computing capabilities depend on. Second, maintaining driver and microcode support for aging silicon consumes engineering resources that Broadcom prefers to redirect toward current-generation platforms.

The reasoning is sound from an engineering perspective. The problem is operational. Many enterprise data centers refresh server hardware on five-to-seven-year cycles. A customer who purchased Cascade Lake servers in 2020 expected to run them until 2025 or 2026. Those servers cannot run ESXi 9.0.

If you are in this situation, your options are:

1. **Remain on vSphere 8.x** for unsupported hardware and run a mixed-version environment. This is operationally complex but technically feasible. Note that VCF 9.0 workload domains require ESXi 9.0 -- you cannot mix ESXi versions within a single workload domain.
2. **Accelerate hardware refresh** to bring all hosts onto supported silicon. This is the clean path but requires capital expenditure.
3. **Use the unsupported CPU override** during ESXi 9.0 installation (allowLegacyCPU boot option). This bypasses the CPU check but puts you in an unsupported configuration. I do not recommend this for production.

There is no comfortable answer here. Plan your hardware refresh timeline against your VCF upgrade timeline, and have the conversation with procurement early.

---

## 4.7 GPU Support for AI and VDI

### 4.7.1 The vGPU Architecture in vSphere 9

vSphere 9.0 supports GPU virtualization through three models:

- **GPU Passthrough (DirectPath I/O):** A physical GPU is exclusively assigned to a single VM. Maximum performance, zero sharing. This is the preferred model for AI training workloads.
- **NVIDIA vGPU:** A single physical GPU is partitioned into multiple virtual GPUs using NVIDIA's vGPU software. Each VM gets a vGPU profile that defines its share of GPU compute and memory. This is the standard model for VDI and inference workloads.
- **vSGA (Virtual Shared Graphics Acceleration):** A software-based shared graphics model. Lower performance, broader compatibility. Suitable for task workers who need basic 3D rendering.

### 4.7.2 What Changed in vSphere 9

Three improvements stand out for GPU-intensive environments:

**Fast Suspend/Resume (FSR) for vGPU VMs:** In previous versions, suspending a vGPU-enabled VM to apply a host patch required transferring the entire GPU frame buffer to disk. For a VM with two NVIDIA L40 GPUs (48 GB each), this took approximately 42 seconds. In vSphere 9.0, FSR reduces this to approximately 2 seconds. This transforms live patching operations for GPU clusters from a multi-hour maintenance window into a routine, minimally disruptive activity.

**6x Faster vMotion for GPU VMs:** The vGPU data channel has been re-architected with multiple parallel TCP connections and a zero-copy data path. Bandwidth peaks at 69 Gbps on 100G-equipped servers, compared to approximately 10 Gbps in vSphere 8.x. The stun time during vMotion -- the brief period when the VM is paused -- drops to roughly 1 second for a 24 GB GPU workload and approximately 2 seconds for a 48 GB workload.

**vGPU Profile Utilization Dashboard:** vCenter 9.0 introduces a consolidated view of all vGPU profiles across your GPU inventory. This sounds mundane, but in environments with hundreds of GPUs spread across multiple clusters, knowing which profiles are allocated, which are idle, and which hosts have capacity for a specific profile type is operationally critical. Previously, this required CLI queries or third-party tooling.

### 4.7.3 VMware Private AI Foundation with NVIDIA

VCF 9.0 integrates with VMware Private AI Foundation with NVIDIA (NVIDIA AI Enterprise 9.0), which provides a validated, supported stack for running AI workloads on virtualized infrastructure. The stack includes:

- NVIDIA GPU Operator for automated GPU driver lifecycle management in Kubernetes
- Pre-validated vGPU profiles for common AI frameworks (PyTorch, TensorFlow, vLLM)
- Integration with VKS for GPU-accelerated Kubernetes pods
- Support for NVIDIA H200 (virtualized) and Blackwell (passthrough/DirectPath I/O) GPUs

This is the path Broadcom is paving for enterprises that want to run AI workloads on their existing VCF infrastructure rather than building separate bare-metal GPU clusters.

---

## 4.8 vSphere Kubernetes Service (VKS)

### 4.8.1 What VKS Is and How It Works

vSphere Kubernetes Service (VKS) -- the successor to Tanzu Kubernetes Grid Service -- is VCF's native mechanism for provisioning and managing Kubernetes clusters. VKS runs on the vSphere Supervisor, which is a Kubernetes control plane embedded directly into vCenter and backed by ESXi hosts as worker nodes.

When a developer or platform team requests a Kubernetes cluster through VKS, the following occurs:

1. The Supervisor provisions a set of VMs on the target vSphere namespace (a resource-bounded partition of a cluster).
2. These VMs are bootstrapped as Kubernetes control plane and worker nodes using the Cluster API (CAPI) framework.
3. The resulting Kubernetes cluster is a conformant, upstream-compatible cluster that supports standard kubectl operations, Helm charts, and any CNCF-certified tooling.

VKS clusters are lifecycle-managed by the Supervisor. Kubernetes version upgrades, node scaling, and certificate rotation are handled through declarative specifications -- you update the desired state, and the Supervisor converges the cluster to match.

### 4.8.2 Persistent Volumes on vSAN

Kubernetes workloads that require persistent storage consume it through the vSphere CSI (Container Storage Interface) driver. In a VCF environment, the backing storage is vSAN.

VCF 9.0 introduces the **Volume Service**, which allows tenants to provision persistent volumes through VCF Automation. Two volume types are supported:

- **ReadWriteOnce (RWO):** Backed by vSAN block storage. A volume is attached to a single pod at a time. This is the standard model for databases and stateful applications.
- **ReadWriteMany (RWX):** Backed by vSAN File Shares. Multiple pods can mount the volume simultaneously. This is used for shared data scenarios -- machine learning training data, content management systems, shared configuration stores.

RWX volumes require vSAN File Services to be enabled on the Supervisor cluster. VCF 9.0 increases the per-cluster file share limit from 250 to 500, which addresses a scaling constraint that affected large multi-tenant deployments in VCF 5.x.

Storage policies are enforced at the tenant level. A platform administrator defines which vSAN storage policies are available to each tenant namespace, and quota enforcement prevents any single tenant from consuming more than their allocated share. This is the storage governance model that was missing from early Tanzu implementations, and it is now a first-class feature.

### 4.8.3 VKS and VCF Automation (VCFA) Integration

VCF Automation (VCFA) is the self-service portal layer of VCF 9.0. It allows platform teams to expose curated services -- VMs, Kubernetes clusters, databases, networking, container registries -- to application teams through a catalog-based interface.

As of VCF 9.0.1, VCFA includes native VKS Cluster Management (VKSM), which enables full lifecycle management of VKS clusters from the VCFA console: provisioning, scaling, upgrading, backup, and restore. This eliminates the need for application teams to interact with vCenter directly -- they request a Kubernetes cluster through VCFA, receive credentials, and deploy their workloads.

### 4.8.4 Argo CD: GitOps Comes to VCF

VCF 9.0 introduces a Broadcom-built Argo CD Operator as a Supervisor Service. Argo CD is the de facto standard for GitOps-based continuous delivery in the Kubernetes ecosystem, and its integration into VCF is significant.

Each Argo CD instance is scoped to a vSphere Namespace, which aligns with the Supervisor's multi-tenancy model. From a single Git repository, teams can declaratively manage:

- VKS cluster specifications (create, update, configure)
- Application deployments into VKS clusters
- VM workloads (declarative VM management via Kubernetes-style manifests)
- vSphere Pod configurations

The workflow is straightforward: a developer commits a change to a Git repository, Argo CD detects the change, and it automatically applies the desired state to the target environment. This is the same GitOps pattern used by hyperscaler-native Kubernetes platforms, and bringing it to VCF closes a significant gap for organizations that want to standardize on Git-driven infrastructure management.

For edge deployments, this is particularly powerful. VCF 9.0 supports single-node vSphere Supervisor, and Argo CD can manage application deployment across hundreds of edge locations from a central Git repository -- a pattern that eliminates the need for site-by-site manual configuration.

---

## 4.9 Performance Benchmarks: What the Numbers Actually Tell Us

### 4.9.1 MLPerf Inference v5.1: VCF Matches Bare Metal

In late 2025, Broadcom submitted VCF 9.0 results to the MLPerf Inference v5.1 benchmark suite, the industry-standard benchmark for AI inference performance administered by MLCommons. The results were submitted in collaboration with Dell, Intel, NVIDIA, and Supermicro.

The headline result: **VCF 9.0 achieved performance on par with bare-metal configurations** across six benchmark categories:

- Speech-to-Text (Whisper)
- Text-to-Video (Stable Diffusion XL)
- Large Language Models (Llama 3.1-405B and Llama 2-70B)
- Graph Neural Networks (R-GAT)
- Computer Vision (RetinaNet)

Hardware configurations included NVIDIA H200 GPUs in virtualized (vGPU) mode, Blackwell GPUs in passthrough mode, and Intel Xeon 6787P dual-socket CPUs for CPU-only inference.

### 4.9.2 What the Results Mean -- and What They Do Not

The MLPerf results demonstrate that the vSphere 9 hypervisor introduces negligible overhead for GPU-accelerated inference workloads. When GPUs are doing the heavy lifting, the CPU and memory consumed by the VM represent a small fraction of the host's total resources, leaving substantial capacity for additional non-GPU workloads. This is the core value proposition of virtualized AI: you do not dedicate an entire server to a single inference workload; you share the infrastructure.

However, I want to offer a few honest caveats:

**These are Broadcom-sourced benchmarks.** The submissions were made by Broadcom and its partners. While MLPerf is an independent framework with standardized rules, the hardware configurations and tuning parameters were chosen to showcase VCF's strengths. Real-world performance depends on your specific workload characteristics, network topology, storage configuration, and VM density.

**Inference is not training.** MLPerf Inference measures how fast a system can run a trained model against new data. AI training workloads -- which involve massive data movement, gradient synchronization across multiple GPUs, and sustained memory bandwidth demands -- are a different beast. Broadcom has not published comparable training benchmarks on VCF, and the overhead of virtualization is likely higher for multi-GPU training jobs that require RDMA and NVLink communication.

**Passthrough vs. vGPU matters.** The Blackwell GPU results used passthrough (DirectPath I/O), which bypasses the hypervisor's device emulation layer entirely. Passthrough delivers bare-metal GPU performance by definition. The H200 results used vGPU, which does incur a small hypervisor overhead, but for inference workloads, this overhead falls within the noise floor of measurement.

Take the MLPerf results as evidence that VCF is a viable platform for AI inference. Do not take them as a guarantee that your specific training pipeline will run at bare-metal speeds.

### 4.9.3 NSX Enhanced Data Path (EDP): The Networking Tax Gets Smaller

One of the persistent complaints about VCF has been the CPU overhead of NSX. The previous-generation NSX data path consumed host CPU cycles for every packet processed, and in high-throughput environments, that overhead was measurable -- sometimes painfully so.

VCF 9.0 makes NSX Enhanced Data Path (EDP) Standard the **default** host switch mode for all new workload domains and fresh NSX installations. EDP Standard delivers up to **3x higher switching performance** compared to the legacy NSX data path, with significantly lower CPU utilization per packet.

The technical improvements behind this number include:

- **EDP Flow Cache:** The first packet in a flow is processed through the full NSX pipeline (firewall rules, routing decisions, encapsulation). Subsequent packets in the same flow hit a cached action, bypassing the full pipeline. This is a run-to-completion optimization that eliminates redundant processing.
- **EDP Mbuf Framework:** The legacy PktHandle packet structure (which consumed significant cache space) is replaced by a 128-byte mbuf format that reduces cache footprint by approximately 50%. Fewer cache misses translate directly to higher packet throughput.
- **Optimized DPDK-style polling:** EDP uses a polling-based packet processing model rather than interrupt-driven processing, which reduces context switching overhead at high packet rates.

For compute workloads, the EDP improvement means that less host CPU is consumed by networking, leaving more capacity for VMs. In environments running 25 GbE or higher network interfaces with significant east-west traffic (which is most VCF deployments), the CPU savings from EDP can be equivalent to recovering one or two cores per host -- capacity that is now available for workload VMs.

EDP Standard is recommended for all VCF deployments, including general compute, network-intensive, and edge workloads. If you are upgrading from an older VCF version, enabling EDP on existing clusters requires a rolling reconfiguration of the host switch mode, which can be performed without VM downtime.

---

## 4.10 Putting It Together: Compute Capacity Planning in VCF 9.0

The compute layer in VCF 9.0 gives you more tools than any previous release, but it also demands more careful planning. Here is a summary of the key capacity planning decisions:

1. **CPU Selection:** Verify that your target hardware is on the vSphere 9.0 HCL. If you are running Cascade Lake or older, you cannot upgrade to VCF 9.0 without a hardware refresh.

2. **Memory Architecture:** Decide whether NVMe memory tiering is appropriate for your workloads. If yes, size the NVMe tier at a 1:1 ratio with DRAM for general workloads, and ensure that aggregate active memory does not exceed 50% of DRAM capacity.

3. **GPU Inventory:** Map your AI and VDI workloads to GPU profiles. Use passthrough for training, vGPU for inference and VDI. Factor in the FSR and vMotion improvements when calculating maintenance window durations.

4. **VM Hardware Version Strategy:** Do not upgrade VMs to Hardware Version 22 until all potential target clusters are running ESXi 9.0. Document your EVC baseline and communicate the compatibility cliff to application teams.

5. **Cluster Services:** After upgrading to vSphere 9.0, switch vCLS to retreat mode and reclaim the resources consumed by agent VMs.

6. **Image Management:** Migrate from baselines to vLCM images before the next major release removes baseline support entirely. This is a project, not a toggle -- allocate time for testing vendor-specific components.

7. **Kubernetes Readiness:** If you plan to offer Kubernetes-as-a-service through VCFA, size your Supervisor cluster with sufficient resources for VKS control plane VMs and the Argo CD operator. Budget vSAN capacity for persistent volumes, especially RWX volumes backed by File Shares.

8. **Network Overhead:** Enable EDP Standard on all workload domains to minimize the CPU tax of NSX. Measure before and after -- the improvement is real, and it directly increases your usable compute capacity.

---

## Summary

vSphere 9 is not a minor version bump. It redefines the compute contract in VCF: bigger VMs for scale-up workloads, NVMe memory tiering for cost-effective capacity expansion, a simplified clustering architecture without vCLS, GPU acceleration that finally makes live migration practical for AI workloads, and a Kubernetes platform that integrates GitOps natively. The CPU compatibility restrictions will cause pain in some environments, and the mandatory shift to image-based lifecycle management will force operational changes. Both are necessary trade-offs for a platform that is positioning itself as the enterprise standard for private cloud compute -- from traditional three-tier applications to large language model inference.

The compute layer is the foundation. In Chapter 5, we turn to the storage layer -- vSAN 9 -- and examine how it complements the compute capabilities discussed here.


---

# Chapter 5 --- Storage (vSAN 9)

## The Storage Challenge in 2025

Every enterprise infrastructure team I work with is fighting the same war on multiple fronts. Data volumes are compounding at forty percent or more per year. Ransomware has moved from headline risk to operational certainty---sixty-seven percent of organizations surveyed in 2024 reported at least one successful encryption event. Regulatory frameworks like DORA, NIS2, and the SEC cyber-disclosure rules now demand provable immutability and auditable recovery points. And then there is AI: the moment a line-of-business leader decides to fine-tune a large language model on proprietary data, the storage conversation changes completely, because training pipelines consume bandwidth and IOPS at a scale that traditional SAN architectures were never designed to deliver.

Against this backdrop, the storage layer of VMware Cloud Foundation 9 has to accomplish something genuinely difficult. It must be dense enough to keep costs competitive with public-cloud block storage, fast enough for latency-sensitive inference workloads, resilient enough to survive both hardware failures and deliberate cyberattacks, and simple enough that a two-person infrastructure team can operate it without a dedicated storage administrator. vSAN 9, anchored by the Express Storage Architecture and a suite of new data-services capabilities, is VMware's answer to all four demands simultaneously.

This chapter walks through the full storage story: the object model that underpins every policy decision, the architectural split between ESA and the Original Storage Architecture, the headline features---global deduplication, native replication with immutable snapshots, expanded principal storage---and the operational tooling that keeps it all visible. If you are planning a greenfield VCF 9 deployment or migrating a brownfield vSAN 7 or 8 environment, this is the chapter you will return to most often.

---

## vSAN Fundamentals: The Object Model and Storage Policies

Before we examine what is new in vSAN 9, we need a shared vocabulary. vSAN is not a traditional file system layered on top of a RAID controller. It is an object store distributed across every host in the cluster, governed by policies that the hypervisor enforces automatically. Understanding the object model is essential for making correct sizing and availability decisions.

### Objects, Components, and Witnesses

When a virtual machine is provisioned on a vSAN datastore, each of its virtual devices---VMDK files, VM home namespace, swap file---becomes a discrete *object*. vSAN then decomposes each object into one or more *components* and distributes those components across hosts in the cluster according to the storage policy attached to the VM. If a mirroring policy is in effect, vSAN creates full copies of each component on separate hosts. If erasure coding is in effect, it stripes data and parity fragments across multiple hosts. In either case, a lightweight *witness* component is placed on a third host to break ties during a network partition.

This object-level granularity is what separates vSAN from array-based storage. A traditional SAN protects an entire LUN with a single RAID level; every VM on that LUN inherits the same protection whether it needs it or not. vSAN protects each VM---indeed, each virtual disk---independently. A mission-critical database can run RAID-1 with FTT=2 (triple mirror, tolerating two simultaneous failures) while a disposable build server on the same cluster uses RAID-5 with FTT=1, consuming a fraction of the capacity. The policies travel with the VM, surviving vMotion, storage vMotion, and even cross-cluster migration.

### Storage Policy Based Management (SPBM)

Storage Policy Based Management is the control plane that translates human intent into vSAN object placement. An administrator defines a policy---specifying parameters like failures to tolerate, failure tolerance method, stripe width, IOPS limit, and whether compression and deduplication are enabled---and attaches it to a VM or a class of VMs. vSAN's distributed runtime then continuously reconciles the actual placement of components with the desired state expressed in the policy. If a host fails and a component becomes non-compliant, vSAN automatically rebuilds it on a surviving host, provided sufficient capacity and fault domains exist.

SPBM is not unique to vSAN; it is a vSphere-wide framework that also governs Virtual Volumes and I/O filters. But vSAN is where SPBM reaches its fullest expression, because every data-placement and data-services decision---protection level, compression, deduplication, encryption, IOPS reservation---is driven by policy rather than by manual LUN configuration.

### Failures to Tolerate (FTT) and Failure Tolerance Method

The two most consequential policy parameters are *Failures to Tolerate* (FTT) and *Failure Tolerance Method* (FTM). Together, they determine how much redundancy vSAN builds into each object and how much raw capacity that redundancy consumes.

**RAID-1 (Mirroring).** When FTM is set to mirroring, vSAN creates FTT + 1 full copies of every component. FTT=1 means two copies across two fault domains; FTT=2 means three copies across five fault domains; FTT=3 means four copies across seven fault domains. Mirroring is write-efficient because each write operation touches only the mirror copies---there is no parity calculation. It is the right choice for write-intensive transactional workloads where every microsecond of write latency matters. The cost is capacity: FTT=1 with RAID-1 consumes 2x the logical data size.

**RAID-5/6 (Erasure Coding).** When FTM is set to erasure coding, vSAN stripes data and parity across hosts. RAID-5 (FTT=1) distributes data plus one parity fragment; RAID-6 (FTT=2) distributes data plus two parity fragments. The capacity overhead is dramatically lower: RAID-5 consumes roughly 1.33x the logical data size, while RAID-6 consumes 1.5x. The trade-off in the Original Storage Architecture was performance---every write required additional read-modify-write cycles to compute parity, adding latency. As we will see, the Express Storage Architecture eliminates this penalty entirely.

The minimum host counts deserve memorization:

| Protection Level | Minimum Hosts (OSA) | Minimum Hosts (ESA) | Capacity Overhead |
|---|---|---|---|
| RAID-1, FTT=1 | 3 | 3 | 2x |
| RAID-5, FTT=1 | 4 (4+1) | 3 (2+1 adaptive) | 1.33x (4+1) or 1.5x (2+1) |
| RAID-1, FTT=2 | 5 | 5 | 3x |
| RAID-6, FTT=2 | 6 | 6 | 1.5x |
| RAID-1, FTT=3 | 7 | 7 | 4x |

Note the critical difference for ESA: the adaptive RAID-5 scheme introduced in vSAN 8 ESA allows erasure coding on clusters as small as three hosts, using a 2+1 layout. When the cluster grows to six or more hosts, ESA automatically transitions to a more efficient 4+1 layout. This adaptive behavior means that small clusters no longer pay the full mirroring tax just because they lack enough hosts for traditional erasure coding.

---

## ESA vs. OSA: A Complete Comparison

The Express Storage Architecture, introduced in vSAN 8 and reaching maturity in vSAN 9, is not merely a performance optimization. It is a ground-up redesign of how vSAN ingests, protects, compresses, and places data. The Original Storage Architecture remains supported for existing deployments, but ESA is the strategic direction, and every new feature in vSAN 9---global deduplication, native replication, enhanced data path---requires ESA.

### Hardware Model

**OSA** organizes storage into *disk groups*. Each disk group contains exactly one high-performance caching device (typically an NVMe or SAS SSD) and one or more capacity devices (which can be SSDs or even spinning disks in all-flash or hybrid configurations). All write I/O flows through the cache device before being de-staged to capacity devices. This architecture was elegant for its era, but the cache device becomes a single point of failure for the entire disk group: if it fails, every capacity device in that group goes offline, triggering a large-scale rebuild.

**ESA** eliminates disk groups entirely. Every NVMe device in the host participates in a single *storage pool*. There is no dedicated cache tier; instead, ESA's log-structured file system (vSAN LFS) buffers incoming writes in a highly efficient log and prepares them for full-stripe writes directly to the NVMe devices. The hardware requirement is straightforward: all storage devices must be NVMe-based TLC (or better) flash, and the server must be on the vSAN ESA ReadyNode list. There are no spinning disks, no SAS SSDs, and no separate cache/capacity distinction.

### Performance Characteristics

The elimination of the cache bottleneck, combined with the log-structured write path, gives ESA a fundamentally different I/O profile. Random write latency drops significantly because writes are absorbed into the log and coalesced into sequential full-stripe writes rather than being funneled through a single cache device. Random read performance improves because any NVMe device in the pool can serve data directly, distributing read load across all devices rather than concentrating it on whichever capacity device holds the requested block.

The most striking performance claim is around erasure coding. In OSA, RAID-5/6 carried a well-documented write penalty: each write required reading existing data and parity, computing new parity, and writing both. This made RAID-5/6 measurably slower than RAID-1 for write-heavy workloads, and many administrators defaulted to mirroring despite the capacity cost. In ESA, the log-structured write path enables full-stripe writes that bypass the read-modify-write cycle entirely. VMware's guidance for ESA clusters is unambiguous: use RAID-5/6 erasure coding for all cluster types and sizes that support it, because there is no longer a performance penalty.

In vSAN 9, the Enhanced Data Path pushes performance further. Through kernel-level optimizations and optional offload to Data Processing Units (DPUs), vSAN 9 ESA can deliver up to three times faster data transfer speeds compared to the previous generation, with lower latency and reduced CPU overhead. DPUs can offload compression, encryption, and deduplication processing, freeing host CPUs for application workloads---a critical advantage when those CPUs are sharing time with GPU-accelerated AI inference.

### Compression and Deduplication Behavior

**OSA** performs compression and deduplication together as a post-process operation at the disk-group level. When enabled, data is first written to the cache tier, then de-staged to capacity devices where it is compressed and deduplicated within the boundary of a single disk group. The deduplication domain is therefore limited to the data on one disk group on one host---a relatively small population of blocks, which limits deduplication ratios for most workloads. Compression operates at a 4 KB block granularity and achieves roughly 2:1 ratios on typical mixed workloads.

**ESA** transforms both capabilities. Compression is applied inline---before data is persisted---and operates on each 4 KB block with an improved algorithm that can achieve up to 4x better compression ratios than OSA. Because there is no cache tier bottleneck, inline compression does not create a performance chokepoint.

Deduplication in ESA, as of vSAN 9, operates at the *cluster-wide* level, which we cover in depth in the next section.

### Failure Domain Impact

In OSA, losing a cache device means losing the entire disk group. If a host has two disk groups, each with five capacity devices, a single cache-device failure takes five capacity devices offline and forces vSAN to rebuild all data that was stored on those devices. The blast radius is large.

In ESA, there is no cache device to lose. A single NVMe device failure affects only the data stored on that specific device. The blast radius is dramatically smaller, which means faster rebuilds, lower rebalancing I/O, and less risk of a second failure during the rebuild window.

### Ideal Workloads and Cost Considerations

**OSA** remains appropriate for existing deployments that cannot justify a hardware refresh, environments that still use hybrid (SSD + HDD) configurations, and edge sites where NVMe ReadyNode hardware is not yet available.

**ESA** is the right choice for all new deployments, particularly those requiring high IOPS (databases, VDI, AI/ML pipelines), maximum space efficiency (erasure coding without performance penalty, cluster-wide deduplication), and resilience against ransomware (native replication with immutable snapshots, which requires ESA).

From a cost perspective, ESA hardware---all-NVMe ReadyNodes---carries a higher per-host acquisition price than a comparable OSA configuration with SAS SSDs. However, the total cost of ownership frequently favors ESA because: (1) erasure coding at RAID-1 performance reduces raw capacity requirements by 33--50 percent; (2) cluster-wide deduplication and improved compression deliver additional capacity savings; and (3) the simplified operational model---no disk groups to manage, smaller failure domains, fewer rebuilds---reduces ongoing labor costs.

### Migration Path

VMware does not support in-place conversion from OSA to ESA. The migration path is to add new ESA-capable hosts to the cluster (or build a new ESA cluster), migrate workloads using vMotion or storage vMotion, and then decommission the OSA hosts. In a VCF environment managed by SDDC Manager, this process is orchestrated through workload-domain lifecycle operations, allowing a phased transition with no application downtime.

---

## Global Cluster-Wide Deduplication: The Full Story

If there is a single feature in vSAN 9 that justifies the upgrade conversation, it is global cluster-wide deduplication. Previous versions of vSAN ESA performed deduplication within a single host. vSAN 9 expands the deduplication domain to the *entire cluster*, which fundamentally changes the economics of storage-dense workloads.

### What Changed

In vSAN 8 ESA, deduplication operated on a per-host basis. Each host independently identified duplicate 4 KB blocks within its own storage pool and eliminated redundant copies. This was effective for workloads where duplicate data happened to land on the same host, but it missed duplicates spread across multiple hosts---which is the norm in large-scale VDI, dev/test, and template-heavy environments.

vSAN 9 ESA makes the *cluster* the deduplication domain. Every 4 KB block written anywhere in the cluster is hashed and compared against a cluster-wide metadata index. When a duplicate is detected, the redundant copy is eliminated and replaced with a pointer to the single remaining instance. As hosts are added to the cluster, the deduplication domain grows automatically, increasing the probability of finding duplicates and improving the overall deduplication ratio over time.

### Technical Mechanism

The implementation is built on two new object types that are instantiated when global deduplication is enabled:

1. **Deduplication metadata objects.** These maintain a hash entry for every 4 KB chunk across the cluster. The metadata index is the lookup table that determines whether a newly written block is a duplicate of an existing block. The metadata objects are distributed across hosts for both performance and resilience.

2. **Deduplication data objects.** When a block is identified as a duplicate, the canonical copy of the data is moved into a deduplication data object. These objects are *sharded* across hosts in a way that preserves spatial locality---contiguous blocks from the same source are kept together in the same shard wherever possible. This design minimizes read fragmentation: when a VM performs a large sequential read, the storage layer can satisfy it from a single shard rather than gathering fragments from many hosts.

All deduplication activity occurs *after* data has been persisted to storage (post-process), which minimizes the impact on guest VM write latency. The process is intelligent about data temperature: vSAN's metadata mapping distinguishes recently written (hot) data from cold data and always prioritizes deduplication of cold data first. This prevents the engine from wasting cycles deduplicating blocks that are likely to be overwritten again shortly.

### Space Savings Math: A VDI Example

Consider a 500-desktop VDI deployment on a ten-host ESA cluster. Each desktop has a 60 GB virtual disk based on a common Windows 11 gold image, plus 20 GB of user-profile data. The gold image is effectively identical across all 500 desktops---roughly 55 GB of the 60 GB VMDK is shared content.

Without deduplication, the raw storage consumed by desktop VMDKs alone is 500 x 60 GB = 30 TB. With cluster-wide deduplication, the 55 GB of shared gold-image content is stored once, plus 500 copies of the roughly 5 GB of per-desktop deltas: 55 GB + (500 x 5 GB) = 2.555 TB. That is a deduplication ratio of approximately 11.7:1 on the VMDK data alone. Add the 10 TB of user-profile data (which typically deduplicates at 1.5--2:1 because of common application caches, temp files, and Office templates), and the blended ratio across the environment lands in the 4--6:1 range.

Broadcom's published guidance states that cluster-wide deduplication can reduce storage footprint by up to 8x, depending on data characteristics. In my field experience, VDI and dev/test workloads routinely achieve 4--8x, while more heterogeneous production environments see 1.5--3x. The key insight is that per-host deduplication might achieve 1.5--2x on the same VDI workload because the gold-image copies are distributed across ten hosts, each host holding only fifty instances. Cluster-wide deduplication sees all five hundred instances at once and collapses them to a single canonical copy.

### Inline vs. Post-Process

A common question is why vSAN 9 uses post-process deduplication rather than inline. The answer is deliberate: inline deduplication requires a hash lookup on every write in the I/O hot path, which adds latency to every guest write operation. Post-process deduplication absorbs writes at full speed, persists them, and then scans for duplicates asynchronously during periods of lower I/O activity. The trade-off is that the cluster temporarily consumes more capacity before deduplication reclaims space, but the performance impact on production workloads is negligible.

This is a different design decision from most traditional storage arrays, which favor inline deduplication. The rationale is that array controllers have dedicated hardware ASIC pipelines for hash computation, so the latency cost of inline dedup is minimal. vSAN runs in software on general-purpose CPUs (or DPUs), where the hash computation competes with application workloads for cycles. Post-process is the correct engineering choice for a software-defined architecture.

### Release Status and Requirements

Global cluster-wide deduplication shipped in vSAN 9.0 as a *limited-availability* feature, meaning it requires a Request for Product Qualification (RPQ) through your VMware account team. The limited-availability designation reflects the fact that the initial release does not support all topologies---stretched clustering, for example, is excluded. Check with your TAM or account team for the latest supportability matrix before enabling in production. In vSAN 9.0.1, the feature moved closer to general availability with expanded topology support.

---

## Native vSAN-to-vSAN Replication

### How It Works

vSAN 9 introduces native asynchronous replication between vSAN ESA clusters, built on vSAN Data Protection's snapshot technology. Unlike traditional array-based replication, which operates at the LUN or volume level, vSAN replication operates at the VM level. An administrator creates a *protection group*, assigns VMs to it, specifies an RPO (as low as one minute), and designates a target vSAN ESA cluster. vSAN then captures crash-consistent snapshots of the protected VMs at the specified interval and replicates the changed blocks to the target cluster.

The replication leverages vSAN ESA's native snapshot engine, which produces *deep snapshots*---snapshots that are first-class objects in the vSAN object model rather than redo logs chained to a base disk. This is a critical distinction from traditional vSphere snapshots, which degrade performance as the chain length grows. vSAN deep snapshots maintain consistent read and write performance regardless of how many snapshots exist, because they are implemented as metadata pointers into the log-structured file system rather than as sequential delta chains.

### Immutable Snapshots: Up to 200 Per VM

Each protection group can retain up to 200 snapshots per VM. When the snapshot count exceeds 200, the oldest snapshots are automatically expired. Critically, these snapshots can be configured as *immutable*---a simple toggle in the protection-group settings. An immutable snapshot cannot be modified, deleted, or encrypted by any process, including a compromised administrator account or a ransomware payload running inside the guest.

The 200-snapshot limit is generous by any standard. With a one-minute RPO, 200 snapshots provide over three hours of granular recovery points. With a 15-minute RPO---a more common configuration for general-purpose workloads---200 snapshots provide 50 hours of recovery history. With an hourly RPO, the retention window extends to over eight days. Administrators can mix RPO intervals across protection groups to balance granularity against storage consumption.

### Ransomware Recovery Workflow

The combination of immutable snapshots and native replication creates a purpose-built ransomware recovery workflow:

1. **Detection.** An anomaly-detection system (VMware Advanced Cyber Compliance, a third-party EDR, or even a manual alert) identifies a potential encryption event.

2. **Isolation.** The affected VMs are quarantined on the source cluster using NSX microsegmentation or by powering them off.

3. **Recovery-point selection.** The administrator navigates to the target cluster's snapshot catalog and identifies the last known-good snapshot before the encryption event began. Because snapshots are immutable, the attacker cannot have tampered with them.

4. **Restoration.** The VM is recovered from the selected snapshot on the target cluster---or, if the source site is compromised, directly at the recovery site. Because the snapshot is a full vSAN object, recovery is a metadata operation that completes in seconds; the VM can power on immediately and lazily populate data blocks on first access.

5. **Validation.** The restored VM is booted in an isolated network segment for forensic validation before being placed back into production.

### VMware Live Recovery Integration

vSAN-to-vSAN replication integrates directly with VMware Live Recovery (VLR), the unified disaster-recovery and cyber-recovery orchestration platform in VCF 9. VLR provides automated recovery plans---scripted failover and failback sequences with dependency ordering, IP re-mapping, and validation scripts---on top of the raw snapshot replication.

A key operational benefit of this integration is data-transfer efficiency. VLR, combined with vSAN Data Protection's snapshot-level replication, can reduce data transfer during a disaster or cyber recovery event by 93 to 96 percent compared to full-VM replication, depending on VM size and rate of change. For an organization with hundreds of VMs, this translates to tens or hundreds of terabytes less data transferred during recovery---a transformative reduction in both egress costs and time to recovery.

In VCF 9, VLR ships as a single unified appliance that manages all recovery operations: vSphere-based enhanced replication for site-to-site DR, vSAN-based snapshot replication for cyber recovery, and orchestrated failover/failback across both. This consolidation eliminates the operational overhead of managing separate Site Recovery Manager and vSAN replication appliances.

### RPO and RTO Expectations

| Scenario | Typical RPO | Typical RTO |
|---|---|---|
| Critical databases (SQL, Oracle) | 1--5 minutes | Minutes (snapshot mount + lazy hydration) |
| General production VMs | 15--30 minutes | Minutes |
| Dev/test environments | 1--4 hours | Minutes to tens of minutes |
| Cyber recovery (immutable snapshots) | Varies by retention policy | Minutes (plus validation time) |

The RTO is consistently in the minutes range because recovery from a vSAN snapshot is a metadata operation---the VM can boot immediately and read data on demand from the snapshot's underlying objects. There is no need to hydrate an entire VMDK before the VM becomes operational.

---

## Expanded Principal Storage

### Why It Matters

In previous versions of VMware Cloud Foundation, vSAN was the only supported option for *principal storage* in the management workload domain during an automated (greenfield) deployment through SDDC Manager. Supplemental storage---NFS, iSCSI, Fibre Channel---could be added afterward for workload domains, but the management domain had to start with vSAN. This requirement meant that every VCF deployment began with a minimum of three vSAN-capable hosts in the management domain, even if the organization had an existing enterprise SAN with abundant capacity.

VCF 9.0 changes this equation. NFSv3 and Fibre Channel VMFS datastores are now supported as principal storage for the management workload domain in greenfield deployments initiated through the VCF Installer. This is not just a licensing concession---it reflects a deliberate architectural decision to make VCF accessible to organizations whose storage strategy is built around external arrays.

### NFS and FC: Now First-Class Citizens

With NFS or FC as principal storage, the minimum host count for the management domain drops from three (required for vSAN) to two. This reduces the entry cost for a VCF deployment significantly and allows organizations to leverage existing investments in NetApp, Pure Storage, Dell PowerStore, or any other array that presents NFSv3 or FC-backed VMFS datastores.

NFSv3 is supported for both management and workload domains as principal or supplemental storage. NFSv4.1, which adds stronger authentication (Kerberos) but introduces additional complexity, is supported only as supplemental storage in the current release.

For storage types not available in the greenfield installer---iSCSI, NFS v4.1, FCoE, and NVMe over Fabrics (FC, TCP, or RDMA transport)---alternative configuration methods are documented for adding them as principal storage to workload domains post-deployment.

### Decision Guide: vSAN vs. External Principal Storage

The choice between vSAN and external storage as principal storage is not binary; many organizations use both, with vSAN for workload domains where HCI simplicity and linear scale-out are valued, and external arrays for the management domain or for workloads with specific array-based features (synchronous replication, inline deduplication at the array controller, etc.).

**Choose vSAN when:** You want a single operational model across compute and storage. You are building a greenfield environment with no existing SAN. You need the data-protection features described in this chapter (native replication, immutable snapshots). You want linear scale-out---adding a host adds both compute and storage simultaneously.

**Choose external NFS/FC when:** You have an existing enterprise SAN with available capacity and expertise. Your management domain needs to be as small as possible (two hosts). You require array-specific features (synchronous metro replication, storage-level encryption with key management, array-level snapshots for compliance). Your organization has a dedicated storage team that manages the array lifecycle independently.

**Hybrid approach:** Use external storage for the management domain (smaller footprint, leverages existing array) and vSAN ESA for workload domains (linear scale-out, native data protection, cluster-wide deduplication). This pattern is increasingly common among customers I advise who are modernizing gradually.

---

## Storage for AI Pipelines

The intersection of private-cloud infrastructure and artificial intelligence is no longer theoretical. Organizations are deploying GPU-accelerated inference clusters inside VCF to maintain data sovereignty, avoid public-cloud egress costs, and comply with regulations that prohibit sending sensitive data to third-party model providers. The storage layer is often the bottleneck that determines whether these workloads succeed or fail.

### Training vs. Inference: Different Storage Profiles

**Model training** is bandwidth-hungry and moderately latency-tolerant. A training pipeline reads massive datasets---often tens or hundreds of terabytes---in sequential sweeps, epoch after epoch. The storage layer must sustain high sequential read throughput (multiple GB/s per GPU node) and store large datasets efficiently. Latency on individual I/O operations matters less than aggregate bandwidth, because the training framework pipelines data loading with GPU computation.

**Real-time inference** is latency-sensitive and IOPS-intensive. An inference server loads a model into GPU memory at startup (a large sequential read) and then serves predictions against a stream of small, random input requests. The storage layer must deliver low-latency random reads for model loading and checkpoint retrieval, with consistent sub-millisecond response times under concurrent load.

vSAN ESA's all-NVMe architecture serves both profiles. The log-structured write path and full-stripe reads deliver high sequential throughput for training data ingestion. The distributed storage pool, with every NVMe device available to serve read requests, delivers the low-latency random-read performance that inference workloads demand. The Enhanced Data Path in vSAN 9, with optional DPU offload, reduces CPU overhead further---critical when host CPUs are already sharing duty with GPU management and vMotion operations.

### NVMe Tiering as AI Accelerant

VCF 9 also introduces advanced memory tiering that allows NVMe devices to extend the memory tier. With a default DRAM-to-NVMe ratio of 1:1, a host with 1 TB of DRAM and NVMe-backed memory tiering can present 2 TB of addressable memory to VMs. For inference workloads running large language models, this can mean the difference between fitting a model entirely in "memory" versus paging model weights from storage---a latency cliff that degrades inference throughput dramatically.

The combination of NVMe storage for persistent data and NVMe memory tiering for hot data creates a two-tier NVMe architecture where the same physical drives accelerate both storage I/O and memory access. This is unique to the VCF stack and represents a compelling argument for consolidating AI workloads onto the same infrastructure that runs traditional enterprise applications.

### The Data Sovereignty Argument

Organizations in regulated industries---financial services, healthcare, government, defense---face a structural constraint: the data they want to use for AI cannot leave their premises. Public-cloud AI services are off the table for sensitive datasets. VCF 9 with vSAN ESA provides the storage foundation for a private AI platform that keeps data on-premises, under the organization's physical and logical control, while delivering the performance characteristics that GPU-accelerated workloads require.

vSAN ESA's support for GPUDirect Storage over RDMA takes this further by allowing direct I/O transfer from NVMe storage to GPU memory without CPU involvement. This eliminates a significant bottleneck in the training data pipeline and brings on-premises AI storage performance closer to the purpose-built GPU clusters offered by cloud providers.

---

## vSAN Health and Monitoring

A storage system that you cannot observe is a storage system that will eventually surprise you. vSAN has invested heavily in operational visibility, and VCF 9 brings the monitoring story together under the VCF Operations umbrella.

### The vSAN Health Service

The vSAN Health Service is the first-line monitoring layer, integrated directly into the vSphere Client. It runs a comprehensive suite of health checks organized into categories:

- **Hardware compatibility.** Validates that every host's storage controllers, NVMe devices, and firmware versions are on the vSAN Hardware Compatibility List (HCL). An incompatibility here is not advisory---it is a support boundary.

- **Network configuration.** Verifies that vSAN VMkernel adapters are configured correctly, that multicast (for OSA) or unicast (for ESA) communication is functioning, and that network latency between hosts is within acceptable bounds.

- **Cluster configuration.** Checks for misconfigured time synchronization, inconsistent advanced settings across hosts, and missing ESXi patches.

- **Storage device health.** Monitors SSD/NVMe wear indicators (percentage of rated write endurance consumed), identifies devices reporting uncorrectable errors, and flags devices that are approaching end of life.

- **Data health.** Verifies that all vSAN objects are in a compliant state---that is, their actual placement matches the policy requirements. Objects that are temporarily non-compliant (e.g., during a rebuild after a host failure) are flagged with an expected completion time.

- **Stretched-cluster health.** For stretched-cluster configurations, validates witness connectivity, inter-site latency, and site-awareness correctness.

Each health check produces a status: green (pass), yellow (warning), or red (critical). The vSAN Health Service dashboard aggregates these statuses into an at-a-glance cluster health score, making it easy to identify clusters that need attention during a morning operational review.

### Key Metrics to Monitor

Beyond the health checks, vSAN exposes a rich set of performance metrics that should be part of every monitoring strategy:

- **IOPS** (read and write, by cluster, host, disk group/storage pool, and individual device). Sudden IOPS spikes may indicate runaway processes or backup jobs; sustained high IOPS near device limits may indicate the need for additional hosts.

- **Latency** (read and write, average and percentile). vSAN ESA clusters should sustain average write latency well under one millisecond for most workloads. Sustained latency above two milliseconds warrants investigation.

- **Throughput** (MB/s). Critical for sequential workloads like backups, data migrations, and AI training pipelines.

- **Congestion.** vSAN's internal congestion metric (0--255) reflects back-pressure in the storage stack. Values above 30 indicate that the cluster is struggling to keep up with I/O demand. Persistent congestion above 60 is a capacity or performance planning action item.

- **Resync traffic.** After a host failure, maintenance event, or policy change, vSAN resyncs data to restore compliance. Monitoring resync traffic helps administrators predict when the cluster will return to a fully protected state.

- **Deduplication and compression ratios.** With cluster-wide deduplication enabled, these ratios directly affect usable capacity projections. A sudden drop in the deduplication ratio may indicate a change in workload mix.

### The vSAN Observer Tool

The vSAN Observer is a diagnostic and performance-analysis tool that ships with vSAN and provides deep visibility into the storage stack. Unlike the Health Service, which focuses on status and alerting, Observer provides real-time and historical performance data at a level of granularity suited to troubleshooting:

- **Per-device latency histograms.** Identify individual NVMe devices that are contributing disproportionate latency, which may indicate firmware issues or early device degradation.

- **IOPS and bandwidth per device.** Confirm that I/O is distributed evenly across all devices in the storage pool. Hotspots may indicate policy misconfiguration or a rebalancing need.

- **vSAN memory-pool consumption.** Monitor the memory allocated to vSAN's in-kernel caches and metadata structures. Excessive memory consumption may indicate an unusually large object count or a deduplication metadata index that is growing faster than expected.

- **Network-level statistics.** Per-VMkernel-adapter throughput, retransmits, and latency measurements help isolate storage performance issues caused by network contention rather than device limitations.

Observer runs as an on-demand diagnostic tool---it is typically activated during troubleshooting sessions rather than running continuously. For continuous monitoring, VCF Operations provides a more sustainable operational model.

### VCF Operations and the Observability Workbench

VCF Operations (the evolution of vRealize Operations, now integrated into the VCF platform) provides the enterprise-grade monitoring and analytics layer for vSAN. In VCF 9, VCF Operations includes purpose-built dashboards for storage operations:

**Storage Operations View.** A unified operational overview for all storage types powering the VCF environment---vSAN, NFS, FC, iSCSI---in a single pane. This view surfaces storage-related alerts, capacity forecasting, and performance trends across the entire VCF instance.

**vSAN Health Dashboard.** Aggregates cluster health scores from the vSAN Health Service and presents them alongside performance and capacity metrics. Clusters are categorized by health score: 81--100 (green, healthy), 61--80 (yellow, degraded), and 0--60 (red, unhealthy, requiring immediate attention).

**vSAN Performance Dashboards.** Separate dashboards for ESA and OSA clusters, with a "Metrics Picker" feature that allows operators to drag and drop metrics into chart areas for ad-hoc analysis. Selected metrics are displayed in time-series format for any cluster or host, enabling rapid correlation of performance events.

**The Observability Workbench.** This is the troubleshooting power tool within VCF Operations. It allows operators to generate support bundles, use Log Assist to correlate logs and metrics across the storage, compute, and networking layers, and build custom investigation dashboards during an incident. The Workbench integrates log data, metric data, and topology data into a single investigation workflow, reducing the mean time to diagnose storage performance issues from hours to minutes.

The combination of the vSAN Health Service (real-time, in-client), the Observer tool (deep diagnostic), and VCF Operations (enterprise analytics and workbench-based investigation) creates a three-tier observability model that scales from a single-admin small deployment to a multi-team enterprise operations center.

---

## Putting It All Together

vSAN 9 is not a point release. It is the version where VMware's software-defined storage story reaches the level of capability and maturity that makes it a credible replacement for dedicated storage arrays in most enterprise use cases. Global deduplication eliminates the last major space-efficiency gap. Native replication with immutable snapshots delivers ransomware protection that was previously available only through third-party backup products. Expanded principal storage removes the vSAN-only entry requirement that limited VCF adoption in SAN-heavy shops. And the Enhanced Data Path with DPU offload positions vSAN as the storage foundation for the next wave of AI workloads running on private infrastructure.

The decision framework is straightforward:

- **New deployments:** ESA, always. Use RAID-5/6 erasure coding (the performance penalty is gone). Enable cluster-wide deduplication for VDI, dev/test, and any workload with significant data commonality. Configure native replication with immutable snapshots to at least one secondary site.

- **Existing OSA deployments:** Plan the migration to ESA. Add ESA hosts, migrate workloads, decommission OSA hosts. The migration is non-disruptive but requires ESA-qualified hardware.

- **Mixed environments:** Use external NFS or FC for the management domain if you have an existing array. Use vSAN ESA for workload domains. Let SPBM policies enforce per-VM protection levels.

- **AI initiatives:** Validate that your ESA ReadyNodes include NVMe devices with sufficient endurance for training-write patterns. Evaluate memory tiering to extend addressable memory for inference VMs. Test GPUDirect Storage over RDMA if your GPU nodes support it.

The chapters that follow will build on this storage foundation as we examine networking (Chapter 6) and workload management (Chapter 7). Storage policy decisions made here ripple through every subsequent layer of the VCF stack---get them right, and the rest of the platform operates within well-understood guardrails.


---

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


---

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


---

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


---

# Chapter 9 --- AI and Modern Workloads

The artificial-intelligence wave has arrived with a force that few technology cycles have matched. Every enterprise I work with---from financial-services firms in Frankfurt to defense contractors in Virginia---is running at least one proof-of-concept that involves large language models, computer-vision pipelines, or predictive analytics powered by deep learning. The question is no longer *whether* to adopt AI but *where* to run it.

For many organizations, the reflexive answer has been "spin it up in a hyperscaler." That instinct is understandable: public clouds were first to market with GPU instances, managed notebooks, and turnkey inference endpoints. But as models grow, datasets multiply, and regulatory scrutiny intensifies, a counter-movement is well underway. IDC reported that 86 percent of CIOs planned to repatriate at least some workloads in 2025, and AI pipelines sit near the top of the repatriation list. Broadcom has responded by making VMware Cloud Foundation 9 an AI-native platform, embedding Private AI Services directly into the stack and forging deep integrations with NVIDIA accelerated computing.

This chapter examines why enterprises are choosing on-premises infrastructure for AI, how VCF 9 delivers GPU-enabled Workload Domains and containerized ML frameworks, and where the honest trade-offs lie between private cloud and hyperscaler alternatives.

---

## 9.1 Why Enterprises Choose On-Premises for AI

Before we touch a single configuration screen, we need to understand the business gravity that pulls AI workloads back inside the data center. Six forces dominate the conversation in every customer engagement I lead.

### 9.1.1 Data Gravity Math

AI models are only as good as the data they consume, and enterprise data is overwhelmingly generated and stored on-premises or in colocations. A mid-size bank may hold 500 TB of transaction records, a hospital network 200 TB of imaging studies, a manufacturer 80 TB of sensor telemetry. Moving that volume to a hyperscaler for a training run is not merely slow; it is expensive and operationally risky.

Consider the math. At AWS standard egress rates of roughly $0.09 per gigabyte, moving 100 TB out of a region costs approximately $9,000---each time. A single fine-tuning cycle on a large language model that ingests 100 TB of proprietary text and produces 50 TB of checkpoint data will generate meaningful egress charges every iteration. Over a twelve-month development cycle with dozens of iterations, the cumulative bill can exceed six figures on data movement alone.

The physics of the problem are straightforward: it is cheaper to bring compute to the data than to move data to the compute. This is the data-gravity argument, and it only strengthens as datasets grow.

### 9.1.2 Egress Cost Example

Let me put a sharper point on it. Suppose your data-science team trains a domain-specific model weekly, each cycle reading 40 TB from cloud object storage and writing 10 TB of artifacts. Monthly egress: roughly 200 TB. At $0.09 per GB, that is $18,000 per month---$216,000 per year---just for the privilege of moving bytes. On-premises, the same traffic traverses a spine-leaf fabric you already own. The marginal cost of that bandwidth is zero.

### 9.1.3 Regulatory Constraints

The General Data Protection Regulation, the EU AI Act, HIPAA, and sector-specific mandates such as DORA for financial services increasingly govern not just where data is stored but where it is *processed*. Training a model on patient health records in a US hyperscaler region when the data originates in Germany raises cross-border transfer questions that legal teams cannot answer with a standard Data Processing Agreement. Running the same training job on VCF infrastructure inside a Frankfurt colocation eliminates the question entirely.

### 9.1.4 Model IP Protection

A fine-tuned model embodies months of data-science effort and, critically, the proprietary data it was trained on. Hosting model weights and training pipelines on shared infrastructure---even with encryption at rest and in transit---introduces residual risk that many boards consider unacceptable. Private infrastructure provides administrative control over every layer of the stack, from the firmware on the NIC to the namespace boundary in Kubernetes.

### 9.1.5 Inference Latency

Training tolerates latency; inference often does not. A fraud-detection model that must score a transaction in under 10 milliseconds, a manufacturing quality-inspection system operating at line speed, or a conversational AI agent serving customer support calls---all demand predictable, low-latency inference. Colocating inference servers with the application tier on the same VCF Workload Domain removes the network variability inherent in cross-region or cross-provider calls.

### 9.1.6 Three-Year Ownership Cost

When I help customers build a total-cost-of-ownership model, the on-premises case for steady-state AI workloads is compelling. GPU servers depreciate over three to five years, but their per-hour cost drops with every month of operation. A four-node cluster of NVIDIA HGX servers running on VCF, fully loaded with licensing, power, cooling, and operations staff, typically breaks even with equivalent hyperscaler GPU instances within 12 to 18 months. By year three, the private-cloud option can deliver a 40 to 60 percent cost advantage for workloads that run continuously. Broadcom's own customer engagements have shown TCO benefits of 3 to 5 times compared to hyperscaler alternatives for sustained AI workloads.

The calculus changes for bursty experimentation or one-off training runs, and we will address that nuance in Section 9.8.

---

## 9.2 VCF Private AI Services

VMware Cloud Foundation 9.0 introduced Private AI Services as a standard entitlement, transforming VCF from a general-purpose private-cloud platform into an AI-native one. The suite comprises six integrated capabilities: GPU Monitoring, Model Store, Model Runtime, Agent Builder, Vector Database, and Data Indexing and Retrieval. Together, they give platform teams a turnkey foundation for generative and predictive AI without bolting on a half-dozen third-party tools.

### 9.2.1 GPU-Enabled Workload Domains

The fundamental building block is the GPU-enabled Workload Domain. In VCF's architecture, a Workload Domain is an independently managed cluster with its own vCenter, vSAN datastore, and NSX transport zone. By dedicating a Workload Domain to AI workloads, you isolate GPU scheduling, storage I/O patterns, and network traffic from traditional enterprise applications.

A GPU-enabled Workload Domain in VCF 9 supports NVIDIA Blackwell-architecture accelerators---including RTX PRO 6000 Blackwell Server Edition and B200 GPUs---as well as the Hopper and Ada Lovelace families. The domain can also leverage AMD Instinct MI350 Series GPUs with AMD ROCm Enterprise software for customers who prefer a multi-vendor accelerator strategy.

Hosts within the domain are configured with NVIDIA ConnectX-7 NICs or BlueField-3 400G DPUs and connected via Enhanced DirectPath I/O, enabling GPUDirect RDMA and GPUDirect Storage for high-speed, multi-node data transfer. This is the plumbing that allows distributed training jobs to exchange gradient tensors at near-line-rate without bouncing through the hypervisor's network stack.

### 9.2.2 NVIDIA vGPU Profiles

Not every AI workload requires an entire GPU. Inference serving for a modest language model, data preprocessing with RAPIDS, or lightweight fine-tuning can share a physical GPU across multiple virtual machines using NVIDIA vGPU technology.

vGPU profiles are pre-defined slices of GPU memory and compute. The C-series (compute) profiles are purpose-built for AI and HPC workloads. For example, a single NVIDIA L40S can be partitioned into multiple C-series profiles, each allocated a specific fraction of the GPU's 48 GB of GDDR6 memory. A data-science team running four concurrent Jupyter notebook sessions for exploratory analysis might each receive a 12 GB slice, while a production inference server could claim the full device.

VCF 9 and vSphere 8 Update 3 introduced a significant improvement: mixed vGPU profiles can now coexist on the same physical GPU. Previously, all VMs sharing a GPU had to use the same profile type and memory size. The new flexibility lets you place a 12 GB inference workload alongside an 8 GB preprocessing job on the same accelerator, improving consolidation ratios and reducing the number of GPUs sitting partially idle.

### 9.2.3 Placement Policies and GPU Passthrough

VCF provides two GPU consumption models:

- **vGPU (shared):** Multiple VMs share a single GPU via time-slicing or MIG (Multi-Instance GPU) partitioning. Best for inference, notebook environments, and lightweight training.
- **DirectPath I/O (passthrough):** A physical GPU is exclusively assigned to a single VM. Best for large-scale training where the workload needs unfettered access to GPU memory and PCIe bandwidth.

DRS affinity and anti-affinity rules, combined with VM-Host placement policies, ensure that GPU-hungry VMs land on hosts with available accelerators. vSphere's GPU-aware DRS can balance GPU utilization across a cluster, migrating non-GPU workloads off GPU hosts to free headroom. Note that vMotion of VMs with passthrough GPUs requires the suspend-and-resume approach, so maintenance windows should be planned accordingly. vGPU-backed VMs, by contrast, support live migration.

### 9.2.4 Private AI Services in Detail

Let me briefly describe each of the six services that ship with VCF 9:

- **GPU Monitoring:** Integrated into VCF Operations 9.0, this provides summary dashboards showing GPU-equipped clusters and hosts, drill-down views to individual physical GPUs, and vGPU-level metrics. Operators can set alerts on GPU temperature, memory utilization, and compute saturation without deploying a separate monitoring stack.
- **Model Store:** A governed repository where ML Ops teams curate open-source and commercial large language models with role-based access control. Data scientists browse a catalog, pull a model into their namespace, and begin inference or fine-tuning---all under enterprise RBAC and audit logging.
- **Model Runtime:** A managed serving layer that handles model loading, scaling, and health checks. Think of it as a simplified, VCF-native alternative to standing up your own inference infrastructure.
- **Agent Builder:** A low-code environment for constructing AI agents that chain LLM calls, tool invocations, and retrieval-augmented generation (RAG) workflows.
- **Vector Database:** Built on pgvector atop PostgreSQL and delivered through Data Services Manager, this provides the semantic-search backend that RAG applications need. Enterprise support, backup integration, and multi-tenancy come standard.
- **Data Indexing and Retrieval:** The ingestion pipeline that chunks, embeds, and indexes enterprise documents into the Vector Database, enabling domain-specific context for LLM queries.

These services operate as a multi-tenant Models-as-a-Service layer, allowing separate business units to share GPU infrastructure and curated models while maintaining strict data isolation within their respective namespaces.

---

## 9.3 Storage for AI Workloads

AI pipelines impose storage demands that differ sharply from traditional enterprise applications. Understanding the distinction between training and inference I/O profiles is essential for sizing a VCF environment correctly.

### 9.3.1 Training vs. Inference Storage Requirements

**Training** is bandwidth-hungry. A distributed training job reads massive datasets---often hundreds of gigabytes to tens of terabytes---sequentially, shuffles the data into batches, and streams it to GPUs. Checkpoint writes occur periodically (every few minutes to every few hours) and produce large sequential writes. The critical metric is sustained sequential throughput, measured in GB/s.

**Inference** is IOPS-hungry. An inference server loads model weights once (a one-time sequential read that can be tens of gigabytes for a large language model) and then processes a stream of small, random-access requests. Latency per request matters more than aggregate throughput. The critical metrics are random-read IOPS and tail latency at the 99th percentile.

### 9.3.2 vSAN ESA for AI Pipelines

vSAN Express Storage Architecture, standard in VCF 9, is built around a single-tier, all-NVMe design that eliminates the caching-tier bottleneck of the original storage architecture. Every device in the pool contributes both capacity and performance, and the architecture natively supports NVMe-oF for front-end connectivity.

For AI training workloads, vSAN ESA's flat NVMe pool delivers the sequential throughput that data-loading pipelines demand. The architecture's native compression and deduplication---with global deduplication in vSAN 9.0 reducing storage TCO by up to 34 percent---help contain the cost of storing large training datasets and checkpoint files.

For inference workloads, the low-latency random-read profile of NVMe SSDs aligns naturally with model-serving I/O. When an inference server like Triton needs to load a 70-billion-parameter model from disk, the sustained read bandwidth of a vSAN ESA datastore on modern NVMe drives can complete the load in seconds rather than minutes.

### 9.3.3 NVMe Tiering for Preprocessing

VCF 9 introduced production-ready NVMe memory tiering, a feature that allows hosts to extend addressable memory beyond physical DRAM by using local NVMe devices as a memory tier. For AI preprocessing workloads---Pandas DataFrames, Spark transformations, feature-engineering pipelines---that are memory-bound but not latency-critical, NVMe tiering can reduce server TCO by up to 38 percent by allowing you to configure hosts with less DRAM and more NVMe capacity.

This is particularly valuable in the data-preparation phase of an ML pipeline, where large datasets must be loaded into memory for cleaning, normalization, and feature extraction before being fed to a GPU for training. Rather than purchasing hosts with 2 TB or 4 TB of DRAM, you can deploy hosts with 512 GB of DRAM plus NVMe tiering and achieve acceptable preprocessing performance at a fraction of the hardware cost.

### 9.3.4 GPUDirect Storage

For the most demanding training workloads, VCF supports NVIDIA GPUDirect Storage over RDMA, which enables direct I/O transfer from NVMe storage to GPU memory without CPU involvement. This eliminates the CPU as a bottleneck in the data-loading path and is essential for keeping high-end GPUs fed with data during large-scale distributed training.

### 9.3.5 The Data Sovereignty Argument

Storage is where the data-sovereignty conversation becomes tangible. When training data resides on a vSAN datastore inside your own data center, you have unambiguous administrative control over every byte. There is no shared-responsibility model to parse, no subpoena risk under foreign jurisdiction, and no ambiguity about which encryption keys protect the data. For organizations subject to GDPR, HIPAA, or classified-data handling requirements, this clarity is often the decisive factor.

---

## 9.4 Networking for AI Workloads

Network design can make or break an AI deployment. Training jobs are bandwidth-sensitive; inference is latency-sensitive; both benefit from offloading network processing away from the CPU cores that should be running workloads.

### 9.4.1 Enhanced Data Path for Inference Latency

NSX Enhanced Data Path (EDP) is a DPDK-style data plane that processes packets in a poll-mode loop rather than relying on traditional interrupt-driven I/O. The result is higher throughput at lower, more predictable latency with significantly reduced CPU overhead on the hypervisor.

EDP is available in two modes in NSX 4.x and later:

- **EDP-Standard:** Interrupt-based, suitable for general low-latency workloads.
- **EDP-Performance:** Polling-based, where dedicated cores continuously poll for packets. This mode delivers the lowest possible latency and is the recommended configuration for real-time inference endpoints where every microsecond matters.

For an inference-serving Workload Domain, I recommend enabling EDP-Performance on the transport nodes that host inference VMs or Kubernetes worker nodes. The reduction in jitter alone---eliminating interrupt coalescing delays---can shave hundreds of microseconds off tail latency, which directly translates to better P99 response times for end users.

### 9.4.2 DPU Offload: Freeing CPU for Workloads

NVIDIA BlueField-3 DPUs, supported in VCF 9 with Enhanced DirectPath I/O, offload the entire NSX networking and security stack---virtual switching, distributed firewall, load balancing---from the host CPU to the DPU's Arm cores. The x86 cores that were previously consumed by network processing are returned to the workload pool.

On a typical two-socket server with 128 cores, NSX network processing can consume 4 to 8 cores depending on traffic volume. Offloading to a BlueField-3 DPU returns those cores to GPU-adjacent workloads---data loaders, preprocessing threads, inference serving---where they directly improve AI throughput. In a 16-host AI Workload Domain, recovering 6 cores per host gives you 96 additional cores of compute capacity at zero incremental hardware cost.

### 9.4.3 RDMA for Distributed Training

Multi-node distributed training requires exchanging gradient tensors between GPUs on different hosts at every training step. Traditional TCP/IP networking introduces unacceptable latency and CPU overhead for this all-reduce communication pattern.

VCF supports RDMA over Converged Ethernet (RoCE) via NVIDIA ConnectX-7 NICs, enabling GPUDirect RDMA---the direct transfer of data between GPU memory on one host and GPU memory on another without involving the CPU or operating-system network stack on either side. The performance approaches bare-metal levels: NVIDIA's benchmarks show that multi-node deep learning training on vSphere with GPUDirect RDMA achieves near-bare-metal throughput, making virtualized distributed training practical for even the largest models.

To configure RoCE in a VCF environment, you need a dedicated VLAN for RDMA traffic with Priority Flow Control (PFC) enabled on the physical switches, lossless quality-of-service configuration at the fabric level, and Enhanced DirectPath I/O enabled for the ConnectX-7 or BlueField-3 interfaces on each host.

---

## 9.5 Containerized AI on vSphere Kubernetes Service

While VMs remain the right abstraction for some AI workloads---particularly those that require passthrough GPUs or legacy CUDA applications---the AI/ML ecosystem has overwhelmingly standardized on Kubernetes as the orchestration layer. vSphere Kubernetes Service (VKS), included with VCF 9, provides a CNCF-certified Kubernetes runtime whose control-plane and worker nodes are vSphere VMs. This gives platform teams the operational familiarity of vSphere---snapshots, HA, DRS, vMotion---with the developer experience of upstream Kubernetes.

### 9.5.1 Kubeflow for ML Pipelines

Kubeflow is the de facto open-source platform for orchestrating end-to-end machine-learning workflows on Kubernetes. On VKS, a Kubeflow installation provides:

- **Kubeflow Pipelines:** Directed acyclic graph (DAG)-based workflow orchestration for data ingestion, preprocessing, training, evaluation, and model registration.
- **Kubeflow Notebooks:** Managed Jupyter and VS Code notebook servers with GPU access, allowing data scientists to develop interactively within the governed cluster.
- **Katib:** Automated hyperparameter tuning using Bayesian optimization, grid search, or random search across GPU-accelerated trial runs.

VKS's integration with vSAN CSI provides persistent volumes backed by the vSAN ESA datastore, so pipeline artifacts, training checkpoints, and model weights persist across pod restarts.

### 9.5.2 Ray for Distributed Computing

Ray is a unified framework for scaling Python workloads---including distributed training, reinforcement learning, and hyperparameter search---across a cluster. RayTrain wraps popular frameworks like PyTorch and TensorFlow, distributing training across multiple GPU-equipped worker pods with minimal code changes.

On VKS, a RayCluster custom resource defines a head node and a set of GPU worker nodes. The Kubernetes scheduler, aware of GPU resources via the NVIDIA device plugin, places worker pods on nodes with available GPUs. Ray's autoscaler can request additional worker pods when the job queue deepens, and VKS's cluster autoscaler can provision additional worker-node VMs if the cluster lacks capacity.

### 9.5.3 Triton Inference Server

NVIDIA Triton Inference Server is the standard for high-performance, multi-framework model serving. It supports TensorRT, ONNX Runtime, PyTorch, TensorFlow, and custom Python backends, and it handles dynamic batching, model ensembles, and concurrent model execution on a single GPU.

Deploying Triton on VKS involves a Deployment manifest that requests GPU resources, a Service for load balancing, and optionally a Horizontal Pod Autoscaler that scales replicas based on GPU utilization or inference-queue depth. Triton's model repository can be backed by a vSAN-backed PersistentVolume or an S3-compatible object store running on-premises.

### 9.5.4 KServe for Standardized Inference

KServe, a CNCF incubating project and part of the Kubeflow ecosystem, provides a higher-level abstraction for model serving. It wraps inference runtimes---including Triton, TorchServe, and custom containers---behind a standardized InferenceService custom resource that handles canary rollouts, traffic splitting, autoscaling to zero, and request logging.

For organizations that want to offer inference-as-a-service to internal consumers, KServe on VKS provides a self-service model-deployment experience. A data scientist pushes a trained model to the Model Store, creates an InferenceService manifest referencing it, and KServe handles the rest---provisioning a pod, configuring a route, and scaling based on demand.

### 9.5.5 Argo CD for MLOps

GitOps is the operational model that ties the ML lifecycle together. Argo CD, a declarative GitOps continuous-delivery tool for Kubernetes, watches Git repositories for changes to Kubernetes manifests and reconciles the cluster state accordingly.

In an MLOps context, the workflow is as follows: a data scientist trains a model and registers a new version in the Model Store. An automated pipeline updates the KServe InferenceService manifest in Git with the new model reference. Argo CD detects the change, pulls the updated manifest, and initiates a canary rollout---sending a small percentage of traffic to the new model version, monitoring error rates and latency, and progressively shifting traffic if the new version passes validation. If the new model degrades performance, Argo CD automatically rolls back to the previous version.

This approach provides full auditability---every model deployment is a Git commit---and eliminates manual kubectl operations in production, which is a governance requirement in regulated industries.

---

## 9.6 Private AI Reference Architecture

Let me describe a complete reference architecture that I have deployed at multiple customer sites. This design balances performance, isolation, and operational simplicity.

### 9.6.1 Domain Layout

The architecture comprises three VCF Workload Domains:

1. **Management Domain:** Hosts vCenter Server, SDDC Manager, NSX Manager, VCF Operations, Aria Automation, and the Argo CD instance that governs GitOps deployments. This domain contains no GPU hardware and runs on standard compute hosts. It is the control plane for the entire environment.

2. **AI Compute Domain:** A GPU-enabled Workload Domain with 8 to 16 hosts, each equipped with 2 to 8 NVIDIA GPUs (Blackwell B200 or Hopper H100), dual ConnectX-7 100/200/400G NICs or BlueField-3 DPUs, and 512 GB to 2 TB of DRAM. This domain runs two VKS clusters:
   - **Training Cluster:** Configured for distributed training with RoCE-enabled networking, Ray and Kubeflow Pipelines, and large PersistentVolumes for checkpoint storage.
   - **Inference Cluster:** Configured for low-latency serving with EDP-Performance networking, Triton and KServe, and Horizontal Pod Autoscaling tied to GPU metrics.

3. **Data Lake Domain:** A storage-dense Workload Domain running vSAN ESA with high-capacity NVMe drives. This domain hosts MinIO (S3-compatible object storage) for training datasets, the Vector Database for RAG applications, and any Hadoop or Spark clusters used for feature engineering. Network connectivity to the AI Compute Domain uses 100G or faster links on a dedicated NSX segment to ensure data-loading throughput is not constrained by shared network capacity.

### 9.6.2 Networking Design

The network architecture uses four NSX segments:

- **Management Segment:** Carries vCenter, NSX Manager, SDDC Manager, and operations traffic. Standard MTU. Accessible from the corporate network via a Tier-1 gateway.
- **Training Segment:** A dedicated VLAN-backed segment with jumbo frames (MTU 9000) and Priority Flow Control enabled for RoCE traffic. This segment connects GPU hosts within the AI Compute Domain for gradient exchange during distributed training. No external access.
- **Inference Segment:** An overlay segment with EDP-Performance enabled, connecting inference pods to the application tier. A Tier-1 gateway provides load-balanced ingress for inference API consumers.
- **Data Segment:** A high-bandwidth segment connecting the Data Lake Domain to the AI Compute Domain. Jumbo frames enabled. Traffic is predominantly large sequential reads (training data) and writes (checkpoints, model artifacts).

NSX Distributed Firewall policies enforce micro-segmentation between these segments, ensuring that training traffic cannot reach the inference endpoints and that data-lake access is restricted to authorized service accounts.

### 9.6.3 Operational Workflow

The day-to-day workflow in this architecture follows a clear pipeline:

1. Data engineers ingest raw data into the Data Lake Domain (MinIO).
2. Preprocessing jobs run on VKS in the AI Compute Domain, reading from MinIO, transforming data, and writing features back to MinIO.
3. Training jobs, orchestrated by Kubeflow Pipelines, read features and train models using Ray or native PyTorch distributed training.
4. Trained models are registered in the VCF Model Store with version metadata and lineage tracking.
5. An Argo CD pipeline detects the new model version and deploys it to the Inference Cluster via KServe, using a canary rollout strategy.
6. GPU Monitoring in VCF Operations provides visibility into GPU utilization, memory consumption, and inference latency across the environment.

---

## 9.7 Sovereign AI

Sovereignty is no longer a niche concern for government agencies. The EU AI Act, GDPR's evolving interpretations around AI training data, and the geopolitical tension between data-protection regimes and foreign surveillance laws (notably the US CLOUD Act) have made data sovereignty a board-level issue for any enterprise operating across jurisdictions.

### 9.7.1 Geo-Fencing and Data Residency

VCF's Workload Domain architecture provides a natural enforcement boundary for data residency. A Workload Domain deployed in a Frankfurt data center, running on hardware procured and operated by the enterprise (or a trusted sovereign cloud provider), guarantees that training data, model weights, inference requests, and inference responses never leave German soil.

NSX Distributed Firewall and Gateway Firewall policies can enforce geo-fencing at the network layer, blocking any traffic destined for IP ranges outside the approved jurisdiction. Combined with VCF's encryption at rest (vSAN encryption with customer-managed keys) and encryption in transit (NSX IPsec or TLS), this creates a defensible compliance posture for even the most stringent data-residency requirements.

### 9.7.2 GDPR and AI Training

GDPR Article 22 grants individuals the right not to be subject to decisions based solely on automated processing, including profiling. Article 35 requires Data Protection Impact Assessments for high-risk processing, which includes most AI training on personal data. The right to erasure (Article 17) raises the thorny question of whether deleting a data subject's records also requires retraining any model that ingested those records.

Running AI training on VCF infrastructure gives the data controller full administrative control over the training pipeline, the training data, and the resulting model weights. This control simplifies compliance because the organization can demonstrate exactly where data was processed, which model versions were trained on which datasets, and how erasure requests are handled---without relying on a hyperscaler's shared-responsibility documentation.

### 9.7.3 Government and Defense Use Cases

Government agencies and defense organizations represent the extreme end of the sovereignty spectrum. Many require air-gapped or classification-level infrastructure where no component of the stack phones home to an external service. VCF's fully on-premises deployment model---with no mandatory cloud connectivity---makes it one of the few enterprise private-cloud platforms that can operate in disconnected environments.

Gartner's concept of "geopatriation"---moving workloads from global public clouds back into local sovereign environments---predicts that over 75 percent of European and Middle Eastern enterprises will geopatriate virtual workloads by 2030, up from less than 5 percent in 2025. VCF is positioned to capture a significant share of this movement, particularly for AI workloads where the combination of data sensitivity and compute intensity makes local processing the only viable option.

---

## 9.8 VCF vs. Hyperscalers for AI: An Honest Assessment

I would be doing readers a disservice if I painted VCF as the right answer for every AI workload. It is not. The honest assessment requires acknowledging where VCF wins, where hyperscalers win, and how a hybrid strategy threads the needle.

### 9.8.1 Where VCF Wins

**Steady-state inference at scale.** If you are serving millions of inference requests per day on models that change infrequently, on-premises GPU infrastructure on VCF delivers dramatically lower per-request cost than hyperscaler GPU instances. The capital expenditure is front-loaded, but the operating cost flattens over time.

**Data-heavy training on proprietary data.** When the training dataset lives on-premises and measures in the tens or hundreds of terabytes, the egress cost and transfer time of moving it to a hyperscaler make on-premises training the pragmatic choice. GPUDirect Storage and RDMA on VCF deliver the I/O performance needed to keep GPUs fed.

**Regulatory and sovereignty requirements.** For workloads subject to GDPR, HIPAA, ITAR, or classified-data handling, VCF's fully on-premises model eliminates the jurisdictional ambiguity inherent in hyperscaler shared-responsibility frameworks.

**Predictable cost.** VCF licensing is subscription-based and predictable. There are no egress charges, no per-request inference fees, and no surprise bills when a training job runs longer than expected. For CFOs managing AI budgets, this predictability is invaluable.

**Operational continuity.** VCF preserves familiar VMware operational workflows---vMotion, HA, DRS, Live Patching---even for GPU-accelerated workloads. Operations teams do not need to learn a new cloud provider's console, IAM model, and networking abstractions.

### 9.8.2 Where Hyperscalers Win

**Bursty experimentation.** Early-stage AI development involves rapid iteration: spinning up a cluster of 64 GPUs for a day, running a training job, tearing it down. Hyperscalers excel at this because you pay only for what you use, and the lead time is minutes, not months.

**Access to frontier hardware.** Hyperscalers often have early access to the latest GPU architectures (NVIDIA GB200 NVL72 racks, for example) and offer managed services (SageMaker, Vertex AI, Azure ML) that abstract away infrastructure complexity. If your team lacks deep infrastructure expertise, these managed services accelerate time-to-value.

**Massive-scale pre-training.** Pre-training a foundation model from scratch---an endeavor requiring thousands of GPUs for weeks or months---is a workload few enterprises will run on-premises. The capital investment, power infrastructure, and cooling requirements are prohibitive for all but the largest organizations. This is where hyperscalers offer genuine, differentiated value.

**Global inference distribution.** If you need to serve inference from 20 regions worldwide with sub-50ms latency, a hyperscaler's global footprint is difficult to replicate with private infrastructure. Edge inference at CDN scale is a hyperscaler strength.

### 9.8.3 The Hybrid Strategy

The most effective enterprises I work with adopt a hybrid model:

- **Develop and experiment in the cloud.** Data scientists prototype in hyperscaler notebooks and managed training services, using synthetic or anonymized datasets. The agility is unmatched.
- **Train on-premises when data gravity demands it.** Once a model architecture is validated, production training on proprietary data moves to the VCF AI Compute Domain, where the data already lives.
- **Serve inference on-premises for regulated and latency-sensitive workloads.** Production inference for internal applications, customer-facing APIs with strict SLAs, and sovereignty-constrained use cases runs on VCF.
- **Serve inference in the cloud for global distribution.** Consumer-facing applications that need worldwide low-latency access use hyperscaler inference endpoints, often running distilled or smaller versions of the on-premises model.

### 9.8.4 Decision Framework

When a customer asks me, "Should this AI workload run on VCF or in a hyperscaler?" I walk through five questions:

1. **Where does the data live, and how large is it?** If the training data is on-premises and exceeds 10 TB, the data-gravity argument favors VCF.
2. **What are the regulatory constraints?** If the data is subject to residency requirements, the answer is almost always on-premises.
3. **Is the workload steady-state or bursty?** Continuous workloads favor VCF economics; bursty workloads favor hyperscaler pay-per-use.
4. **What is the latency requirement?** Sub-10ms inference latency colocated with the application tier points to on-premises.
5. **What is the three-year budget?** Model the TCO honestly, including egress, GPU instance hours, licensing, power, cooling, and staff. The math usually makes the answer clear.

No single platform wins every scenario. The art is in placing each workload where it runs best---and VCF 9's Private AI Services ensure that "on-premises" is no longer synonymous with "compromise."

---

## 9.9 Summary

AI workloads are not a passing trend that infrastructure teams can defer to the next budget cycle. They are here, they are growing, and they impose demands on compute, storage, and networking that require deliberate architectural decisions.

VMware Cloud Foundation 9 meets these demands head-on. GPU-enabled Workload Domains with NVIDIA Blackwell accelerators and BlueField-3 DPUs provide the raw horsepower. vSAN ESA delivers the all-NVMe storage throughput that training and inference demand. NSX Enhanced Data Path and DPU offload ensure that network latency does not become the bottleneck. vSphere Kubernetes Service brings the cloud-native ML ecosystem---Kubeflow, Ray, Triton, KServe, Argo CD---into the governed, observable, enterprise-grade environment that operations teams expect.

Private AI Services---GPU Monitoring, Model Store, Model Runtime, Agent Builder, Vector Database, and Data Indexing and Retrieval---transform VCF from infrastructure into a platform, giving data scientists and ML engineers the self-service capabilities they need without sacrificing governance, security, or sovereignty.

The decision between private cloud and hyperscaler is not binary. The enterprises that succeed with AI will be those that place each workload where it runs best: on-premises for data-heavy, regulated, steady-state workloads; in the cloud for bursty experimentation and global distribution. VCF 9 ensures that the on-premises option is not a concession but a competitive advantage.

In Chapter 10, we turn to disaster recovery and business continuity---because the AI models you build are only as valuable as your ability to protect them.


---

# Chapter 10: Competitive Analysis

## Where VCF 9 Stands in a Market That Refuses to Stand Still

If you have made it this far in the book, you understand what VMware Cloud Foundation 9 can do. But no technology decision happens in a vacuum. Your CIO is fielding calls from Nutanix reps. Your cloud architect just returned from AWS re:Invent with Outposts brochures. Someone on the security team forwarded an article about Azure Local. And your developers are asking why they cannot just run everything on OpenShift.

This chapter is my honest attempt to map the competitive landscape as it exists today -- not as VMware marketing would like it to exist, and not as VMware's competitors would have you believe. I have spent years as a Technical Account Manager working with enterprises that run VCF in production, and I have also helped customers evaluate every platform discussed here. Some of those evaluations ended with a VCF purchase order. Some did not. Both outcomes taught me something.

Let us start with the broader context, because the market itself has shifted in ways that matter more than any individual feature comparison.

---

## The Private Cloud Market in 2025: A Tectonic Realignment

The private cloud market reached approximately $125 billion in 2025 and shows no signs of slowing, with projections suggesting growth well beyond $200 billion by the mid-2030s. But the raw numbers obscure the more interesting story: why the market is growing, and what that growth means for platform selection.

### Cloud Repatriation Is No Longer a Fringe Narrative

For years, "cloud repatriation" was treated as contrarian punditry -- something a few vocal skeptics talked about while the rest of the industry moved workloads to AWS and Azure. That narrative has collapsed under the weight of evidence.

A 2024 Barclays CIO survey found that 83% of enterprises are planning to move workloads from public cloud back to private or on-premises infrastructure, up from 43% in 2021. IDC reports suggest that around 80% of enterprises expect to repatriate some compute or storage workloads within the next twelve months. These are not marginal numbers.

The case studies are equally compelling. 37signals, the company behind Basecamp and HEY, documented savings of roughly $1.3 to $1.5 million annually after repatriating from AWS, projecting approximately $7 million in savings over five years against an annual AWS bill that had reached $3.2 million. GEICO saw cloud costs increase 2.5 times after spending a decade migrating over 600 applications to the public cloud. Dropbox and Adobe have similarly pulled significant workloads back on-premises.

The drivers are consistent across these case studies: predictable workloads do not benefit from elastic pricing; egress charges create invisible cost traps; data sovereignty requirements increasingly conflict with multi-tenant public cloud architectures; and AI workloads -- which demand sustained GPU compute -- are proving dramatically cheaper to run on owned infrastructure. The Flexera State of the Cloud Report indicates that 42% of workloads are being moved from public clouds to private clouds or on-premises solutions to optimize costs.

None of this means public cloud is dying. Gartner predicts 90% of organizations will adopt hybrid cloud through 2027, and 92% already operate in hybrid or multi-cloud environments. The question is no longer "cloud or on-prem" but rather "which workloads belong where, and which platform gives me the operational consistency to manage both?"

That question is precisely where VCF 9, Nutanix, Azure Local, AWS Outposts, and OpenShift are all competing.

### The Key Players

The private cloud infrastructure market in 2025 is defined by five distinct approaches:

- **VMware Cloud Foundation (Broadcom)**: The integrated private cloud stack combining ESXi, vSAN, NSX, and SDDC Manager with full lifecycle automation.
- **Nutanix Cloud Platform**: Hyperconverged infrastructure with its own AHV hypervisor, Prism management, and a strong multi-cloud narrative.
- **Azure Local (Microsoft)**: Hyper-V-based HCI with Azure Arc integration, positioned as an on-premises extension of the Azure control plane.
- **AWS Outposts**: AWS-managed hardware deployed in customer data centers, extending AWS APIs and services to on-premises locations.
- **Red Hat OpenShift Platform Plus**: Kubernetes-native platform with OpenShift Virtualization for running VMs alongside containers.

Each takes a fundamentally different architectural bet on what enterprises need. Let us examine them one at a time.

---

## VCF 9 vs. Nutanix Cloud Platform: The Main Event

If VCF has a natural rival, it is Nutanix. Both target the same enterprise private cloud buyer, both offer hyperconverged infrastructure, and both have strong opinions about how a data center should be operated. The comparison is detailed enough to warrant a category-by-category breakdown.

### Hypervisor

VCF 9 runs exclusively on ESXi 9.0, the most battle-tested enterprise hypervisor in the industry. ESXi's performance characteristics are well understood, its ecosystem of third-party integrations is unmatched, and its track record in production at scale spans two decades. VMware's own benchmarks -- validated by Principled Technologies -- show vSphere supporting 1.5 times more VMs and delivering 62% more database transactions than Red Hat OpenShift Virtualization 4.16, figures that roughly apply to comparable KVM-based hypervisors.

Nutanix offers AHV, a KVM-based hypervisor that has matured considerably since its introduction. AHV is included at no additional cost with the Nutanix platform, which is a meaningful licensing advantage. The NCI 7.5 release brought improvements to API performance, upgrade workflows, and guest tools capabilities including support for up to 2,048 VMs per cluster. Nutanix also supports ESXi and Hyper-V as alternative hypervisors, giving customers flexibility that VCF simply does not offer.

**Verdict**: ESXi remains the performance leader with the deepest ecosystem, but AHV has reached enterprise-grade maturity and its inclusion in the base license is a legitimate cost advantage. Organizations with heavy VMware tool dependencies (vRealize, Horizon, third-party backup integrations) will find ESXi more practical. Greenfield deployments or those prioritizing hypervisor cost elimination should seriously evaluate AHV.

### Storage

VCF 9 includes vSAN with the Express Storage Architecture (ESA), which is a ground-up redesign of vSAN's data path. The headline feature in VCF 9 is global deduplication spanning clusters rather than individual disks, delivering approximately 46% storage reduction in typical workloads. vSAN ESA also removes the requirement for separate cache and capacity tiers, simplifying hardware selection.

Nutanix's Distributed Storage Fabric has been the company's core differentiator since its founding. It is mature, well-optimized, and benefits from years of production refinement. Nutanix's storage layer handles replication, deduplication, and compression natively, and the company has invested heavily in ransomware resilience through features like Secure Snapshot with multi-party approval controls.

One practical consideration: VCF's vSAN licensing now includes 1 TiB of storage per licensed core, and exceeding that allocation triggers additional costs through the vSAN Capacity Add-On. A standard 32-core server's included 8 TB capacity can be exhausted quickly in storage-heavy environments. Nutanix's licensing does not impose per-core storage caps in the same way, which simplifies capacity planning.

**Verdict**: Both platforms deliver enterprise-grade software-defined storage. vSAN ESA's global deduplication is a meaningful technical advancement. Nutanix's storage layer is arguably more mature and its licensing is simpler for storage-heavy workloads. For compute-heavy environments with moderate storage needs, vSAN is excellent. For storage-intensive deployments, evaluate the vSAN Capacity Add-On cost carefully against Nutanix's all-inclusive model.

### Networking

This is where VCF pulls ahead decisively. NSX in VCF 9 provides full network virtualization including microsegmentation, distributed firewalling, VPCs, the new Transit Gateway construct, load balancing, and VPN services. NSX kernel modules are now bundled directly with ESXi 9, reducing deployment complexity. The VPC lifecycle is integrated into VCF Automation workflows, giving application teams self-service network provisioning with guardrails.

Nutanix Flow provides basic microsegmentation and network policy enforcement, but it is not in the same class as NSX for advanced networking scenarios. Organizations requiring complex multi-tenant network isolation, east-west traffic inspection at scale, or sophisticated service chaining will find Nutanix's networking capabilities limiting.

**Verdict**: NSX is VCF's strongest competitive differentiator against Nutanix. If advanced networking and zero-trust microsegmentation are priorities, VCF wins this category outright. If your networking requirements are straightforward -- basic VLAN segmentation with simple firewall rules -- Nutanix Flow may be sufficient and the NSX complexity premium is unnecessary.

### Management and Operations

Nutanix Prism is widely praised for its simplicity. The single-pane-of-glass experience, one-click upgrades, and intuitive dashboards represent what many administrators wish vCenter looked like. Prism Central provides multi-cluster management, capacity planning, and operational analytics in an interface that requires minimal training.

VCF's management story is more complex. SDDC Manager handles lifecycle operations, vCenter manages compute, NSX Manager handles networking, and Aria (formerly vRealize) provides monitoring, automation, and operations analytics. VCF 9 has made progress in streamlining Day 2 operations -- certificate management, credential rotation, and patching are now more automated through SDDC Manager -- but the multi-console reality persists. The platform requires operators who understand the relationships between these components.

**Verdict**: Nutanix wins on operational simplicity. VCF wins on operational depth. Small teams with limited VMware expertise will be more productive with Nutanix. Larger organizations with established VMware competency will leverage VCF's deeper management capabilities. This is a genuine trade-off, not a clear win for either side.

### Kubernetes and Modern Applications

VCF 9 includes Tanzu Kubernetes Grid for running containerized workloads alongside traditional VMs. The integration is functional but not best-in-class -- developers who have used native Kubernetes or managed Kubernetes services often find Tanzu's approach opinionated in ways that create friction.

Nutanix Kubernetes Engine (NKE) similarly provides Kubernetes cluster provisioning and management. Neither platform competes with dedicated Kubernetes platforms like OpenShift or Rancher for container-native development workflows. Both are adequate for running containerized applications in a VM-centric infrastructure but neither is where a platform engineering team would choose to build a developer platform from scratch.

**Verdict**: Draw. Both provide functional Kubernetes integration. Neither is the primary reason to choose the platform. Organizations with serious container-native strategies will likely run a dedicated Kubernetes platform alongside either VCF or Nutanix.

### Migration Path

For organizations currently running VMware vSphere, VCF 9 represents a natural -- if not always painless -- upgrade path. SDDC Manager provides guided brownfield import workflows, though ESXi 9.0's hardware deprecation of older storage controllers and network adapters means that infrastructure modernization may be required alongside the software migration.

Nutanix has invested heavily in making VMware-to-Nutanix migration straightforward. The company supports repurposing popular vSAN ReadyNode hardware configurations, reducing the hardware investment required for migration. Nutanix Move provides automated VM migration tooling. The ability to run ESXi on Nutanix hardware during a transition period -- before converting to AHV -- provides a lower-risk migration approach.

**Verdict**: VCF has the natural advantage for existing VMware customers, but Nutanix has done excellent work reducing migration friction. The real question is whether Broadcom's licensing changes (discussed later in this chapter) have created sufficient financial motivation to justify the migration effort.

### Verdict by Use Case

| Use Case | Recommended Platform |
|---|---|
| Heavy NSX/microsegmentation requirements | VCF |
| Storage-intensive workloads with unpredictable growth | Nutanix |
| Existing deep VMware ecosystem investment | VCF |
| Small infrastructure team, need simplicity | Nutanix |
| Multi-hypervisor flexibility requirement | Nutanix |
| Regulated industries requiring zero-trust networking | VCF |
| Greenfield deployment with cost sensitivity | Evaluate both; Nutanix often wins on TCO |
| Large enterprise with established VMware skills | VCF, but validate licensing costs carefully |

---

## VCF 9 vs. Azure Local: The Sovereignty Question

Azure Local (formerly Azure Stack HCI) represents Microsoft's bet that enterprises want the Azure experience on-premises. It combines Hyper-V-based compute, Storage Spaces Direct for software-defined storage, and Azure Arc for unified management -- all governed by the Azure control plane.

### The Sovereignty Consideration

For years, the knock against Azure Local was straightforward: it required a persistent connection to the Azure cloud control plane, making it unsuitable for air-gapped environments, classified networks, or organizations with strict data sovereignty mandates. This was a genuine disqualifier for a significant segment of the market.

Microsoft has since addressed this with Azure Local Disconnected, which runs the control plane as a virtual appliance on customer hardware, eliminating the dependency on continuous cloud connectivity. As of early 2026, this capability has reached general availability, supporting deployment scenarios where only registration data is exchanged during initial setup and license renewal.

This is a meaningful development, and intellectual honesty requires acknowledging it. Azure Local Disconnected narrows -- though does not eliminate -- VCF's sovereignty advantage. VCF's control plane has always been fully on-premises with zero external dependencies, and that architectural simplicity still matters for organizations that are uncomfortable with any Microsoft control plane involvement, even a locally hosted one.

### Windows Workload Fit

Azure Local is genuinely excellent for Windows-centric workloads. The integration with Active Directory, Azure AD (Entra ID), Windows Server, and the broader Microsoft ecosystem is seamless in ways that VCF cannot match. Organizations running primarily Windows Server VMs, SQL Server, and Microsoft application stacks will find Azure Local's operational model more natural than VCF's.

VCF, by contrast, is hypervisor-agnostic at the workload level -- it runs Windows and Linux VMs with equal capability -- but it does not offer the native Microsoft management integrations that Azure Local provides.

### Maturity and Scale Concerns

Gartner's late 2025 analysis noted that customers tend to be concerned about Azure Local's product maturity and scale, with enterprises showing inclination toward small-scale lift-and-shift deployments rather than full-scale private cloud implementations. VCF's track record at scale -- managing thousands of hosts across dozens of clusters in production -- remains a meaningful differentiator for large environments.

Azure Local also takes a more hardware-agnostic approach than VCF 9, which can be both an advantage (more hardware flexibility, ability to reuse existing infrastructure) and a disadvantage (less validated, less predictable performance characteristics).

### Verdict

Azure Local is the right choice for organizations deeply invested in the Microsoft ecosystem that want a consistent Azure operational experience across cloud and on-premises, particularly for small-to-medium deployments. VCF is the stronger choice for large-scale private cloud deployments, heterogeneous workload environments, organizations requiring NSX-level networking sophistication, or those that need guaranteed sovereignty without any cloud provider control plane involvement. The "sovereignty disqualifier" that defined earlier versions of Azure Stack HCI has been partially addressed, but VCF's fully independent architecture remains simpler and more proven for true air-gap scenarios.

---

## VCF 9 vs. AWS Outposts: The Hybrid Cloud Extension Model

AWS Outposts takes a fundamentally different approach from every other platform in this chapter. Rather than providing an independent private cloud, Outposts extends AWS services into your data center. It is AWS in your rack, managed by AWS, running AWS APIs, billing through AWS.

### Not Air-Gappable -- By Design

This is not a bug; it is the architecture. AWS Outposts requires a persistent connection back to the parent AWS Region via the Service Link. During a network disconnection, existing EC2 instances and EBS volumes continue running, but you cannot start, stop, or terminate instances. Mutating API calls fail. CloudWatch metrics stop flowing. EBS-backed pods in EKS on Outposts cannot be created, updated, or scaled. After seven days of disconnection, metrics and logs may be lost entirely. S3 on Outposts tolerates disconnection for up to twelve hours for object operations, but bucket creation and policy changes require connectivity.

For organizations requiring air-gapped operations, classified computing, or truly disconnected edge deployments, Outposts is simply not an option. VCF operates with complete independence from any external control plane, making it the clear choice for these scenarios.

### Hardware Lock-In

Outposts hardware is owned and managed by AWS. You do not choose your server vendor, storage configuration, or network architecture -- AWS specifies and installs the infrastructure. This simplifies operations (AWS handles hardware maintenance and replacement) but eliminates the procurement flexibility that many enterprises value. You cannot repurpose Outposts hardware for other workloads, and capacity changes require AWS engagement.

VCF, by contrast, runs on a broad range of server hardware from Dell, HPE, Lenovo, and other OEMs on the VMware Compatibility Guide, giving organizations freedom to negotiate pricing, leverage existing vendor relationships, and standardize on their preferred hardware platform.

### Cost at Scale

Outposts pricing is consumption-based, and while this is attractive for small deployments or workloads with uncertain longevity, the economics shift at scale. AWS Outposts has higher setup costs compared to VCF, and the ongoing per-instance pricing means that large, dense deployments become progressively more expensive relative to owned infrastructure with VCF licensing.

The crossover point depends heavily on utilization rates and workload density, but organizations running hundreds or thousands of VMs on Outposts will typically find that VCF on owned hardware delivers better three-year TCO.

### Verdict

AWS Outposts is purpose-built for organizations that want AWS API consistency between their cloud and on-premises environments, are comfortable with AWS managing their on-premises hardware, and do not require disconnected operations. VCF is the better choice for organizations that need infrastructure independence, hardware procurement flexibility, air-gap capability, or better economics at scale. The two platforms target genuinely different use cases, and there is less head-to-head competition between them than marketing narratives suggest.

---

## VCF 9 vs. Red Hat OpenShift Platform Plus: Different Battles, Same Data Center

The VCF-versus-OpenShift comparison is the most nuanced in this chapter because the platforms are not truly competing for the same workloads -- yet they increasingly coexist in the same data centers, and budget allocation forces them into competition.

### Container-First vs. VM-First

OpenShift is a Kubernetes-native platform. Its fundamental unit of deployment is a container, and everything -- including VMs via OpenShift Virtualization (based on KubeVirt) -- is managed through Kubernetes primitives. This is an architectural strength for cloud-native applications and a genuine challenge for traditional VM workloads.

VCF is a VM-first platform. Its fundamental unit of deployment is a virtual machine running on ESXi, with Kubernetes (via Tanzu) layered on top. This is the inverse architectural bet: strong for traditional enterprise workloads with containers as an add-on capability.

OpenShift Virtualization has made significant progress -- Red Hat even released OpenShift Virtualization Engine as a standalone VM-only product at a lower price point -- but VMware's benchmarks show vSphere supporting significantly more VMs with higher transaction throughput than OpenShift Virtualization. For organizations running thousands of traditional VMs, VCF's performance and density advantages are material.

### The "Both in the Same Data Center" Pattern

In practice, many large enterprises end up running both platforms. VCF handles the estate of traditional VMs -- database servers, legacy applications, Windows workloads, appliances -- while OpenShift runs the container-native microservices, CI/CD pipelines, and modern application stacks.

This is not an architectural failure; it is a pragmatic acknowledgment that no single platform excels at everything. The cost of this pattern is operational complexity -- two infrastructure teams, two skill sets, two maintenance windows -- but many organizations find this preferable to forcing container workloads into a VM-centric model or forcing legacy VMs into Kubernetes.

### Developer Experience

This is where OpenShift genuinely excels. Red Hat has invested heavily in developer tooling, GitOps workflows (ArgoCD/OpenShift GitOps), integrated CI/CD (OpenShift Pipelines based on Tekton), service mesh (OpenShift Service Mesh based on Istio), and a developer portal (Red Hat Developer Hub). The developer experience is modern, opinionated, and productive.

VCF's developer experience, by comparison, is an infrastructure operator's platform. Developers interact with it indirectly through Tanzu or through self-service VM provisioning via Aria Automation. The platform was designed for infrastructure teams, and it shows. This is not necessarily a deficiency -- infrastructure platforms should serve infrastructure operators -- but it means VCF will never win a developer experience comparison against OpenShift.

### Verdict

Do not choose between these platforms based on feature matrices. Choose based on your workload profile. If your estate is 80%+ traditional VMs with modest container needs, VCF is the right foundation. If your estate is 80%+ containerized microservices with a few legacy VMs, OpenShift is the right foundation. If you are somewhere in the middle, you will likely run both, and that is fine. The question becomes which platform gets the larger budget share, and that should be determined by where your workload growth is heading, not where your workload estate currently sits.

---

## TCO Analysis: The Numbers Behind the Narratives

Total cost of ownership comparisons are among the most manipulated artifacts in enterprise IT. Every vendor has a TCO calculator that, remarkably, shows their platform winning. Here is what the data actually tells us, with appropriate caveats.

### The IDC Business Value Study

In August 2024, IDC published a study evaluating VMware Cloud Foundation's financial impact across production deployments. The headline numbers: 564% three-year ROI, a 10-month payback period, 51% TCO savings compared to traditional three-tier infrastructure with vSphere, 42% lower three-year cost of operations, and 34% lower infrastructure costs.

These numbers are real, but context matters. The study sample consisted of mostly large, enterprise-level organizations with an average employee count of 58,731 (median 34,000) and average annual revenue of $11.73 billion. These are organizations with the scale to fully leverage VCF's consolidation capabilities and the operational maturity to realize its efficiency gains. The results may not be representative for mid-market organizations with smaller deployments or less mature operations teams.

Additionally, the study was commissioned by VMware (now Broadcom). This does not invalidate the findings -- IDC maintains methodological independence -- but it means the study design naturally focuses on successful deployments. Failed or troubled VCF implementations are not represented.

### The Three-Year Model and the Crossover Point

The fundamental TCO question in private cloud versus public cloud is straightforward: at what point does the upfront capital investment in private infrastructure pay for itself against ongoing public cloud operational expenses?

For workloads with predictable, sustained utilization, the crossover point typically falls between 18 and 24 months. Before this point, public cloud's absence of capital expenditure makes it cheaper. After this point, private cloud's fixed costs and absence of consumption-based billing make it cheaper -- and the gap widens with each subsequent month.

The key variables that accelerate or delay this crossover include:

- **Utilization rate**: Private cloud economics improve dramatically above 60% average utilization. Below 40%, public cloud often remains cheaper.
- **Egress charges**: Organizations with significant data movement to the internet or between clouds can see public cloud costs balloon by 20-40% above base compute charges.
- **Storage growth**: Public cloud block and object storage costs compound over time. Private cloud storage, once provisioned, has minimal incremental cost.
- **Operational staffing**: Private cloud requires infrastructure operators. If your organization does not already have this capability, the staffing costs must be factored in.

### Hidden Hyperscaler Costs

The most common TCO mistake in public cloud estimation is comparing list-price compute against private cloud fully loaded costs. Public cloud bills include charges that have no private cloud equivalent:

- **Data egress fees**: AWS charges up to $0.09 per GB for data transfer out. An organization moving 100 TB per month pays $9,000 monthly in egress alone.
- **Cross-AZ traffic**: Traffic between availability zones within the same region incurs charges on every major cloud platform.
- **API call charges**: S3, DynamoDB, and similar services charge per API call, creating costs that are difficult to predict and optimize.
- **Premium support tiers**: AWS Enterprise Support costs 10% of monthly spend (with a $15,000 minimum). Azure Unified Support pricing is similarly structured.
- **Reserved instance complexity**: Achieving competitive pricing on public cloud requires committing to one- or three-year reserved instances, which undermines the flexibility argument.

### An Honest TCO Summary

For large enterprises with predictable workloads, mature operations teams, and a planning horizon of three or more years, VCF typically delivers lower TCO than public cloud for the same workloads. The IDC study's 51% TCO reduction is achievable under these conditions, though your specific results will depend on your utilization, staffing, and workload characteristics.

For variable workloads, short-lived projects, or organizations without infrastructure operations capability, public cloud remains the more economical choice. The private cloud TCO advantage assumes you can actually operate the infrastructure effectively.

---

## Where VCF Genuinely Wins

Having examined the competitive landscape with appropriate nuance, let me identify the areas where VCF 9 holds genuine, defensible advantages.

### Single Bill of Materials

VCF is the only platform that delivers compute virtualization, software-defined storage, network virtualization with microsegmentation, lifecycle management, and private cloud automation in a single, integrated product from a single vendor. Nutanix requires third-party networking for advanced use cases. Azure Local depends on the broader Azure ecosystem. Outposts is limited to AWS service availability. Only VCF provides the complete stack.

### NSX Depth

No competitor matches NSX's combination of distributed firewalling, microsegmentation, VPCs, Transit Gateway, load balancing, VPN, and service insertion capabilities. For organizations implementing zero-trust networking architectures, NSX remains the gold standard in private cloud networking. The VCF 9 integration of NSX kernel modules directly into ESXi eliminates the deployment complexity that was a historical criticism.

### Global Deduplication

vSAN ESA's global deduplication across clusters -- delivering approximately 46% storage reduction -- is a genuine technical achievement that no competitor has matched at the cluster-spanning level. For organizations with significant storage requirements, this translates directly to reduced hardware costs.

### Ecosystem Breadth

VMware's partner ecosystem remains the largest in enterprise infrastructure. Backup vendors (Veeam, Commvault, Cohesity), security tools (CrowdStrike, Carbon Black), monitoring platforms (Datadog, Splunk), and hardware vendors all provide VMware-optimized integrations. This ecosystem was built over two decades and cannot be replicated quickly by any competitor.

### Lifecycle Automation

SDDC Manager in VCF 9 provides unified lifecycle management for the entire stack -- ESXi, vCenter, NSX, vSAN, Aria components -- including patching, certificate rotation, credential management, and compliance validation. The ability to orchestrate coordinated upgrades across all components from a single workflow reduces the operational risk that plagues multi-vendor infrastructure stacks.

### True Sovereignty

VCF's control plane is entirely on-premises with zero external dependencies. No telemetry is required. No cloud connection is needed for any operational function. No external entity has access to the management plane. For government, defense, intelligence, and regulated industry deployments, this architectural independence is a non-negotiable requirement that VCF fulfills without caveats.

---

## Where VCF Genuinely Struggles

Intellectual honesty demands equal scrutiny of VCF's weaknesses. These are the areas where I see customers struggle, where competitors have legitimate advantages, and where Broadcom needs to invest.

### Broadcom Pricing Concerns

This is the elephant in the room, and no amount of feature comparison changes the reality: Broadcom's licensing changes have caused significant pain across the VMware customer base. The shift from perpetual to subscription-only licensing, the consolidation from approximately 168 product bundles to a handful of mega-bundles, and the increase in minimum core requirements from 16 to 72 cores per CPU have resulted in reported price increases ranging from 150% to over 1,200% for some customers.

A UK university reported its annual support cost rising from 40,000 GBP to 500,000 GBP under the new VCF bundle mandate. European customers have reported increases of 800% to 1,500%. Small businesses face 350% to 450% increases due to the 72-core minimum disproportionately penalizing smaller deployments.

I will not pretend these numbers do not matter. For many organizations, the licensing cost increase is the single largest factor driving competitive evaluations. Broadcom's financial strategy has been a Wall Street success -- the VMware segment's profitability has increased substantially -- but customer sentiment tells a different story. Industry reports indicate that Gartner expects 35% of VMware workloads to migrate to alternative platforms by 2028, and the licensing changes are the primary catalyst.

As a TAM, I work with customers to optimize their licensing through right-sizing, core allocation strategies, and bundle consolidation. These optimizations are real and can significantly reduce the impact. But I would be dishonest to suggest that every customer who stays on VCF does so because the technology won on merit. Some stay because migration costs exceed the licensing increase. That is lock-in, not loyalty.

### Complexity

VCF is a powerful platform, but it is not a simple one. A fully deployed VCF environment includes ESXi, vCenter, NSX Manager, SDDC Manager, vSAN, Aria Suite components, and potentially Tanzu -- each with its own management interface, upgrade cadence, and operational requirements. VCF 9 has made meaningful progress in unifying lifecycle operations through SDDC Manager, but the underlying architectural complexity remains.

Nutanix Prism is genuinely simpler to operate. This is not a controversial statement; it is a consistent finding across analyst reports, customer feedback, and my own experience. Organizations with small infrastructure teams will spend more time managing VCF than managing an equivalent Nutanix deployment.

### Hardware Requirements

ESXi 9.0 has deprecated significant numbers of older storage controllers, network adapters, and server platforms. Organizations planning a VCF 9 deployment may discover that their existing hardware is no longer on the VMware Compatibility Guide, requiring hardware refresh alongside the software upgrade. This is a real cost that must be factored into migration planning.

By comparison, Nutanix supports repurposing many popular vSAN ReadyNode configurations, and Azure Local (via Hyper-V) takes a more hardware-agnostic approach that allows incremental adoption on existing infrastructure.

### Developer Experience

VCF was built for infrastructure operators, and its developer experience reflects this lineage. Developers do not interact with VCF directly; they interact with the VMs or Kubernetes clusters it provisions. The self-service capabilities through Aria Automation are functional but not what a modern developer expects compared to a Kubernetes-native platform like OpenShift or even a managed cloud service.

This gap matters increasingly as organizations adopt platform engineering practices. The infrastructure team using VCF may be highly productive, but the developer team consuming its output may feel constrained by provisioning workflows, approval gates, and abstraction layers that feel heavyweight compared to a "git push to deploy" model.

### Post-Acquisition Uncertainty

The Broadcom acquisition has created genuine uncertainty in the VMware ecosystem. The reduction of authorized VMware Cloud Service Providers from over 4,500 globally to a much smaller number -- including only 12 Pinnacle partners and 300 Premier partners in the US -- has disrupted partner relationships. Late renewal penalties of 20-25% have strained customer goodwill. The consolidation of the product portfolio, while arguably simplifying the offering, has forced customers to pay for capabilities they may not need.

This uncertainty is a business risk, not a technical one. VCF 9 is arguably the best version of VMware Cloud Foundation ever released. But technology decisions are not made purely on technical merit -- they are made in the context of vendor relationships, pricing predictability, and confidence in the platform's long-term direction. Broadcom has work to do in rebuilding the trust that the acquisition disrupted.

---

## Making the Decision: A Framework

Competitive analysis is only useful if it leads to better decisions. Here is the framework I use with customers when evaluating VCF against alternatives:

1. **Start with the workload profile.** What percentage of your estate is traditional VMs versus containers? What are the networking requirements? What are the storage growth projections? The workload drives the platform, not the reverse.

2. **Assess your team's skills.** VCF rewards deep VMware expertise. If your team has it, VCF's complexity becomes capability. If your team does not, the learning curve is real and the operational risk during transition is meaningful.

3. **Model the true TCO.** Include licensing, hardware (including any required refresh for VCF 9 compatibility), staffing, training, migration costs, and the opportunity cost of the transition period. Compare this against a realistic public cloud estimate that includes egress, support, and reserved instance commitments.

4. **Evaluate sovereignty requirements.** If you need true air-gap capability with zero external dependencies, VCF is the strongest option. If you can tolerate a cloud-connected control plane, the field widens.

5. **Consider the three-year trajectory.** Where is your organization heading? If you are investing in cloud-native development and platform engineering, OpenShift or a similar Kubernetes-native platform may serve your future better than VCF, even if VCF serves your present better. If you are consolidating and optimizing a large VM estate, VCF's strengths align with your direction.

6. **Negotiate.** Regardless of which platform you choose, your competitive evaluation is your leverage. Broadcom, Nutanix, Microsoft, and Red Hat all have pricing flexibility that only materializes when they know you are seriously considering alternatives.

---

## Closing Thoughts

The private cloud market in 2025 is more competitive than it has been in years, and that competition benefits every customer. VCF 9 is a technically excellent platform that addresses many of the criticisms leveled at earlier versions -- the lifecycle automation is stronger, NSX integration is tighter, vSAN ESA's global deduplication is innovative, and the overall architecture is more cohesive.

But VCF 9 does not exist in isolation. It exists in a market where Nutanix offers comparable functionality with genuinely simpler operations and more predictable pricing. Where Azure Local has addressed its sovereignty limitations and provides unmatched Windows ecosystem integration. Where OpenShift offers a developer experience that VCF cannot match. And where Broadcom's own pricing decisions have given every competitor a recruiting tool they never had before.

The right platform is the one that fits your workloads, your team, your budget, and your trajectory. For many enterprises, that platform remains VCF -- but in 2025, "many" is no longer "most," and earning the business requires demonstrating value, not assuming loyalty.

In the next chapter, we will move from comparative analysis to practical implementation, examining the reference architectures and deployment patterns that make VCF 9 succeed in production.


---

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


---

# Chapter 12: Certifications --- Your Career Accelerator in the VCF Era

## Why VCF Certification Matters

Let me be direct with you. In my role as a Technical Account Manager at VMware by Broadcom, I speak with infrastructure teams every single week who are navigating one of the most significant transitions in the history of enterprise virtualization. VMware Cloud Foundation 9 is not an incremental upgrade. It is a platform re-architecture that redefines how private clouds are built, operated, and consumed. And the professionals who can prove they understand this platform --- with a credential backed by the vendor itself --- are the ones getting hired, promoted, and trusted with the most consequential projects.

I have watched the market shift in real time. Before the Broadcom acquisition, a VCP-DCV was the gold standard for vSphere administrators. That certification still carries weight, but the industry has moved. Enterprises are consolidating on VCF as their private cloud platform, and the demand signal is unmistakable: hiring managers want VCF-certified professionals. Job postings that once asked for "VCP-DCV preferred" now read "VCP-VCF required." Consulting firms are staffing entire practices around VCF migration work. Managed service providers are requiring their engineers to hold VCF credentials before they can touch customer environments.

From a TAM perspective, I can tell you that certified customers deploy faster, open fewer support tickets, and get more value out of their VCF investment. When I work with a team that holds VCF certifications, the conversations are different. We spend less time explaining foundational concepts and more time solving real business problems. That efficiency benefits everyone.

The career impact is tangible. VCF-certified professionals command a salary premium that ranges from fifteen to twenty-five percent over their non-certified peers in comparable roles. More importantly, the certification opens doors to roles that simply do not exist for uncertified candidates: cloud architect positions at Fortune 500 companies, senior infrastructure roles at hyperscale service providers, and principal engineer positions at VMware partners.

There is also a practical benefit that many candidates overlook. Earning a VCP-VCF certification now unlocks complimentary home lab licenses: VMware Cloud Foundation with 128 cores, vDefend Firewall with 128 cores, and Avi Load Balancer with 12 service units, all valid for three years. That is not a trivial perk. It gives you a production-grade lab environment to continue learning, testing, and building expertise long after you pass the exam.

The bottom line: if you work with VMware infrastructure in any capacity, VCF certification is no longer optional. It is the baseline expectation for a modern virtualization professional.

---

## The Three Certification Tracks: An Overview

Broadcom has restructured the VMware certification program around three distinct VCF tracks, each targeting a different role and skill set. Understanding which track aligns with your career goals is the first decision you need to make.

### VCP-VCF Administrator

The Administrator track validates your ability to deploy, configure, manage, and operate a VMware Cloud Foundation environment. This is the broadest certification and the one most infrastructure professionals should pursue first. It covers the full stack: vSphere, vSAN, NSX, VCF Automation, VCF Operations, and the platform lifecycle management capabilities that tie everything together. The current exam is the **2V0-17.25**, aligned with VCF 9.0.

### VCP-VCF Support

The Support track is purpose-built for professionals who spend their days in the trenches of troubleshooting and incident resolution. While the Administrator exam tests whether you can build and run a VCF environment, the Support exam tests whether you can fix it when something breaks. The current exam is the **2V0-15.25**, also aligned with VCF 9.0. This track is particularly relevant for support engineers, operations center staff, and managed service provider teams.

### VCP-VCF Architect

The Architect track sits at the top of the VCP pyramid. It validates your ability to design VCF environments from the ground up, making defensible architectural decisions that account for performance, availability, security, compliance, and cost. The exam code has transitioned from the **2V0-13.24** (VCF 5.2) to the **2V0-13.25** (VCF 9.0). This track is the gateway to the VCAP and ultimately the prestigious VCDX designation, which Broadcom recently expanded to welcome administrators and support specialists alongside architects.

All three certifications share common characteristics: they are proctored through Pearson VUE, cost $250 USD at full price, use a scaled scoring model with a passing threshold of 300, and carry a 36-month validity period before renewal is required.

---

## VCP-VCF Administrator: 2V0-17.25 Full Breakdown

### Exam Logistics

| Detail | Value |
|---|---|
| Exam Code | 2V0-17.25 |
| Full Title | VMware Cloud Foundation 9.0 Administrator |
| Certification Earned | VCP-VCF Administrator |
| Number of Questions | 70 |
| Duration | 135 minutes |
| Passing Score | 300 (scaled) |
| Question Format | Single-choice, multiple-choice, drag-and-drop ordering |
| Delivery | Pearson VUE (test center or online proctored) |
| Price | $250 USD (discounts available through VMUG Advantage) |
| Language | English |
| Prerequisites | Recommended 1 year IT experience, 6 months VCF hands-on |
| Replaces | 2V0-11.25 (VCF 5.2 Administrator) |

### Complete Blueprint Topic Breakdown

The 2V0-17.25 exam blueprint is organized into five sections. Not all sections carry equal weight, and understanding the distribution is critical to allocating your study time effectively.

**Section 1: IT Architectures, Technologies, and Standards**

This section establishes your foundational knowledge. It may seem basic, but do not underestimate it. Questions in this section test whether you understand the broader context in which VCF operates.

- Identify and differentiate types of cloud architectures (private, public, hybrid, multi-cloud)
- Explain virtualization concepts at the compute, storage, and network layers
- Describe software-defined data center (SDDC) principles and their relationship to VCF
- Identify industry standards and frameworks relevant to cloud infrastructure (NIST, ISO, ITIL)
- Explain the role of APIs and automation in modern infrastructure management

**Section 2: VMware Cloud Foundation Fundamentals**

This is where VCF-specific knowledge begins. This section tests your understanding of the platform architecture, component relationships, and the new constructs introduced in VCF 9.0.

- Describe the VCF architecture including the relationship between Fleet, Instance, and Domain
- Explain the role of SDDC Manager in lifecycle management
- Identify VCF components: vCenter Server, ESXi, vSAN, NSX, VCF Automation, VCF Operations
- Describe the VCF Identity Broker (vIDB) and its role in centralized authentication
- Explain VCF Single Sign-On models (embedded vs. appliance-based Identity Broker)
- Describe the VCF Fleet Management Layer and its relationship to Instance Management Planes
- Differentiate between Fleet-level services and Instance-level services
- Identify the role of VCF Automation in self-service consumption and organization constructs
- Explain VCF Operations for monitoring, capacity management, and compliance
- Describe Virtual Private Clouds (VPCs) as a consumption model within VCF
- Identify supported deployment modes: Simple (single-node) and High Availability (three-node)

**Section 3: Plan and Design the VMware by Broadcom Solution**

This section tests your ability to translate requirements into VCF design decisions. While it overlaps somewhat with the Architect exam, the Administrator exam focuses on tactical planning rather than strategic architecture.

- Determine hardware requirements for VCF deployment (compute, storage, networking)
- Plan network topology for management, vMotion, vSAN, and workload traffic
- Design host profiles and cluster configurations for workload domains
- Plan DNS, NTP, and certificate infrastructure for VCF deployment
- Determine storage policies aligned with workload requirements
- Plan NSX network segments, Tier-0 and Tier-1 gateway configurations
- Design identity and access management strategy using VCF Identity Broker
- Plan for multi-site and stretched cluster topologies

**Section 4: Deploy, Configure, and Operate VMware Cloud Foundation**

This is the largest and most heavily weighted section. It covers the day-to-day operational activities that define the VCF administrator role.

- Deploy VCF using Cloud Builder (greenfield) and convert existing environments (brownfield)
- Configure and manage workload domains (management and VI workload domains)
- Add and remove ESXi hosts from clusters and workload domains
- Configure vSAN storage policies and monitor vSAN health
- Deploy and configure NSX for network virtualization and micro-segmentation
- Configure VCF Automation including the Provider Portal and Organization Management
- Deploy and configure VCF Operations for monitoring, alerting, and dashboards
- Manage certificates using SDDC Manager certificate management workflows
- Perform VCF lifecycle management: patching, upgrading, and bundle management
- Configure backup and restore for VCF management components
- Manage VCF licensing and compliance
- Configure and manage vSphere Supervisor for Kubernetes workloads
- Implement network policies and security groups using NSX
- Monitor and manage VCF Fleet health using the Fleet Management Console
- Configure content libraries and IaaS policies for tenant consumption

**Section 5: Troubleshoot and Optimize the VMware by Broadcom Solution**

This section tests your ability to diagnose and resolve common issues. While less heavily weighted than Section 4, it consistently produces the most difficult questions on the exam.

- Troubleshoot VCF deployment failures (Cloud Builder errors, network validation failures)
- Diagnose and resolve lifecycle management issues (upgrade failures, bundle download errors)
- Troubleshoot ESXi host connectivity and resource contention
- Resolve vSAN health alerts and storage performance issues
- Diagnose NSX connectivity problems (tunnel failures, routing issues, firewall rule conflicts)
- Troubleshoot VCF Automation and VCF Operations integration issues
- Interpret SDDC Manager logs and SoS (Support and Serviceability) utility output
- Optimize compute resource allocation and cluster performance
- Troubleshoot certificate-related failures across VCF components

### VCF 9 Terminology Traps for VCF 5.x Candidates

If you earned a VCF 5.x certification and are now preparing for the VCF 9.0 exam, pay close attention. The terminology has changed significantly, and using outdated terms on the exam will cost you points.

| VCF 5.x Term | VCF 9.0 Term | Why It Matters |
|---|---|---|
| Aria Automation | VCF Automation | Branding change; all "Aria" references are retired |
| Aria Operations | VCF Operations | Same platform, new name; exam uses VCF Operations exclusively |
| Aria Suite Lifecycle | Retired concept | Lifecycle management is now integrated into SDDC Manager |
| VIDM (VMware Identity Manager) | VCF Identity Broker (vIDB) | Completely different architecture; vIDB is not a rebrand |
| Management Domain | Management Domain | Term is unchanged, but Fleet context is new |
| N/A | Fleet | New construct: governance scope for shared services |
| N/A | Instance | New construct: discrete VI footprint with own management stack |
| N/A | Virtual Private Cloud (VPC) | New consumption model within VCF 9.0 |
| Workload Domain | Workload Domain | Term persists, but now exists within an Instance context |
| Cloud Builder | Cloud Builder | Same tool, but now supports Fleet-aware deployment |
| SDDC Manager | SDDC Manager | Same role, but now Instance-scoped rather than global |

The single biggest trap I see candidates fall into is conflating Fleet and Instance. A Fleet is a governance and lifecycle scope --- it is where shared services like VCF Automation and VCF Operations live. An Instance is where workloads run, and each Instance maintains its own SDDC Manager, vCenter, and NSX control planes. When the exam asks about "Fleet-level services," it is asking about VCF Automation, VCF Operations, and VCF Identity Broker. When it asks about "Instance-level management," it is asking about SDDC Manager, vCenter, and NSX within a specific Instance.

---

## VCP-VCF Support: 2V0-15.25

### Exam Logistics

| Detail | Value |
|---|---|
| Exam Code | 2V0-15.25 |
| Full Title | VMware Cloud Foundation 9.0 Support |
| Certification Earned | VCP-VCF Support |
| Number of Questions | 60 |
| Duration | 135 minutes |
| Passing Score | 300 (scaled) |
| Price | $250 USD |
| Language | English |
| Prerequisites | Recommended 2 years IT experience, 1 year VCF support experience |

### Unique Troubleshooting Objectives

The Support exam shares some foundational content with the Administrator exam, but it diverges sharply in its emphasis on troubleshooting methodology. Where the Administrator exam asks "How do you configure this?", the Support exam asks "What do you do when this breaks?"

The exam blueprint is organized around the following troubleshooting domains:

**Troubleshoot VCF Deployment and Fleet Management**
- Diagnose Cloud Builder deployment failures and identify root causes
- Troubleshoot Fleet registration and Instance onboarding issues
- Resolve VCF Identity Broker deployment and configuration failures
- Diagnose SSO integration issues across Fleet and Instance boundaries

**Troubleshoot VCF Upgrade and Conversion**
- Identify root causes for upgrade bundle download and staging failures
- Resolve pre-check validation failures during VCF upgrades
- Troubleshoot brownfield conversion issues when bringing existing environments into VCF
- Diagnose compatibility matrix conflicts during lifecycle management operations

**Troubleshoot VCF Compute**
- Diagnose host connectivity failures, including VIB installation issues and boot failures
- Resolve resource contention scenarios (CPU, memory, network I/O)
- Troubleshoot VM placement failures related to DRS rules, affinity groups, and resource pools
- Identify and resolve vSphere HA and vSphere DRS misconfigurations

**Troubleshoot VCF Storage**
- Resolve vSAN health alerts including disk group failures and rebalancing issues
- Diagnose storage policy compliance violations
- Troubleshoot vSAN stretched cluster split-brain scenarios
- Identify performance bottlenecks in vSAN and NFS/VMFS datastores

**Troubleshoot VCF Networking**
- Diagnose NSX tunnel (TEP) connectivity failures
- Resolve Tier-0 and Tier-1 gateway routing issues
- Troubleshoot distributed firewall rule conflicts and rule ordering problems
- Identify MTU mismatches and VLAN configuration errors across the VCF network fabric

**Fleet Management and Licensing**
- Resolve licensing compliance issues and license allocation across VCF Fleets
- Troubleshoot capacity reporting discrepancies
- Diagnose Fleet management console connectivity and health issues

The Support exam rewards candidates who think like support engineers: methodical, evidence-driven, and focused on root cause rather than symptoms. When you encounter a troubleshooting question, follow this mental model: (1) identify the symptom, (2) determine the affected layer (compute, storage, network, management), (3) identify the most likely root cause from the answer choices, (4) verify that the proposed remediation addresses the root cause and not just the symptom.

---

## VCP-VCF Architect: 2V0-13.24 / 2V0-13.25

### Exam Logistics

| Detail | Value |
|---|---|
| Exam Code | 2V0-13.25 (current); 2V0-13.24 (VCF 5.2, retired) |
| Full Title | VMware Cloud Foundation 9.0 Architect |
| Certification Earned | VCP-VCF Architect |
| Number of Questions | 60 |
| Duration | 135 minutes |
| Passing Score | 300 (scaled) |
| Price | $250 USD |
| Language | English |
| Prerequisites | Recommended 3+ years design/architecture experience |

### The Design Mindset

The Architect exam is fundamentally different from the Administrator and Support exams. It does not test whether you can click the right button or interpret the right log file. It tests whether you can make the right decision when there is no single right answer --- only trade-offs.

Every question on the Architect exam is a design question, even when it does not look like one. A question about NSX Tier-0 gateway sizing is not asking you to recite the maximum number of uplinks. It is asking you to evaluate throughput requirements, redundancy models, and failure domain isolation to determine the appropriate configuration for a given scenario.

### Blueprint Emphasis Areas

**VCF Architecture Design**
- Design Fleet topologies: single-site, dual-site, and multi-region deployments
- Determine the appropriate number of Instances and workload domains based on organizational, compliance, and performance requirements
- Design SSO boundaries and VCF Identity Broker deployment models
- Architect management domain sizing for VCF management components
- Design for high availability across all management components

**Compute Design**
- Design cluster configurations based on workload profiles (general purpose, high performance, GPU-accelerated)
- Determine host sizing and cluster scaling strategies
- Design DRS and HA policies aligned with application SLAs
- Architect vSphere Supervisor configurations for Kubernetes workloads

**Storage Design**
- Design vSAN storage policies aligned with workload IOPS, throughput, and capacity requirements
- Architect vSAN fault domain and stretched cluster configurations
- Determine storage tiering strategies using vSAN storage policies
- Design for data protection and recovery using native VCF capabilities

**Network Design**
- Design NSX network topologies including Tier-0, Tier-1, and segment configurations
- Architect network security using distributed firewalls, gateway firewalls, and micro-segmentation
- Design VPC configurations for tenant isolation
- Plan physical network underlay requirements for VCF overlay networking
- Design load balancing using Avi Load Balancer (NSX Advanced Load Balancer)

**Automation and Operations Design**
- Design VCF Automation deployment for multi-tenant consumption
- Architect VCF Operations monitoring, alerting, and capacity management strategies
- Design content library and IaaS policy structures for organizational governance
- Plan lifecycle management strategies for patches, upgrades, and compliance

### The Design Decision Framework

On the Architect exam, you will frequently encounter questions where two or more answers seem correct. In these situations, apply this framework:

1. **Requirements First**: What does the scenario actually require? Not what you think it should require, but what the question states.
2. **Constraints Second**: What limitations does the scenario impose? Budget, hardware, compliance, timeline --- these eliminate options.
3. **VMware Best Practice Third**: When requirements and constraints leave multiple viable options, default to the VMware Validated Design and VCF Design Guide recommendations.
4. **Simplicity Wins**: When all else is equal, the simpler design is the correct answer. VMware exams consistently favor designs that minimize operational complexity.

---

## Study Resources

### Free Resources

**Official Exam Blueprints and Guides**

Your study journey starts here. Every question on the exam maps to a blueprint objective. If you cannot articulate what each objective means and how it manifests in a VCF environment, you are not ready.

- VCP-VCF Administrator Exam Guide: Available at broadcom.com under Education and Certification
- VCP-VCF Support Exam Guide: Published at docs.broadcom.com/doc/vmware-cloud-foundation-support-exam-guide
- VCP-VCF Architect Exam Guide: Published at docs.broadcom.com/doc/vmware-cloud-foundation-architect-exam-guide

**VMware Hands-on Labs (HOL)**

The Hands-on Labs are the single most valuable free resource for exam preparation. They give you guided, hands-on experience with VCF 9.0 in a fully provisioned environment --- no hardware required, no cost, no time limit beyond the lab session itself.

- **HOL-2610-01-VCF-L: What's New in VMware Cloud Foundation 9.0 --- Platform**
  Duration: approximately 75 minutes. This lab covers VCF constructs (Fleet, Instance, Domain), deployment from scratch and by converging existing infrastructure, and Virtual Private Clouds (VPCs). This is the single most important lab for exam preparation. It gives you hands-on exposure to the new architectural constructs that dominate the VCF 9.0 exam blueprint.

- **HOL-2610-02-VCF-L: What's New in VMware Cloud Foundation 9.0 --- Automation**
  This lab walks you through VCF Automation, starting with tenant organization setup via the quick start wizard. It covers the Provider Portal (infrastructure, access, identity management), Organization Management (content libraries, IaaS policies), and hands-on deployment of VMs and Kubernetes clusters. Essential for Section 4 of the Administrator exam.

- **HOL-2610-03-VCF-L: What's New in VMware Cloud Foundation 9.0 --- Operations**
  This lab focuses on private cloud operations: health monitoring using Diagnostic Findings and VCF Health tools, the Network Operations Dashboard (integrating NSX and vSphere network monitoring), Storage Operations Dashboard (vSAN and non-vSAN datastore monitoring), Security Operations Dashboard (authentication activity, encryption status, certificate health), and chargeback implementation. Critical for both the Administrator and Support exams.

- **HOL-2610-04-VCF-L: What's New in VMware Cloud Foundation 9.0 --- Networking and Security**
  This lab covers NSX integration within VCF 9.0, including network segment creation, distributed firewall policy configuration, gateway firewall rules, and VPC networking constructs. Invaluable for the networking sections across all three exams.

**Broadcom TechDocs**

The official VCF 9.0 documentation at techdocs.broadcom.com is the authoritative source for every exam topic. Pay particular attention to:
- VMware Cloud Foundation Concepts documentation
- VCF Design Guide
- VCF Deployment Guide
- VCF Operations Guide
- VCF Networking Reference Architecture

**VMware Cloud Foundation Blog**

The VCF Blog at blogs.vmware.com/cloud-foundation publishes certification guidance, feature deep dives, and exam preparation tips. The Certification and Learning archives are particularly valuable.

**williamlam.com**

William Lam is a Distinguished Platform Engineering Architect in the VCF Division at Broadcom, and his blog is an indispensable resource. Key posts for exam candidates include:
- "Ultimate Lab Resource for VCF 9.0" --- a comprehensive guide to building a VCF 9.0 lab
- "Automated VMware Cloud Foundation (VCF) 9.0 Lab Deployment Script" --- an automated deployment script using nested ESXi
- "Minimal Resources for Deploying VCF 9.0 in a Lab" --- guidance on single-node and three-node deployment modes
- Posts covering VCF Fleet management, SSO identity providers, and offline depot creation

**vLore Blog (vloreblog.com)**

The vLore Blog provides targeted exam prep content, including prep notes compiled from actual exam experience. The "VCF 9.0 Admin Exam Prep" post is a concise walkthrough of what to expect, complete with downloadable study notes.

**VMware {code} VCP-VCF Study Group**

This community-led study group meets regularly and covers exam topics systematically, from encryption methods and vSAN services to SDDC Manager configuration and API usage. The group maintains a presence on the Broadcom Community Programs Discord server where you can ask questions and connect with other candidates.

**VMUG Advantage**

A VMUG Advantage membership costs $210 per year and provides significant certification benefits:
- Up to $125 off any VMware certification exam (effectively 50% off a VCP exam)
- One free VCP exam attempt at select VMUG Connect events
- Access to VCF lab licenses for hands-on practice
- Community networking with other VMware professionals

The math is simple: the membership pays for itself with a single exam discount, and the lab licenses alone are worth many times the membership fee.

### Paid Resources

**Broadcom Learning (Enterprise Learning Subscription)**

The VMware Enterprise Learning Subscription (ELS) provides 24/7 access to instructor-led and self-paced training delivered by VMware Certified Instructors. VCF platform customers may have learning entitlements included with their subscription --- check with your Broadcom account team.

Key courses for exam preparation:
- VMware Cloud Foundation: Build, Manage, and Secure [9.0]
- VMware Cloud Foundation: Automation and Operations [9.0]
- VMware NSX for VCF [9.0]

The ELS subscription includes VCP exam vouchers, making it the most cost-effective option if your organization is willing to invest in formal training.

**Udemy**

Several third-party courses on Udemy target VCF certification preparation. Look for courses with recent reviews (2025 or later), high ratings, and content aligned with the 2V0-17.25 or 2V0-13.25 exam blueprints. Use these as supplements to official resources, not replacements. Notable options include courses specifically targeting the VCF 9.0 Administrator exam with practice question sets.

---

## 30/60/90-Day Study Plans

### 90-Day Plan: The Comprehensive Path (Recommended for Career Changers and VCF Newcomers)

**Days 1--30: Foundation Building**

*Week 1: Orientation and Assessment*
- Download and read the official exam blueprint cover to cover
- Take a baseline practice test to identify your weakest areas
- Set up your study environment: bookmark TechDocs, HOL portal, exam guide
- Join the VMware {code} VCP-VCF Study Group on Discord

*Week 2: Cloud Architecture and VCF Fundamentals*
- Study Section 1 topics: cloud architectures, virtualization concepts, SDDC principles
- Read the VCF Concepts documentation on TechDocs
- Complete HOL-2610-01 (Platform) --- take notes on Fleet, Instance, and Domain constructs
- Review williamlam.com "Ultimate Lab Resource for VCF 9.0"

*Week 3: VCF Architecture Deep Dive*
- Study the Fleet/Instance/Domain hierarchy in detail
- Read TechDocs on VCF Identity Broker deployment models
- Study SSO boundaries and authentication architecture
- Review VCF deployment modes (Simple vs. High Availability)

*Week 4: Compute and Storage Foundations*
- Study vSphere fundamentals within the VCF context: DRS, HA, resource pools
- Study vSAN architecture: disk groups, fault domains, storage policies
- Complete the storage sections of the VCF Design Guide
- Take a focused practice quiz on compute and storage topics

**Days 31--60: Core Competency Development**

*Week 5: Networking (NSX in VCF)*
- Study NSX architecture within VCF: transport zones, TEPs, segments
- Review Tier-0 and Tier-1 gateway configurations
- Study distributed firewall and gateway firewall concepts
- Complete HOL-2610-04 (Networking and Security)

*Week 6: VCF Automation*
- Study VCF Automation architecture: Provider Portal, Organization Management
- Review content libraries, IaaS policies, and tenant consumption models
- Complete HOL-2610-02 (Automation)
- Practice deploying VMs and Kubernetes clusters in the HOL environment

*Week 7: VCF Operations*
- Study VCF Operations: monitoring, alerting, dashboards, capacity management
- Review Diagnostic Findings, VCF Health, and Network/Storage/Security Operations Dashboards
- Complete HOL-2610-03 (Operations)
- Study chargeback models and compliance monitoring

*Week 8: Lifecycle Management*
- Study SDDC Manager lifecycle management workflows
- Review upgrade paths, bundle management, and patching procedures
- Study certificate management and rotation procedures
- Review backup and restore procedures for management components

**Days 61--90: Mastery and Exam Readiness**

*Week 9: Troubleshooting*
- Study common VCF deployment failure scenarios and resolutions
- Review SDDC Manager logs and SoS utility usage
- Study vSAN health troubleshooting, NSX tunnel diagnostics, and upgrade failure remediation
- Practice scenario-based troubleshooting questions

*Week 10: Integration and Advanced Topics*
- Study vSphere Supervisor and Kubernetes integration in VCF
- Review VPC configurations and multi-tenancy
- Study Fleet topology design: single-site, dual-site, multi-region
- Review licensing, compliance, and fleet management

*Week 11: Practice Exams and Gap Analysis*
- Take two full-length practice exams under timed conditions
- Analyze results and identify remaining weak areas
- Create targeted flashcards for weak areas
- Re-read exam blueprint and verify coverage of every objective

*Week 12: Final Review and Exam*
- Light review only --- avoid cramming new material
- Re-complete any HOL labs where you felt uncertain
- Review your notes, flashcards, and the terminology trap table
- Schedule and take the exam (ideally Tuesday through Thursday, morning slot)

### 60-Day Plan: The Focused Path (For Experienced vSphere Admins)

**Days 1--14: Rapid Assessment and VCF-Specific Concepts**
- Read the exam blueprint and take a baseline practice test
- Focus immediately on VCF 9.0-specific constructs: Fleet, Instance, VPC, Identity Broker
- Complete HOL-2610-01 (Platform)
- Study the VCF Concepts and Architecture documentation

**Days 15--30: Core Technical Domains**
- Complete HOL-2610-02, HOL-2610-03, and HOL-2610-04
- Study NSX, vSAN, VCF Automation, and VCF Operations within VCF context
- Focus on areas where VCF differs from standalone product usage
- Review lifecycle management and certificate management workflows

**Days 31--50: Troubleshooting and Advanced Topics**
- Study troubleshooting scenarios across all domains
- Review vSphere Supervisor, Kubernetes, and VPC configurations
- Study Fleet topology design and multi-site considerations
- Take practice exams and identify gaps

**Days 51--60: Final Preparation**
- Targeted review of weak areas identified in practice exams
- Review terminology changes from VCF 5.x to VCF 9.0
- Light review of all blueprint sections
- Take the exam

### 30-Day Plan: The Accelerated Path (For Active VCF Administrators)

**Days 1--7: Blueprint Review and Gap Identification**
- Read the blueprint and honestly assess where your daily experience has gaps
- Focus study on VCF 9.0-specific changes (Fleet, Instance, VPC, Identity Broker)
- Complete HOL-2610-01 if you have not deployed VCF 9.0 in production

**Days 8--21: Targeted Study**
- Study exam topics where your production experience is thin (commonly: VCF Automation, VCF Operations dashboards, NSX security policies, Kubernetes/Supervisor)
- Complete relevant HOL labs for weak areas
- Take two practice exams to validate readiness

**Days 22--30: Polish and Execute**
- Review incorrect practice exam answers and underlying concepts
- Review terminology trap table (especially if you worked with VCF 5.x)
- Take the exam

---

## Practice Questions

The following 120 practice questions cover all exam topic areas proportionally. Each question includes four answer choices, the correct answer, and an explanation. Use these questions for self-assessment, not memorization. If you get a question wrong, go back to the relevant documentation and understand why.

### Section 1: IT Architectures, Technologies, and Standards (Questions 1--12)

**Question 1.** Which cloud deployment model provides dedicated infrastructure for a single organization while being managed by a third-party service provider?

A. Public cloud
B. Hosted private cloud
C. Community cloud
D. Hybrid cloud

**Correct Answer: B**

A hosted private cloud provides dedicated infrastructure for a single organization, but the infrastructure is owned and managed by a third-party provider in their data center. This differs from an on-premises private cloud where the organization owns the hardware, and from a public cloud where resources are shared across multiple tenants.

---

**Question 2.** What is the primary characteristic of a software-defined data center (SDDC)?

A. All infrastructure is purchased from a single vendor
B. Infrastructure services are delivered entirely through software abstraction
C. Physical hardware is eliminated in favor of cloud-hosted resources
D. Automation is optional and used only for large-scale deployments

**Correct Answer: B**

An SDDC abstracts compute, storage, and networking into software-defined layers, enabling policy-driven automation and management. Physical hardware still exists, but its capabilities are consumed through software abstractions rather than direct hardware configuration.

---

**Question 3.** Which framework defines cloud computing service models (IaaS, PaaS, SaaS) and deployment models?

A. ITIL v4
B. NIST SP 800-145
C. ISO 27001
D. COBIT 2019

**Correct Answer: B**

NIST Special Publication 800-145 is the definitive framework that defines cloud computing, including the five essential characteristics, three service models (IaaS, PaaS, SaaS), and four deployment models (public, private, hybrid, community).

---

**Question 4.** An organization requires a cloud architecture that allows workloads to move between on-premises infrastructure and public cloud services based on demand. Which architecture best meets this requirement?

A. Multi-cloud
B. Hybrid cloud
C. Private cloud
D. Community cloud

**Correct Answer: B**

A hybrid cloud combines on-premises (private) infrastructure with public cloud resources, enabling workload mobility between the two environments based on demand, cost, or performance requirements. Multi-cloud uses multiple public cloud providers but does not necessarily include on-premises infrastructure.

---

**Question 5.** What is the primary benefit of infrastructure as code (IaC) in a cloud environment?

A. It eliminates the need for physical hardware
B. It ensures infrastructure configurations are repeatable and version-controlled
C. It replaces the need for monitoring tools
D. It guarantees zero-downtime deployments

**Correct Answer: B**

Infrastructure as code enables teams to define infrastructure configurations in machine-readable files, ensuring consistency, repeatability, and version control. It does not eliminate physical hardware, replace monitoring, or inherently guarantee zero downtime.

---

**Question 6.** Which virtualization technology allows multiple operating systems to run simultaneously on a single physical host?

A. Containerization
B. Type 1 hypervisor
C. Application virtualization
D. Network function virtualization

**Correct Answer: B**

A Type 1 (bare-metal) hypervisor, such as ESXi, runs directly on physical hardware and enables multiple guest operating systems to run simultaneously. While containers share a single OS kernel, a Type 1 hypervisor provides full OS isolation for each virtual machine.

---

**Question 7.** What role does an API gateway play in a modern cloud infrastructure?

A. It replaces load balancers for application traffic distribution
B. It provides a single entry point for API consumers with authentication and rate limiting
C. It serves as the primary data storage layer for microservices
D. It eliminates the need for network firewalls

**Correct Answer: B**

An API gateway acts as a centralized entry point for API consumers, providing authentication, authorization, rate limiting, and request routing. It complements, rather than replaces, load balancers and firewalls.

---

**Question 8.** In a multi-tier application architecture, which layer is responsible for business logic processing?

A. Presentation tier
B. Application tier
C. Data tier
D. Network tier

**Correct Answer: B**

The application tier (also called the logic tier or middle tier) handles business logic processing. The presentation tier handles the user interface, the data tier manages persistent storage, and the network tier is not a standard tier in the multi-tier architecture model.

---

**Question 9.** Which storage technology provides block-level access to storage volumes over a network?

A. NFS
B. iSCSI
C. SMB/CIFS
D. Object storage

**Correct Answer: B**

iSCSI provides block-level access to storage volumes over an IP network by encapsulating SCSI commands in TCP/IP packets. NFS and SMB/CIFS provide file-level access, while object storage provides object-level access via HTTP-based APIs.

---

**Question 10.** What is the primary purpose of network segmentation in a data center?

A. To increase total network bandwidth
B. To reduce the blast radius of security incidents and improve traffic management
C. To eliminate the need for firewalls
D. To consolidate all traffic onto a single VLAN

**Correct Answer: B**

Network segmentation divides a network into smaller segments, reducing the blast radius of security breaches and improving traffic management. It does not increase bandwidth, eliminate firewalls, or consolidate VLANs.

---

**Question 11.** Which IT service management practice focuses on restoring normal service operation as quickly as possible?

A. Problem management
B. Incident management
C. Change management
D. Capacity management

**Correct Answer: B**

Incident management focuses on restoring normal service operation as quickly as possible while minimizing business impact. Problem management focuses on identifying root causes. Change management controls infrastructure modifications. Capacity management ensures resources meet demand.

---

**Question 12.** What distinguishes a Type 1 hypervisor from a Type 2 hypervisor?

A. Type 1 supports more virtual machines than Type 2
B. Type 1 runs directly on hardware; Type 2 runs on top of a host operating system
C. Type 1 is free; Type 2 requires a license
D. Type 1 supports only Linux guests; Type 2 supports all operating systems

**Correct Answer: B**

A Type 1 (bare-metal) hypervisor runs directly on physical hardware without a host OS, providing better performance and security. A Type 2 (hosted) hypervisor runs as an application on top of a host operating system. ESXi is a Type 1 hypervisor; VMware Workstation is a Type 2 hypervisor.

---

### Section 2: VMware Cloud Foundation Fundamentals (Questions 13--30)

**Question 13.** In VCF 9.0, what is a Fleet?

A. A collection of ESXi hosts in a single cluster
B. A governance and lifecycle scope for shared management services across VCF Instances
C. A redundancy group for vCenter Server
D. A storage pool spanning multiple vSAN clusters

**Correct Answer: B**

A Fleet in VCF 9.0 is a governance and lifecycle scope that provides shared services (VCF Automation, VCF Operations, VCF Identity Broker) across one or more VCF Instances. It is not a host cluster, vCenter group, or storage pool.

---

**Question 14.** Which component replaced VMware Identity Manager (VIDM) in VCF 9.0?

A. vCenter SSO
B. VCF Identity Broker (vIDB)
C. NSX Identity Firewall
D. Active Directory Federation Services

**Correct Answer: B**

VCF Identity Broker (vIDB) replaced VIDM in VCF 9.0. It provides centralized authentication services for all VCF components and can be deployed as an embedded service on vCenter or as a standalone three-node appliance cluster.

---

**Question 15.** What is the role of SDDC Manager in VCF 9.0?

A. It provides virtual machine backup and recovery
B. It manages the lifecycle of VCF Instance components including deployment, patching, and upgrades
C. It serves as the primary load balancer for workload traffic
D. It replaces vCenter for host management

**Correct Answer: B**

SDDC Manager is the lifecycle management engine for a VCF Instance. It handles deployment, configuration, patching, upgrading, and certificate management of VCF components within an Instance. It does not replace vCenter, provide backup, or serve as a load balancer.

---

**Question 16.** How many deployment modes does VCF 9.0 support for management components?

A. One: High Availability (three-node)
B. Two: Simple (single-node) and High Availability (three-node)
C. Three: Minimal, Standard, and Enterprise
D. Four: Dev, Test, Standard, and Production

**Correct Answer: B**

VCF 9.0 supports two deployment modes: Simple (single-node) for lab and small environments, and High Availability (three-node) for production environments. This applies to components like NSX Manager, VCF Operations, and VCF Automation.

---

**Question 17.** Which Fleet-level service provides self-service consumption and multi-tenant organization constructs?

A. VCF Operations
B. SDDC Manager
C. VCF Automation
D. VCF Identity Broker

**Correct Answer: C**

VCF Automation is a Fleet-level service that provides the Provider Portal for infrastructure management and Organization Management for tenant self-service consumption, including content libraries, IaaS policies, and VM/Kubernetes deployment.

---

**Question 18.** What is a Virtual Private Cloud (VPC) in VCF 9.0?

A. A public cloud integration that extends VCF to AWS or Azure
B. A consumption model that provides isolated network and compute resources within a VCF environment
C. A VPN tunnel between two VCF Instances
D. A deprecated feature replaced by workload domains

**Correct Answer: B**

A VPC in VCF 9.0 is a consumption model that provides tenants with isolated network and compute resources within the VCF platform. It is a new construct in VCF 9.0, not a public cloud integration or VPN.

---

**Question 19.** In VCF 9.0, which component maintains its own vCenter Server and NSX control plane?

A. Fleet
B. Instance
C. Workload Domain
D. VCF Automation

**Correct Answer: B**

Each VCF Instance maintains its own SDDC Manager, vCenter Server, and NSX control plane. The Fleet provides shared services above the Instance level but does not own per-Instance management components.

---

**Question 20.** What is the recommended deployment model for VCF Identity Broker in a multi-Instance Fleet?

A. Embedded on each vCenter Server
B. Three-node appliance cluster
C. Single standalone appliance
D. Integrated into SDDC Manager

**Correct Answer: B**

For multi-Instance Fleets, the recommended deployment is a three-node VCF Identity Broker appliance cluster, which provides the availability and scale needed to serve up to five Instances per broker. The embedded model is suitable for single-Instance deployments.

---

**Question 21.** Which VCF component provides centralized monitoring, capacity management, and compliance reporting?

A. VCF Automation
B. SDDC Manager
C. VCF Operations
D. NSX Manager

**Correct Answer: C**

VCF Operations provides centralized monitoring, alerting, dashboards, capacity management, and compliance reporting across the VCF Fleet. It operates as a Fleet-level service, distinct from SDDC Manager (lifecycle management) and VCF Automation (self-service).

---

**Question 22.** What is the primary function of the VCF Operations Diagnostic Findings feature?

A. Automated remediation of all detected issues
B. Real-time checks for security advisories (VMSAs), CVEs, and best practice deviations
C. Backup validation for management components
D. Network throughput optimization

**Correct Answer: B**

Diagnostic Findings provides real-time checks for VMware Security Advisories (VMSAs), Common Vulnerabilities and Exposures (CVEs), and best practice deviations, categorizing issues as Critical, Immediate, or Warning. It identifies issues but does not automatically remediate all of them.

---

**Question 23.** In VCF 9.0, what is the relationship between a management domain and a workload domain?

A. They are identical constructs with different names
B. The management domain hosts VCF management components; workload domains host customer workloads
C. Workload domains contain management domains
D. Management domains are optional in VCF 9.0

**Correct Answer: B**

The management domain hosts VCF management infrastructure (vCenter, SDDC Manager, NSX Manager), while workload domains host customer applications and services. The management domain is always created first during VCF deployment and is a required component.

---

**Question 24.** Which tool is used for initial VCF deployment in a greenfield environment?

A. SDDC Manager
B. VCF Automation
C. Cloud Builder
D. vCenter Server

**Correct Answer: C**

Cloud Builder is the deployment tool used for initial VCF deployment in greenfield environments. It automates the bring-up process, validating prerequisites and deploying the management domain. After initial deployment, SDDC Manager takes over lifecycle management.

---

**Question 25.** How does VCF 9.0 handle brownfield environments?

A. Brownfield deployments are not supported in VCF 9.0
B. Existing vSphere environments can be converted to VCF using the conversion workflow
C. Brownfield environments must be rebuilt from scratch
D. Only environments running vSphere 8.0 or later can be converted

**Correct Answer: B**

VCF 9.0 supports converting existing vSphere environments to VCF through a conversion workflow. This allows organizations to bring their existing infrastructure under VCF management without rebuilding from scratch.

---

**Question 26.** What is the maximum number of VCF Instances recommended per VCF Identity Broker appliance cluster?

A. 2
B. 3
C. 5
D. 10

**Correct Answer: C**

The rule of thumb for a VCF Identity Broker appliance cluster is up to five Instances per broker cluster. Beyond five Instances, additional broker clusters should be deployed.

---

**Question 27.** Which dashboard in VCF Operations provides centralized monitoring for vSAN and non-vSAN datastores?

A. Network Operations Dashboard
B. Storage Operations Dashboard
C. Security Operations Dashboard
D. Compute Operations Dashboard

**Correct Answer: B**

The Storage Operations Dashboard centralizes monitoring for vSAN and non-vSAN datastores, enabling analysis of storage alerts, usage trends, capacity distribution, and performance metrics across the VCF environment.

---

**Question 28.** What type of information does the VCF Operations Security Operations Dashboard track?

A. Firewall rule hit counts only
B. User Security (authentication activity, failed logins) and Infrastructure Security (encryption status, certificate health)
C. Antivirus scan results
D. Physical access control logs

**Correct Answer: B**

The Security Operations Dashboard covers two domains: User Security (tracking authentication activity and failed logins) and Infrastructure Security (reviewing host encryption status, vSAN cluster encryption, and certificate health).

---

**Question 29.** In VCF 9.0, where do Fleet-level services like VCF Automation and VCF Operations run?

A. On dedicated physical servers outside the VCF environment
B. Within the management domain of the Fleet
C. On every ESXi host in the Fleet
D. In a public cloud connected to the Fleet

**Correct Answer: B**

Fleet-level services including VCF Automation, VCF Operations, and VCF Identity Broker run within the management domain of the Fleet. They are deployed as virtual appliances managed by SDDC Manager.

---

**Question 30.** What is the Network Operations Dashboard in VCF Operations?

A. A replacement for NSX Manager
B. An integrated view combining NSX and vSphere network monitoring within VCF Operations
C. A physical switch management interface
D. A DNS management console

**Correct Answer: B**

The Network Operations Dashboard integrates NSX and vSphere network monitoring directly into VCF Operations, providing a unified view of network health, flow analysis, and connectivity status without replacing NSX Manager.

---

### Section 3: Plan and Design (Questions 31--42)

**Question 31.** When planning a VCF deployment, what is the minimum number of ESXi hosts required for the management domain in a standard deployment?

A. 2
B. 3
C. 4
D. 8

**Correct Answer: C**

The management domain in a standard VCF deployment requires a minimum of four ESXi hosts to support the management components (vCenter, SDDC Manager, NSX Manager cluster) with appropriate redundancy through vSAN and HA configurations.

---

**Question 32.** Which network must be configured before VCF deployment can begin?

A. vMotion network only
B. Management, vMotion, vSAN, and host TEP networks
C. Only the management network
D. The workload VM network

**Correct Answer: B**

VCF deployment requires pre-configuration of multiple networks: the management network for component communication, the vMotion network for live migration, the vSAN network for storage traffic, and the host TEP (Tunnel Endpoint) network for NSX overlay traffic. All must be in place before Cloud Builder can proceed.

---

**Question 33.** An organization requires 99.99% availability for their VCF management components. Which deployment mode should be selected?

A. Simple (single-node) mode
B. High Availability (three-node) mode
C. Either mode meets the requirement
D. A custom four-node deployment

**Correct Answer: B**

High Availability (three-node) mode deploys management components (NSX Manager, VCF Operations, VCF Automation) as three-node clusters, providing the redundancy needed for 99.99% availability. Simple mode is single-node and cannot meet this SLA.

---

**Question 34.** When designing vSAN storage policies for a VCF environment, what does the Failures to Tolerate (FTT) setting determine?

A. The maximum number of host failures the cluster can survive
B. The number of component failures a storage object can tolerate
C. The total number of disk failures allowed per host
D. The replication factor for backup jobs

**Correct Answer: B**

Failures to Tolerate (FTT) determines how many component failures a vSAN storage object can withstand. With FTT=1 and RAID-1 mirroring, data is stored in two copies; with FTT=2, three copies. This is a per-object policy, not a cluster-level setting.

---

**Question 35.** An architect is designing a VCF environment for an organization with strict data sovereignty requirements across two geographic regions. Which Fleet topology is most appropriate?

A. Single-site Fleet with stretched clusters
B. Multi-region Fleet with separate Instances per region
C. Single Instance spanning both regions
D. Two separate Fleets with no relationship

**Correct Answer: B**

A multi-region Fleet topology with separate Instances per region meets data sovereignty requirements by keeping workload data within regional boundaries while maintaining centralized Fleet-level governance (VCF Automation, VCF Operations) across regions.

---

**Question 36.** What is the recommended approach for NTP configuration in a VCF environment?

A. Each component should use a different NTP source for redundancy
B. All VCF components should reference a common, reliable NTP source
C. NTP is not required for VCF
D. Only ESXi hosts need NTP; management components use their internal clocks

**Correct Answer: B**

All VCF components must reference a common, reliable NTP source to ensure time synchronization. Time skew between components causes certificate validation failures, SSO issues, and log correlation problems.

---

**Question 37.** When designing NSX network topology for VCF, what is the purpose of a Tier-0 gateway?

A. It provides routing between VMs on the same logical segment
B. It provides north-south routing between the NSX overlay and the physical network
C. It replaces the distributed switch for VM networking
D. It manages DNS resolution for overlay segments

**Correct Answer: B**

A Tier-0 gateway provides north-south routing, connecting the NSX overlay network to the physical network infrastructure. It handles BGP/OSPF peering with physical routers, NAT, and serves as the exit point for traffic leaving the overlay.

---

**Question 38.** An organization needs to provide separate VCF environments for development and production with shared monitoring. What is the recommended design?

A. Single Instance with two workload domains
B. Two Instances within a single Fleet
C. Two separate Fleets
D. Single workload domain with resource pools

**Correct Answer: B**

Two Instances within a single Fleet provides environment separation (each Instance has its own vCenter, SDDC Manager, and NSX) while sharing Fleet-level services like VCF Operations for unified monitoring. A single Instance with workload domains provides less isolation, while separate Fleets prevent shared monitoring.

---

**Question 39.** What DNS records must be created before VCF deployment?

A. Forward (A) records only for management components
B. Forward (A) and reverse (PTR) records for all VCF management components
C. Only PTR records for ESXi hosts
D. DNS records are optional in VCF 9.0

**Correct Answer: B**

Both forward (A) and reverse (PTR) DNS records must be created for all VCF management components before deployment. Cloud Builder validates DNS resolution as part of the pre-deployment checks, and deployment will fail if records are missing.

---

**Question 40.** When designing certificate infrastructure for VCF, which certificate authority type does SDDC Manager support?

A. Only self-signed certificates
B. Microsoft Certificate Authority (CA) and OpenSSL CA
C. Microsoft CA, OpenSSL CA, and third-party CAs via SDDC Manager certificate management
D. Only publicly trusted CAs

**Correct Answer: C**

SDDC Manager supports Microsoft CA, OpenSSL CA, and third-party certificate authorities. Organizations can use SDDC Manager's built-in certificate management workflows to generate CSRs and install certificates from their preferred CA.

---

**Question 41.** What is the primary consideration when sizing ESXi hosts for a vSAN-based VCF deployment?

A. Maximizing the number of VMs per host
B. Balancing compute capacity with storage capacity to avoid resource stranding
C. Using the largest available hosts to minimize host count
D. Ensuring all hosts have identical GPU configurations

**Correct Answer: B**

In vSAN-based deployments, host sizing must balance compute and storage capacity to prevent resource stranding --- a situation where you run out of one resource type while having excess of another. Oversizing storage on compute-bound hosts or vice versa wastes investment.

---

**Question 42.** An architect needs to design a VCF environment that supports both traditional VMs and containerized workloads. Which component enables Kubernetes integration?

A. VCF Automation
B. vSphere Supervisor
C. NSX Manager
D. SDDC Manager

**Correct Answer: B**

vSphere Supervisor (formerly Tanzu Kubernetes Grid Service) enables native Kubernetes integration within VCF, allowing administrators to run containerized workloads alongside traditional VMs on the same infrastructure with shared networking, storage, and security policies.

---

### Section 4: Deploy, Configure, and Operate (Questions 43--84)

**Question 43.** During VCF deployment using Cloud Builder, which validation check runs first?

A. vSAN disk compatibility check
B. Network connectivity and DNS resolution validation
C. License key validation
D. Certificate authority connectivity check

**Correct Answer: B**

Cloud Builder runs network connectivity and DNS resolution validation as one of the first pre-deployment checks. Without proper network reachability and DNS resolution for all management components, subsequent deployment steps cannot proceed.

---

**Question 44.** What is the correct order for adding capacity to a VCF environment?

A. Add hosts to cluster, create workload domain, commission hosts
B. Commission hosts in SDDC Manager, add hosts to cluster, create workload domain if needed
C. Create workload domain, add hosts, commission hosts
D. Add hosts to vCenter, then register with SDDC Manager

**Correct Answer: B**

The correct workflow is: (1) commission new hosts in SDDC Manager, which prepares them with the correct VIBs and configuration; (2) add the commissioned hosts to an existing cluster or (3) create a new workload domain. Hosts must always be commissioned through SDDC Manager first.

---

**Question 45.** How does SDDC Manager handle VCF component upgrades?

A. All components are upgraded simultaneously
B. Components are upgraded in a specific order determined by the upgrade bundle and pre-check validation
C. Administrators must manually upgrade each component independently
D. Upgrades are only available through VMware support intervention

**Correct Answer: B**

SDDC Manager orchestrates upgrades by downloading upgrade bundles, running pre-check validations, and upgrading components in a specific order to maintain inter-component compatibility. The upgrade sequence is determined by bundle dependencies, not arbitrary administrator choice.

---

**Question 46.** Which SDDC Manager feature validates the health of a VCF environment before operations like upgrades?

A. VCF Health Check
B. Pre-check Validation
C. SoS Utility
D. Diagnostic Findings

**Correct Answer: B**

SDDC Manager's pre-check validation runs a comprehensive suite of tests before lifecycle operations (upgrades, patches, host additions) to verify that the environment meets all prerequisites. The SoS utility collects support bundles; Diagnostic Findings is a VCF Operations feature.

---

**Question 47.** What is the purpose of the SoS (Support and Serviceability) utility in VCF?

A. It performs automated remediation of common issues
B. It collects diagnostic information and support bundles from VCF components
C. It provides real-time monitoring dashboards
D. It manages certificate rotation

**Correct Answer: B**

The SoS utility collects diagnostic information, logs, and configuration data from VCF components into support bundles. These bundles are used by VMware support and administrators for troubleshooting. SoS does not perform remediation or provide monitoring.

---

**Question 48.** An administrator needs to replace a failed ESXi host in a VCF workload domain. What is the correct procedure?

A. Decommission the failed host, commission the replacement host, add it to the cluster
B. Remove the host from vCenter and add the new one directly
C. Replace the hardware and re-image the host with the same name
D. Remove the workload domain and recreate it with the new host

**Correct Answer: A**

The correct procedure is to decommission the failed host through SDDC Manager, then commission the replacement host (which applies the correct VIBs and configurations), and finally add the commissioned host to the cluster. Direct vCenter manipulation bypasses SDDC Manager and creates inventory inconsistencies.

---

**Question 49.** Which VCF Automation component provides infrastructure administrators with visibility and control over physical and virtual resources?

A. Organization Portal
B. Provider Portal
C. Service Broker
D. Cloud Assembly

**Correct Answer: B**

The Provider Portal in VCF Automation gives infrastructure administrators (providers) visibility and control over infrastructure resources, access management, and identity configuration. The Organization Portal serves tenant consumers.

---

**Question 50.** How are IaaS policies used in VCF Automation?

A. They define network firewall rules
B. They govern resource allocation, placement, and consumption limits for tenant organizations
C. They manage backup schedules
D. They control ESXi host boot order

**Correct Answer: B**

IaaS policies in VCF Automation govern how tenant organizations consume infrastructure resources, including placement constraints, resource quotas, naming conventions, and approval workflows. They are not firewall rules or backup policies.

---

**Question 51.** What is the purpose of content libraries in VCF Automation?

A. Storing VCF upgrade bundles
B. Providing reusable templates, ISOs, and scripts for tenant consumption
C. Managing certificate chains
D. Archiving SDDC Manager logs

**Correct Answer: B**

Content libraries in VCF Automation store reusable artifacts --- VM templates, OVA/OVF packages, ISO images, and scripts --- that tenants can consume through self-service catalogs. They are not used for upgrade bundles or log storage.

---

**Question 52.** An administrator needs to configure VCF Operations to monitor a newly created workload domain. What must happen first?

A. Deploy a separate VCF Operations instance for each workload domain
B. Add the workload domain's vCenter as a data source in VCF Operations
C. Install a monitoring agent on every VM in the workload domain
D. Create a new Fleet for the workload domain

**Correct Answer: B**

VCF Operations uses vCenter Server as a data source to discover and monitor objects within a workload domain. Adding the workload domain's vCenter as a data source enables monitoring. No per-VM agents are required for infrastructure monitoring, and a separate VCF Operations instance is not needed.

---

**Question 53.** What is the correct method to rotate certificates for VCF management components?

A. Manually replace certificates on each component
B. Use SDDC Manager certificate management workflows to generate CSRs and install certificates
C. Certificates are automatically rotated and require no administrator action
D. Contact VMware support for certificate rotation

**Correct Answer: B**

SDDC Manager provides certificate management workflows that automate CSR generation and certificate installation across VCF components. While not fully automatic, these workflows ensure consistent certificate rotation and reduce the risk of manual errors.

---

**Question 54.** Which VCF component is responsible for NSX transport zone configuration?

A. SDDC Manager
B. VCF Automation
C. NSX Manager
D. vCenter Server

**Correct Answer: C**

NSX Manager is responsible for configuring transport zones, which define the scope of overlay and VLAN-based networking in the VCF environment. While SDDC Manager orchestrates the initial deployment of NSX, ongoing network configuration is performed through NSX Manager.

---

**Question 55.** How does VCF handle multi-tenancy for network isolation?

A. Each tenant gets a dedicated physical switch
B. VPCs and NSX segments provide logical network isolation between tenants
C. VLANs are manually configured on physical switches for each tenant
D. Multi-tenancy is not supported in VCF 9.0

**Correct Answer: B**

VCF 9.0 uses Virtual Private Clouds (VPCs) and NSX segments to provide logical network isolation between tenants. This software-defined approach eliminates the need for per-tenant physical infrastructure while maintaining strong isolation.

---

**Question 56.** What is the purpose of the VCF Automation quick start wizard?

A. It deploys the entire VCF Fleet from scratch
B. It guides initial setup of tenant organizations, including identity provider and resource configuration
C. It installs ESXi on bare-metal hosts
D. It performs automated backups of VCF components

**Correct Answer: B**

The VCF Automation quick start wizard guides administrators through initial tenant organization setup, including identity provider integration, resource configuration, and basic policy establishment. It is a post-deployment configuration tool, not a deployment tool.

---

**Question 57.** An administrator needs to back up the SDDC Manager database. Which method is supported?

A. Using SDDC Manager's built-in backup workflow to an SFTP server
B. Taking a vSAN snapshot of the SDDC Manager VM only
C. Exporting the database via SQL commands
D. Backup is not required for SDDC Manager

**Correct Answer: A**

SDDC Manager supports built-in backup workflows that export the database and configuration to an external SFTP server. VM snapshots are not a supported backup method for SDDC Manager, and the database should not be exported via direct SQL commands.

---

**Question 58.** What happens when an administrator decommissions an ESXi host in SDDC Manager?

A. The host is immediately powered off
B. SDDC Manager removes VCF-specific configurations, migrates workloads, and removes the host from VCF inventory
C. Only the host's VMs are deleted
D. The host remains in the cluster but is marked as non-operational

**Correct Answer: B**

Decommissioning a host through SDDC Manager triggers a workflow that migrates workloads off the host, removes VCF-specific configurations (VIBs, network settings), and removes the host from the VCF and vCenter inventory. The host is not powered off but is returned to a clean state.

---

**Question 59.** Which VCF 9.0 feature allows organizations to bring existing vSphere environments under VCF management?

A. Cloud Builder greenfield deployment
B. VCF Conversion workflow
C. VCF Automation import
D. NSX Federation

**Correct Answer: B**

The VCF Conversion workflow enables organizations to bring existing vSphere environments under VCF management without rebuilding the infrastructure. This brownfield approach preserves existing workloads and configurations while adding VCF lifecycle management capabilities.

---

**Question 60.** What is the purpose of upgrade bundles in SDDC Manager?

A. They contain only security patches
B. They package component updates, dependencies, and upgrade orchestration logic for lifecycle operations
C. They are used exclusively for NSX upgrades
D. They replace the SoS utility during troubleshooting

**Correct Answer: B**

Upgrade bundles in SDDC Manager package component updates along with dependency information and upgrade orchestration logic. They ensure components are upgraded in the correct order with proper compatibility validation, covering the full VCF stack rather than individual components.

---

**Question 61.** An administrator deploys a new VI workload domain. Which components are automatically deployed?

A. Only the ESXi hosts are configured
B. vCenter Server, NSX is extended to the domain, and vSAN is configured for the cluster
C. Only vSAN is configured; vCenter and NSX require manual setup
D. A separate SDDC Manager instance is deployed for the workload domain

**Correct Answer: B**

When a new VI workload domain is created through SDDC Manager, the workflow automatically deploys or extends vCenter Server for management, configures NSX for networking, and sets up vSAN for storage. No separate SDDC Manager instance is needed; the existing Instance SDDC Manager manages all workload domains.

---

**Question 62.** How does VCF 9.0 handle licensing compliance across a Fleet?

A. Each Instance manages its own licenses independently
B. Fleet-level license management tracks compliance, allocation, and usage across all Instances
C. Licenses are applied only at the workload domain level
D. Licensing is handled exclusively through the Broadcom portal

**Correct Answer: B**

VCF 9.0 provides Fleet-level license management that tracks compliance, allocation, and usage across all Instances within the Fleet. This centralized approach ensures license compliance and simplifies reporting for multi-Instance environments.

---

**Question 63.** What is the recommended approach for configuring distributed firewall rules in an NSX environment within VCF?

A. Apply a default allow-all rule and create deny rules for restricted traffic
B. Apply a default deny rule and create allow rules for approved traffic (zero-trust model)
C. Use only gateway firewall rules instead of distributed firewall rules
D. Configure firewall rules on physical switches instead of NSX

**Correct Answer: B**

The zero-trust model (default deny with explicit allow rules) is the recommended approach for distributed firewall configuration in NSX. This ensures that only approved traffic flows between workloads, reducing the attack surface and meeting security best practices.

---

**Question 64.** An administrator needs to deploy Kubernetes clusters for application developers. Which VCF 9.0 workflow supports this?

A. Deploying VMs with Kubernetes manually installed
B. Using VCF Automation to deploy Kubernetes clusters through the Organization Portal
C. Installing Kubernetes directly on ESXi hosts
D. Using NSX to create container networks without vSphere integration

**Correct Answer: B**

VCF Automation enables self-service deployment of Kubernetes clusters through the Organization Portal. This leverages vSphere Supervisor to provision and manage Kubernetes clusters with integrated networking (NSX) and storage (vSAN) without requiring manual installation.

---

**Question 65.** What does the VCF Health tool provide in VCF Operations?

A. A replacement for SDDC Manager pre-check validation
B. Real-time and historical health status of VCF components with categorized findings
C. Automated certificate rotation
D. Backup status reporting only

**Correct Answer: B**

VCF Health provides real-time and historical health monitoring of VCF components, categorizing findings by severity (Critical, Immediate, Warning). It complements but does not replace SDDC Manager's pre-check validation, which is specific to lifecycle operations.

---

**Question 66.** Which protocol does VCF use for storage traffic in a vSAN-based deployment?

A. iSCSI
B. NFS v4.1
C. vSAN proprietary protocol over dedicated vSAN network
D. Fibre Channel

**Correct Answer: C**

vSAN uses its own proprietary protocol for storage I/O traffic, which runs over a dedicated vSAN network. This network must be configured during VCF deployment and requires sufficient bandwidth (typically 25 GbE or higher) for optimal performance.

---

**Question 67.** An administrator wants to monitor network flow data within a VCF environment. Which component provides this capability?

A. VCF Automation
B. VCF Operations Network Operations Dashboard with NSX integration
C. SDDC Manager reports
D. vCenter Performance Charts

**Correct Answer: B**

The VCF Operations Network Operations Dashboard integrates NSX flow data and vSphere network monitoring, providing visibility into network traffic patterns, flow analysis, and connectivity status across the VCF environment.

---

**Question 68.** What is the correct method to add a new workload domain to an existing VCF Instance?

A. Deploy a new Cloud Builder and run the bring-up workflow
B. Use SDDC Manager to create a new workload domain using commissioned hosts
C. Manually install vCenter and NSX, then register with SDDC Manager
D. Clone the management domain

**Correct Answer: B**

New workload domains are created through SDDC Manager using hosts that have already been commissioned. SDDC Manager orchestrates the deployment of vCenter (or extension of an existing vCenter), NSX configuration, and vSAN setup for the new domain.

---

**Question 69.** How does VCF Automation handle identity integration for tenant organizations?

A. Each tenant must use a local user database
B. VCF Automation integrates with VCF Identity Broker for centralized authentication across identity providers
C. LDAP is the only supported identity source
D. Tenants cannot have their own identity configurations

**Correct Answer: B**

VCF Automation integrates with VCF Identity Broker (vIDB) for centralized authentication, supporting multiple identity providers including Active Directory, LDAP, and OIDC-compliant providers. This enables each tenant organization to use their preferred identity source.

---

**Question 70.** What must an administrator verify before downloading upgrade bundles in SDDC Manager?

A. That all VMs are powered off
B. That the environment has internet connectivity to the Broadcom depot or a configured offline depot
C. That all workload domains are in maintenance mode
D. That VCF Automation is disabled

**Correct Answer: B**

SDDC Manager downloads upgrade bundles from the Broadcom online depot (requiring internet connectivity) or from a configured offline depot for air-gapped environments. No other components need to be disabled or placed in maintenance mode for bundle downloads.

---

**Question 71.** Which VCF component enables chargeback reporting for tenant resource consumption?

A. SDDC Manager
B. NSX Manager
C. VCF Operations
D. VCF Identity Broker

**Correct Answer: C**

VCF Operations provides chargeback and showback reporting capabilities, enabling organizations to track and report resource consumption by tenant, workload domain, or organizational unit. This supports internal cost allocation and capacity planning.

---

**Question 72.** An administrator needs to configure micro-segmentation for workloads in a VCF environment. Which feature should they use?

A. vSAN encryption
B. NSX Distributed Firewall
C. vSphere DRS rules
D. VCF Automation policies

**Correct Answer: B**

NSX Distributed Firewall provides micro-segmentation by applying firewall rules at the virtual NIC level of each workload. This enables granular, workload-level security policies without requiring traffic to traverse a physical firewall.

---

**Question 73.** What is the purpose of NSX transport nodes in a VCF environment?

A. They route traffic between VCF Fleets
B. They are hypervisor hosts prepared with NSX VIBs to participate in the NSX overlay network
C. They replace physical switches in the data center
D. They provide DNS services for overlay networks

**Correct Answer: B**

NSX transport nodes are ESXi hosts that have been prepared with NSX VIBs and configured with tunnel endpoints (TEPs) to participate in the NSX overlay network. They enable overlay networking, distributed firewall, and other NSX capabilities on the host.

---

**Question 74.** Which VCF 9.0 feature enables administrators to manage the lifecycle of Fleet management components?

A. VCF Automation lifecycle workflows
B. VCF Operations Fleet management appliance
C. Cloud Builder Fleet edition
D. NSX Federation manager

**Correct Answer: B**

The VCF Operations Fleet management appliance handles lifecycle management operations for Fleet management components, including updates, health monitoring, and configuration management of the shared Fleet services.

---

**Question 75.** An administrator is configuring VCF Automation and needs to set up the Provider Portal. What is the first step?

A. Create tenant organizations
B. Configure infrastructure resources and cloud accounts
C. Deploy Kubernetes clusters
D. Set up chargeback policies

**Correct Answer: B**

The Provider Portal setup begins with configuring infrastructure resources and cloud accounts, which define the available compute, storage, and network resources. Only after infrastructure is configured can tenant organizations be created and policies applied.

---

**Question 76.** What is the role of a Tier-1 gateway in NSX within a VCF deployment?

A. It provides external connectivity to the physical network
B. It provides east-west routing between segments and connects to the Tier-0 gateway for north-south traffic
C. It replaces the distributed switch
D. It manages vSAN traffic routing

**Correct Answer: B**

A Tier-1 gateway provides east-west routing between NSX segments within a tenant or workload domain and connects to the Tier-0 gateway for north-south (external) traffic. It enables multi-tier application topologies with isolated routing per tenant or application.

---

**Question 77.** How does SDDC Manager ensure compatibility during VCF upgrades?

A. It relies on administrators to verify compatibility manually
B. It checks the VCF compatibility matrix and runs pre-upgrade validations before applying updates
C. It only upgrades components from the same vendor
D. It skips incompatible components automatically

**Correct Answer: B**

SDDC Manager checks the VCF compatibility matrix and runs comprehensive pre-upgrade validations before applying updates. These checks verify interoperability between components, ensuring that the upgrade will not create incompatibilities between VCF stack components.

---

**Question 78.** An administrator wants to enable vSphere DRS in a VCF workload domain cluster. Where is this configured?

A. SDDC Manager
B. VCF Automation
C. vCenter Server
D. NSX Manager

**Correct Answer: C**

vSphere DRS (Distributed Resource Scheduler) is configured through vCenter Server at the cluster level. While SDDC Manager deploys the cluster, DRS settings and policies are managed through the vCenter Server interface.

---

**Question 79.** What is the purpose of the VCF Automation Organization Management interface?

A. Managing physical hardware inventory
B. Providing tenant administrators with access to content libraries, IaaS policies, and self-service deployment
C. Configuring NSX firewall rules
D. Managing VCF upgrade schedules

**Correct Answer: B**

The Organization Management interface in VCF Automation provides tenant administrators with access to self-service capabilities including content libraries (templates, images), IaaS policies (quotas, naming), and deployment workflows for VMs and Kubernetes clusters.

---

**Question 80.** An administrator notices that VCF Operations is not collecting data from a workload domain. What should they check first?

A. Whether VCF Automation is running
B. Whether the vCenter data source connection for that workload domain is active and authenticated
C. Whether the ESXi hosts have sufficient memory
D. Whether NSX Manager is operational

**Correct Answer: B**

VCF Operations collects data through vCenter data source connections. If data collection stops, the first troubleshooting step is to verify that the vCenter connection for the affected workload domain is active, properly authenticated, and not blocked by network issues.

---

**Question 81.** Which VCF feature enables centralized management of all VCF Instances from a single interface?

A. VCF Fleet Management Console
B. SDDC Manager multi-instance mode
C. vCenter Linked Mode
D. NSX Federation

**Correct Answer: A**

The VCF Fleet Management Console provides centralized visibility and management across all VCF Instances within a Fleet. While vCenter Linked Mode connects multiple vCenters, the Fleet Management Console is the VCF-native tool for cross-Instance management.

---

**Question 82.** What must an administrator do before removing a workload domain from VCF?

A. Simply delete it from SDDC Manager
B. Migrate or remove all workloads, then decommission the workload domain through SDDC Manager
C. Power off all hosts in the domain
D. Remove NSX configuration first, then delete through vCenter

**Correct Answer: B**

Before removing a workload domain, all workloads must be migrated or removed. Then the workload domain is decommissioned through SDDC Manager, which orchestrates the proper removal of all associated components (vCenter, NSX, vSAN) in the correct order.

---

**Question 83.** How does VCF 9.0 support offline (air-gapped) environments for lifecycle management?

A. Offline environments cannot be upgraded
B. Administrators can create an offline VCF depot that mirrors the Broadcom online depot for bundle downloads
C. A USB drive is used to transfer updates
D. VCF Support manually applies updates via remote session

**Correct Answer: B**

VCF 9.0 supports offline depots that mirror the Broadcom online depot. Administrators download bundles to an intermediary system and populate the offline depot, enabling lifecycle management in air-gapped environments without direct internet connectivity.

---

**Question 84.** An administrator needs to configure vSAN encryption for a workload domain. Where is this setting managed?

A. SDDC Manager encryption settings
B. vCenter Server vSAN cluster settings
C. NSX encryption policies
D. VCF Operations security dashboard

**Correct Answer: B**

vSAN encryption (data-at-rest and data-in-transit) is configured through vCenter Server at the vSAN cluster level. The vCenter Server manages the encryption key provider (KMS) integration and encryption policy settings for the vSAN cluster.

---

### Section 5: Troubleshoot and Optimize (Questions 85--120)

**Question 85.** A VCF deployment via Cloud Builder fails during the network validation phase. What is the most likely cause?

A. Insufficient storage on ESXi hosts
B. DNS forward or reverse records are missing or incorrect for management components
C. The VCF license key has expired
D. vSAN disks have not been formatted

**Correct Answer: B**

Network validation failures during Cloud Builder deployment are most commonly caused by missing or incorrect DNS records. Cloud Builder validates both forward (A) and reverse (PTR) records for all management components. Missing records cause validation to fail before any deployment begins.

---

**Question 86.** An administrator encounters a "bundle download failed" error in SDDC Manager. What should they investigate first?

A. Whether vSAN has sufficient capacity
B. Whether SDDC Manager has connectivity to the Broadcom depot or offline depot and whether proxy settings are correct
C. Whether NSX Manager is operational
D. Whether VCF Operations is running

**Correct Answer: B**

Bundle download failures are typically caused by connectivity issues between SDDC Manager and the Broadcom online depot (or configured offline depot). Check internet connectivity, proxy configurations, DNS resolution, and firewall rules allowing SDDC Manager to reach the depot URL.

---

**Question 87.** A vSAN health check reports a "vSAN object health - Objects inaccessible" alarm. What does this indicate?

A. A storage policy compliance issue that requires no action
B. One or more vSAN objects cannot be accessed, likely due to host or disk failures exceeding the FTT tolerance
C. The vSAN license has expired
D. Network congestion on the management network

**Correct Answer: B**

Inaccessible vSAN objects indicate that component failures have exceeded the Failures to Tolerate (FTT) level configured in the storage policy. This typically occurs when multiple hosts or disk groups fail simultaneously, and the object's data replicas are no longer available.

---

**Question 88.** An ESXi host in a VCF workload domain loses connectivity to the vCenter Server. Which log should the administrator check first?

A. /var/log/vpxa.log on the ESXi host
B. /var/log/sddc-manager.log
C. NSX Manager system log
D. VCF Operations alert log

**Correct Answer: A**

The vpxa.log on the ESXi host records the vCenter agent's communication with vCenter Server. This is the primary log for diagnosing host-to-vCenter connectivity issues, including authentication failures, network timeouts, and certificate problems.

---

**Question 89.** An NSX overlay tunnel between two ESXi hosts is down. What is the most common root cause?

A. vSAN disk failure
B. MTU mismatch on the physical network between the host TEP interfaces
C. DRS misconfiguration
D. VCF Automation service disruption

**Correct Answer: B**

NSX overlay tunnels require jumbo frames (MTU 1600+, typically 9000) on the physical network path between host Tunnel Endpoints (TEPs). If any physical switch or router in the path has a lower MTU, tunnel traffic is dropped due to fragmentation, causing tunnel failures.

---

**Question 90.** A VCF upgrade pre-check fails with a "compatibility matrix mismatch" error. What does this mean?

A. The hardware is not on the VCF HCL
B. The target upgrade version is not compatible with the current versions of one or more VCF components
C. The license key does not support the target version
D. The SDDC Manager database is corrupted

**Correct Answer: B**

A compatibility matrix mismatch means that the target upgrade version requires dependent components to be at specific versions that the current environment does not meet. The administrator must upgrade prerequisite components first or select a different upgrade path.

---

**Question 91.** An administrator receives alerts about certificate expiration for VCF management components. What is the recommended action?

A. Ignore the alerts until the certificates actually expire
B. Use SDDC Manager certificate management workflows to rotate certificates before expiration
C. Restart all management components to refresh certificates
D. Redeploy the management domain

**Correct Answer: B**

Certificates should be rotated before expiration using SDDC Manager's certificate management workflows. Expired certificates cause authentication failures, SSO disruptions, and communication breakdowns between VCF components that are far more disruptive to resolve than proactive rotation.

---

**Question 92.** A VM in a VCF workload domain cannot reach the external network. The VM can communicate with other VMs on the same NSX segment. Where should the administrator investigate?

A. The distributed firewall rules on the VM's segment
B. The Tier-0 gateway configuration and its BGP/routing peering with the physical network
C. The vSAN network configuration
D. The VCF Automation network policy

**Correct Answer: B**

Since the VM can communicate within its segment (east-west works), the issue is with north-south routing. The Tier-0 gateway handles external routing, so the administrator should verify Tier-0 uplink configuration, BGP/OSPF peering status with physical routers, and NAT rules.

---

**Question 93.** SDDC Manager shows a workload domain in an "ERROR" state after a failed operation. What is the correct remediation approach?

A. Delete the workload domain and recreate it
B. Review the SDDC Manager task logs to identify the failed step, remediate the root cause, and retry the operation
C. Restart the SDDC Manager appliance
D. Contact VMware support immediately without attempting any remediation

**Correct Answer: B**

When a workload domain enters an ERROR state, the administrator should review SDDC Manager task logs to identify exactly which step failed and why. After remediating the root cause, the operation can be retried. Deleting the domain or restarting SDDC Manager are not appropriate first responses.

---

**Question 94.** A vSAN cluster in a VCF environment shows "Disk group degraded" status. What does this indicate?

A. A disk within the disk group has failed or been removed
B. The vSAN network is experiencing congestion
C. The cluster has exceeded its licensed capacity
D. DRS has placed too many VMs on a single host

**Correct Answer: A**

A degraded disk group indicates that one or more disks within the group have failed, been removed, or are experiencing errors. vSAN will begin rebuilding data onto remaining healthy components, but capacity and redundancy are reduced until the disk is replaced and the rebuild completes.

---

**Question 95.** An administrator notices high latency on the vSAN network. Which factor should they investigate first?

A. CPU utilization on the ESXi hosts
B. Network bandwidth and congestion on the vSAN-dedicated network adapters and physical switches
C. VCF Operations dashboard configuration
D. NSX firewall rule count

**Correct Answer: B**

vSAN latency is most commonly caused by network issues on the vSAN traffic path. Investigate bandwidth utilization on the vSAN NICs, physical switch port congestion, and any shared bandwidth situations where vSAN traffic competes with other traffic types.

---

**Question 96.** A VCF Identity Broker appliance is unreachable. What is the immediate impact?

A. ESXi hosts will lose network connectivity
B. Authentication to VCF components may fail if the Identity Broker is the sole authentication source
C. vSAN will stop replicating data
D. SDDC Manager will automatically shut down

**Correct Answer: B**

If the VCF Identity Broker is the sole authentication source (or the only healthy node in the cluster), users and services may be unable to authenticate to VCF components. This highlights the importance of deploying the Identity Broker in a three-node HA configuration for production environments.

---

**Question 97.** An NSX Distributed Firewall rule is not being applied to a specific VM. What should the administrator check?

A. Whether the VM is powered on
B. Whether the VM's security group membership and rule scope are correctly configured
C. Whether vSAN is operational
D. Whether VCF Automation is connected

**Correct Answer: B**

Distributed Firewall rules are applied based on security group membership and rule scope. If a VM is not in the correct security group or the rule's scope does not include the VM's segment, the rule will not be applied even if the VM is powered on and connected.

---

**Question 98.** An administrator attempts to commission new ESXi hosts in SDDC Manager, but the commissioning fails. What is the most likely cause?

A. The hosts do not meet the VCF hardware compatibility list requirements
B. The host network configuration (management IP, DNS, NTP) does not match the expected SDDC Manager configuration
C. VCF Operations is not monitoring the hosts
D. The hosts already have VMs running on them

**Correct Answer: B**

Host commissioning failures typically result from mismatched network configurations. The host's management IP, subnet, gateway, DNS, and NTP settings must match what SDDC Manager expects. Mismatches in any of these settings cause validation failures during commissioning.

---

**Question 99.** A VCF upgrade is stuck at 80% completion. What should the administrator do?

A. Cancel the upgrade and start over
B. Check SDDC Manager task logs to identify which component or step is blocked, and investigate the specific failure
C. Power off all hosts and restart the upgrade
D. Roll back the entire VCF environment to the previous version

**Correct Answer: B**

When an upgrade stalls, the administrator should examine SDDC Manager task logs to determine exactly which step is blocked. Common causes include network timeouts, component health issues, or prerequisite failures that were not caught by pre-checks. Understanding the specific failure point enables targeted remediation.

---

**Question 100.** A tenant reports that their VMs deployed through VCF Automation are not receiving the correct network configuration. What should the administrator investigate?

A. The physical switch VLAN configuration
B. The VCF Automation network profile and associated NSX segment configuration for the tenant organization
C. The vSAN storage policy assigned to the VMs
D. The DRS affinity rules for the cluster

**Correct Answer: B**

VM network configuration in VCF Automation is driven by network profiles and their associated NSX segments. If VMs are not receiving correct network settings, the administrator should verify the network profile configuration, NSX segment settings, and the mapping between the tenant organization and the correct network profiles.

---

**Question 101.** An SDDC Manager pre-check for an upgrade reports a "password expiration" warning. What does this indicate?

A. The administrator's portal password needs to be changed
B. One or more VCF component service account passwords are approaching expiration and should be rotated before the upgrade
C. The VCF license is expiring
D. The SSL certificates are about to expire

**Correct Answer: B**

Password expiration warnings during pre-checks indicate that VCF component service account passwords (ESXi root, vCenter service accounts, NSX admin, etc.) are nearing expiration. These should be rotated through SDDC Manager's password management workflows before proceeding with the upgrade.

---

**Question 102.** A vSAN stretched cluster in a VCF environment experiences a site failure. What determines which site the VMs will continue running on?

A. The site with the most ESXi hosts
B. The site designated as the preferred site in the vSAN stretched cluster configuration and the witness host vote
C. The site with the most available storage
D. Random selection by vSphere HA

**Correct Answer: B**

In a vSAN stretched cluster, the preferred site designation and the witness host vote determine which site retains VM access during a site failure. The witness host breaks the tie by voting for the preferred site, ensuring predictable failover behavior.

---

**Question 103.** An administrator notices that VCF Operations dashboards show stale data. What is the likely cause?

A. The dashboards need to be manually refreshed by clicking a button
B. The VCF Operations data collection adapter for vCenter is disconnected or experiencing errors
C. The ESXi hosts are running out of disk space
D. NSX Manager is not forwarding telemetry data

**Correct Answer: B**

Stale data in VCF Operations dashboards indicates a data collection issue. The most common cause is a disconnected or errored vCenter adapter, which is the primary data source. Check the adapter status, credentials, and network connectivity between VCF Operations and the affected vCenter.

---

**Question 104.** A newly deployed NSX Edge node in a VCF environment is not establishing BGP peering with the physical router. What should the administrator verify?

A. vSAN disk health on the Edge node's host
B. BGP configuration (AS numbers, neighbor IP addresses, timers) and network connectivity on the Edge node uplink
C. VCF Automation deployment status
D. SDDC Manager certificate validity

**Correct Answer: B**

BGP peering failures are caused by misconfigured BGP parameters (AS number mismatch, incorrect neighbor IP, authentication key mismatch), network connectivity issues on the Edge uplink interface, or physical switch port configuration errors. Verify both the NSX Edge BGP settings and the physical router configuration.

---

**Question 105.** An administrator wants to optimize VM placement across a VCF cluster. Which vSphere feature should they configure?

A. vSAN storage policies
B. DRS rules and resource pools with appropriate shares, reservations, and limits
C. NSX load balancer
D. VCF Automation placement policies only

**Correct Answer: B**

DRS (Distributed Resource Scheduler) optimizes VM placement across cluster hosts based on resource utilization. Configuring DRS rules (affinity/anti-affinity), resource pools, and setting appropriate shares, reservations, and limits ensures optimal VM placement aligned with workload requirements.

---

**Question 106.** A VCF deployment fails with a "vSAN disk claim" error during Cloud Builder bring-up. What is the most likely issue?

A. The ESXi hosts do not have enough memory
B. The disks on the ESXi hosts are not eligible for vSAN (existing partitions, incompatible controller, or HCL issues)
C. The management network VLAN is misconfigured
D. Cloud Builder has insufficient disk space

**Correct Answer: B**

vSAN disk claim failures occur when disks are not eligible for vSAN use. Common causes include existing partitions on the disks, an incompatible storage controller, disks not on the vSAN HCL, or disks being claimed by other services. Verify disk eligibility and clear any existing partitions.

---

**Question 107.** An administrator needs to troubleshoot intermittent connectivity between VMs on different NSX segments in a VCF environment. What tool should they use?

A. SDDC Manager SoS utility
B. NSX Traceflow to trace the packet path between the VMs
C. VCF Automation network diagnostics
D. vSAN health check

**Correct Answer: B**

NSX Traceflow injects a trace packet into the data path and tracks it through every hop (distributed switch, distributed firewall, logical router, transport node), showing exactly where packets are being dropped or modified. This is the primary diagnostic tool for inter-segment connectivity issues.

---

**Question 108.** A VCF environment is experiencing slow VM provisioning through VCF Automation. What should the administrator check first?

A. vSAN rebalancing status
B. VCF Automation service health, resource availability in the target cluster, and content library connectivity
C. NSX Edge node performance
D. VCF Operations alert thresholds

**Correct Answer: B**

Slow VM provisioning can be caused by VCF Automation service issues, insufficient resources in the target cluster (preventing placement), or content library problems (slow template downloads). Check VCF Automation service health first, then verify resource availability and content library connectivity.

---

**Question 109.** An ESXi host in a VCF cluster enters a "not responding" state in vCenter. What is the first troubleshooting step?

A. Decommission the host from SDDC Manager
B. Verify physical connectivity and check whether the host is reachable via DCUI, SSH, or out-of-band management (iLO/iDRAC)
C. Restart VCF Operations
D. Remove the host from the NSX transport zone

**Correct Answer: B**

A host showing "not responding" in vCenter may be experiencing network issues, hardware failure, or a PSOD. The first step is to determine physical status: can you reach the host via its console (DCUI), SSH, or out-of-band management? This determines whether the issue is a management network problem or a host-level failure.

---

**Question 110.** A VCF environment experiences a full SDDC Manager failure. What is the recovery procedure?

A. Redeploy VCF from scratch using Cloud Builder
B. Restore SDDC Manager from the most recent backup to an SFTP server
C. Promote a workload domain vCenter to serve as SDDC Manager
D. The environment cannot be recovered without SDDC Manager

**Correct Answer: B**

SDDC Manager recovery relies on the backups created through its built-in backup workflows. The SDDC Manager appliance is redeployed and then restored from the most recent backup stored on the SFTP server, recovering the database, configuration, and inventory.

---

**Question 111.** An administrator observes that a VCF Automation deployment of a VM fails with a "placement failed" error. What is the most likely cause?

A. The VM template is corrupted
B. No cluster in the targeted compute resource has sufficient capacity (CPU, memory, storage) to satisfy the deployment request
C. NSX is unavailable
D. The VCF license has expired

**Correct Answer: B**

A "placement failed" error indicates that VCF Automation's placement engine could not find a suitable host or cluster with sufficient resources to deploy the VM. Verify that the target compute resource has available capacity and that placement policies are not overly restrictive.

---

**Question 112.** A vSAN cluster in VCF shows "active resync" operations consuming significant bandwidth. What triggered this?

A. A scheduled backup operation
B. A host or disk failure caused vSAN to rebuild data objects on remaining healthy components
C. VCF Operations initiated a health check
D. A new storage policy was applied to all VMs simultaneously

**Correct Answer: B**

Active resync operations indicate that vSAN is rebuilding data objects after a component failure (host, disk group, or individual disk). vSAN replicates data to remaining healthy components to restore the configured FTT level, which can consume significant network and I/O bandwidth.

---

**Question 113.** An administrator needs to investigate a VCF deployment failure. Which log files are most relevant?

A. /var/log/vmware/vcf/sddc-manager-ui-app/
B. Cloud Builder /opt/vmware/bringup/logs/ and SDDC Manager /var/log/vmware/vcf/
C. vSAN Observer logs only
D. NSX syslog only

**Correct Answer: B**

For VCF deployment failures, the primary logs are the Cloud Builder bringup logs (located in /opt/vmware/bringup/logs/) and the SDDC Manager logs (under /var/log/vmware/vcf/). These logs contain detailed step-by-step deployment progress and error information.

---

**Question 114.** An administrator notices that VCF Operations capacity projections show a workload domain running out of CPU in 30 days. What action should they take?

A. Immediately add more hosts without analysis
B. Validate the projection against actual workload trends, then plan capacity expansion by commissioning and adding hosts through SDDC Manager
C. Reduce the number of VMs by migrating them to other domains
D. Increase the DRS threshold to pack more VMs per host

**Correct Answer: B**

VCF Operations capacity projections should be validated against actual workload trends to confirm accuracy. If confirmed, the administrator should plan a capacity expansion through the proper SDDC Manager workflow: commission new hosts and add them to the cluster, rather than making reactive changes without analysis.

---

**Question 115.** A distributed firewall rule in NSX is blocking legitimate traffic. How can the administrator identify the specific rule causing the blockage?

A. Disable all firewall rules temporarily
B. Use NSX flow monitoring and rule hit count analysis to identify which rule is matching and dropping the traffic
C. Check the vSAN health dashboard
D. Review the SDDC Manager upgrade logs

**Correct Answer: B**

NSX provides flow monitoring and rule hit count statistics that show exactly which rules are matching specific traffic flows. By analyzing these statistics and using Traceflow, the administrator can identify the blocking rule and modify it without disabling the entire firewall.

---

**Question 116.** A VCF brownfield conversion fails during the NSX preparation phase. What should the administrator investigate?

A. Whether the vSAN license is valid
B. Whether the existing network configuration conflicts with NSX requirements (MTU, VLAN, IP pool) and whether ESXi hosts meet NSX compatibility requirements
C. Whether VCF Operations is installed
D. Whether Cloud Builder is still running

**Correct Answer: B**

NSX preparation failures during brownfield conversion typically stem from network configuration conflicts (insufficient MTU for TEPs, VLAN conflicts, IP pool exhaustion) or ESXi host compatibility issues (unsupported NIC drivers, missing VIBs, or version incompatibilities).

---

**Question 117.** An administrator needs to optimize vSAN performance in a VCF workload domain. Which action would have the most significant impact?

A. Increasing the number of NSX segments
B. Ensuring vSAN traffic uses dedicated high-bandwidth NICs (25 GbE+) and is not sharing bandwidth with other traffic types
C. Adding more workload domains
D. Increasing the DRS migration threshold

**Correct Answer: B**

vSAN performance is most significantly impacted by network bandwidth and latency. Ensuring vSAN traffic has dedicated high-bandwidth NICs (25 GbE or higher) and is not sharing bandwidth with management, vMotion, or other traffic types provides the most substantial performance improvement.

---

**Question 118.** A VCF upgrade completes for all components except one NSX Manager node, which remains at the old version. What should the administrator do?

A. Ignore the node since two out of three are upgraded
B. Check the NSX Manager cluster status, review upgrade logs for the failed node, and retry the upgrade for that specific node
C. Decommission the entire NSX Manager cluster
D. Roll back all components to the previous version

**Correct Answer: B**

An NSX Manager cluster with mixed versions is in an inconsistent state that should be resolved. Review the upgrade logs for the failed node to identify the specific error, verify the node's health, and retry the upgrade. The three-node cluster can tolerate one node being down during the upgrade.

---

**Question 119.** An administrator notices increased latency for VMs after enabling vSAN encryption. What is the expected behavior?

A. This indicates a misconfiguration that should be investigated
B. Some performance overhead is expected due to encryption processing; verify that the overhead is within acceptable bounds and that the KMS connection is stable
C. vSAN encryption does not affect performance
D. Latency increases only affect read operations, never writes

**Correct Answer: B**

vSAN encryption introduces computational overhead for encrypting and decrypting data, which results in some performance impact. A modest latency increase is expected behavior. The administrator should verify that the overhead is within acceptable bounds and that the KMS connection is stable, as KMS issues can cause disproportionate performance degradation.

---

**Question 120.** A VCF Fleet Management Console shows one Instance as "disconnected." What is the impact and recommended action?

A. The disconnected Instance loses all functionality immediately
B. The Instance continues to operate independently but cannot be managed from the Fleet Console; investigate network connectivity between the Fleet management services and the Instance
C. All Instances in the Fleet are affected
D. VCF Automation automatically migrates workloads from the disconnected Instance

**Correct Answer: B**

A disconnected Instance continues to operate independently since it has its own SDDC Manager, vCenter, and NSX. However, Fleet-level management (centralized monitoring, VCF Automation, VCF Operations) cannot reach it. Investigate network connectivity between Fleet management services and the Instance's management network.

---

## Exam Day Strategy

### Before the Exam

**Schedule Wisely.** Book your exam for a Tuesday, Wednesday, or Thursday morning. Avoid Mondays (you are still ramping up) and Fridays (mental fatigue from the workweek). Morning slots are better because your cognitive function is at its peak.

**Night Before.** Stop studying by 6:00 PM. Review your one-page quick reference card one final time. Get seven to eight hours of sleep. Lay out your identification documents, confirmation email, and anything needed for the test center.

**Day Of.** Eat a balanced breakfast with protein and complex carbohydrates. Avoid excessive caffeine --- one cup of coffee is fine, three is counterproductive. Arrive at the test center 30 minutes early or, for online proctored exams, set up your testing environment 15 minutes before your appointment.

### During the Exam

**Time Management.** With 70 questions in 135 minutes (Administrator exam), you have roughly 1 minute and 55 seconds per question. Use this framework:
- **First pass (90 minutes):** Answer every question you are confident about. Mark uncertain questions for review.
- **Second pass (30 minutes):** Return to marked questions. Apply the process of elimination. Make your best choice.
- **Final pass (15 minutes):** Review any remaining flagged questions. Trust your initial instinct unless you have a specific reason to change your answer.

**Question Traps to Watch For**

1. **The "best" answer trap.** VMware exams frequently include answers that are technically correct but not the best answer for the scenario. Read the scenario carefully and choose the option that most directly and completely addresses the stated requirement.

2. **The scope mismatch trap.** Questions about Fleet-level operations have different correct answers than questions about Instance-level operations. Pay attention to the scope specified in the question.

3. **The terminology trap.** If you see "Aria Automation" in an answer choice, it is probably wrong --- VCF 9.0 uses "VCF Automation." Similarly, "VIDM" has been replaced by "VCF Identity Broker."

4. **The "do nothing" trap.** Some questions describe a normal situation and include an answer choice of "no action required." Do not be afraid to select this if the scenario describes expected behavior (like minor latency increase after enabling encryption).

5. **The order-of-operations trap.** Questions about workflows (deployment, upgrade, host addition) test whether you know the correct sequence. Commissioning before adding, pre-check before upgrade, backup before major changes.

### Mental Model for Architecture Questions

For questions that present a design scenario, apply this structured approach:

1. **Identify the primary requirement:** What is the question actually asking you to solve? Availability? Performance? Security? Cost?
2. **Eliminate constraint violators:** Remove answer choices that violate stated constraints (budget, hardware, compliance).
3. **Apply the principle of least complexity:** Among remaining valid options, the simpler design is almost always the intended correct answer.
4. **Verify component scope:** Ensure your answer places components at the correct level (Fleet vs. Instance vs. workload domain).

---

## Certification Quick Reference Card

### VCP-VCF Administrator (2V0-17.25)

| Field | Detail |
|---|---|
| Questions | 70 |
| Time | 135 minutes |
| Passing | 300 (scaled) |
| Cost | $250 ($125 with VMUG Advantage) |
| VCF Version | 9.0 |
| Prerequisite | None required (training recommended) |
| Validity | 36 months |
| Delivery | Pearson VUE |
| Focus | Deploy, configure, manage, operate VCF |
| Replaces | 2V0-11.25 |

### VCP-VCF Support (2V0-15.25)

| Field | Detail |
|---|---|
| Questions | 60 |
| Time | 135 minutes |
| Passing | 300 (scaled) |
| Cost | $250 ($125 with VMUG Advantage) |
| VCF Version | 9.0 |
| Prerequisite | None required (1 year support experience recommended) |
| Validity | 36 months |
| Delivery | Pearson VUE |
| Focus | Troubleshoot and support VCF environments |
| Replaces | Previous VCF Support exam |

### VCP-VCF Architect (2V0-13.25)

| Field | Detail |
|---|---|
| Questions | 60 |
| Time | 135 minutes |
| Passing | 300 (scaled) |
| Cost | $250 ($125 with VMUG Advantage) |
| VCF Version | 9.0 |
| Prerequisite | None required (3+ years design experience recommended) |
| Validity | 36 months |
| Delivery | Pearson VUE |
| Focus | Design and architect VCF solutions |
| Gateway to | VCAP, VCDX |

### Key Dates and Links

- Official certification portal: broadcom.com/support/education/vmware/certification
- Hands-on Labs: labs.hol.vmware.com
- VMUG Advantage: vmug.com/membership/vmug-advantage-membership
- VMware {code} Study Group: blogs.vmware.com/code (search VCP-VCF Study Group)
- VCF Blog Certification Archives: blogs.vmware.com/cloud-foundation/certification-learning

---

## 50-Term Exam Prep Glossary

1. **Availability Zone (AZ):** A distinct physical location within a region that provides redundancy and fault isolation for VCF workloads.

2. **BGP (Border Gateway Protocol):** Dynamic routing protocol used by NSX Tier-0 gateways to exchange routing information with physical network infrastructure.

3. **Cloud Builder:** The VMware appliance used for initial greenfield VCF deployment, automating the bring-up of the management domain.

4. **Cluster:** A group of ESXi hosts within a workload domain that share compute, storage (vSAN), and network resources.

5. **Commissioning:** The SDDC Manager process of preparing and registering a new ESXi host for use in a VCF environment, including VIB installation and configuration validation.

6. **Content Library:** A VCF Automation repository that stores reusable artifacts (VM templates, ISOs, OVAs, scripts) for self-service consumption by tenant organizations.

7. **Conversion (Brownfield):** The process of bringing an existing vSphere environment under VCF management without rebuilding the infrastructure from scratch.

8. **Decommissioning:** The SDDC Manager process of removing an ESXi host from VCF management, including workload migration, VIB removal, and inventory cleanup.

9. **Diagnostic Findings:** A VCF Operations feature that performs real-time checks for VMSAs, CVEs, and best practice deviations, categorized by severity.

10. **Distributed Firewall (DFW):** An NSX feature that applies firewall rules at the virtual NIC level of each workload, enabling micro-segmentation without dedicated firewall appliances.

11. **DRS (Distributed Resource Scheduler):** A vSphere feature that balances compute workloads across hosts in a cluster by automatically migrating VMs based on resource utilization.

12. **Edge Node:** An NSX virtual appliance that provides north-south routing, NAT, load balancing, and VPN services at the boundary between overlay and physical networks.

13. **ESXi:** The VMware bare-metal hypervisor that runs on physical hosts and provides the compute virtualization layer in VCF.

14. **Failures to Tolerate (FTT):** A vSAN storage policy setting that determines how many component failures a storage object can withstand. FTT=1 requires two copies; FTT=2 requires three.

15. **Fleet:** A VCF 9.0 construct that defines the governance and lifecycle scope for shared management services (VCF Automation, VCF Operations, VCF Identity Broker) across one or more Instances.

16. **Fleet Management Console:** The centralized interface for managing and monitoring all VCF Instances within a Fleet.

17. **Gateway Firewall:** An NSX firewall that applies rules at the Tier-0 or Tier-1 gateway level, filtering traffic as it enters or exits the overlay network.

18. **HA (High Availability):** A vSphere feature that automatically restarts VMs on surviving hosts when a host failure occurs, ensuring workload availability.

19. **HCL (Hardware Compatibility List):** The Broadcom-maintained list of certified hardware components (servers, NICs, storage controllers, disks) validated for use with VCF.

20. **IaaS Policy:** A VCF Automation policy that governs resource allocation, placement, naming, and consumption limits for tenant organizations.

21. **Identity Broker (vIDB):** The VCF 9.0 component that provides centralized authentication services, replacing VIDM. Deployed as embedded (on vCenter) or as a standalone three-node appliance cluster.

22. **Instance:** A VCF 9.0 construct representing a discrete infrastructure footprint with its own SDDC Manager, vCenter Server, and NSX control plane. Instances exist within a Fleet.

23. **Lifecycle Management (LCM):** The SDDC Manager capability that orchestrates patching, upgrading, and configuration management of all VCF components within an Instance.

24. **Management Domain:** The foundational workload domain in a VCF Instance that hosts management infrastructure components (vCenter, SDDC Manager, NSX Manager).

25. **Micro-segmentation:** A security approach using NSX Distributed Firewall to apply granular firewall policies at the individual workload level, enforcing zero-trust networking.

26. **NSX:** The VMware network virtualization and security platform that provides overlay networking, distributed firewalling, load balancing, and VPN capabilities in VCF.

27. **NSX Manager:** The management plane component for NSX, deployed as a three-node cluster in VCF for high availability.

28. **Offline Depot:** A locally hosted mirror of the Broadcom update depot, enabling lifecycle management operations in air-gapped environments.

29. **Organization (Org):** A VCF Automation construct representing a tenant unit with its own identity, resource allocation, policies, and self-service capabilities.

30. **Overlay Network:** A virtual network created by NSX using GENEVE encapsulation over the physical network, enabling logical network segmentation independent of the physical topology.

31. **Pre-check Validation:** An SDDC Manager function that verifies environment readiness before lifecycle operations by checking compatibility, health, connectivity, and prerequisites.

32. **Provider Portal:** The VCF Automation interface used by infrastructure administrators (providers) to manage cloud accounts, resources, access, and identity for tenant organizations.

33. **SDDC Manager:** The lifecycle management engine for a VCF Instance, responsible for deployment, patching, upgrading, certificate management, and host commissioning/decommissioning.

34. **Segment:** An NSX logical network that provides Layer 2 connectivity for workloads, equivalent to a port group on a distributed switch but with overlay networking capabilities.

35. **SoS Utility (Support and Serviceability):** A VCF diagnostic tool that collects logs, configuration data, and health information from VCF components into support bundles for troubleshooting.

36. **Stretched Cluster:** A vSAN cluster that spans two physical sites with a witness host at a third site, providing site-level fault tolerance for workloads.

37. **Supervisor:** The vSphere component (formerly Tanzu) that enables native Kubernetes cluster deployment and management within a VCF environment.

38. **TEP (Tunnel Endpoint):** An IP address assigned to an ESXi transport node for encapsulating and decapsulating NSX overlay traffic using GENEVE protocol.

39. **Tier-0 Gateway:** An NSX logical router that provides north-south routing between the overlay network and the physical network, handling BGP/OSPF peering, NAT, and external connectivity.

40. **Tier-1 Gateway:** An NSX logical router that provides east-west routing between segments within a tenant or workload domain, connecting to a Tier-0 gateway for external access.

41. **Traceflow:** An NSX diagnostic tool that traces the path of a packet through the virtual network, showing each hop and identifying where packets are dropped or modified.

42. **Transport Zone:** An NSX construct that defines the scope of an overlay or VLAN-based network, determining which transport nodes can communicate with each other.

43. **Upgrade Bundle:** A package downloaded through SDDC Manager containing component updates, dependency information, and upgrade orchestration logic for VCF lifecycle management.

44. **VCF Automation:** The Fleet-level service (formerly Aria Automation) that provides self-service infrastructure provisioning, multi-tenancy, and organization management.

45. **VCF Operations:** The Fleet-level service (formerly Aria Operations) that provides monitoring, alerting, capacity management, compliance reporting, and chargeback capabilities.

46. **vCenter Server:** The centralized management platform for vSphere environments, responsible for host management, VM lifecycle, DRS, HA, and resource pool configuration within a VCF Instance.

47. **Virtual Private Cloud (VPC):** A VCF 9.0 consumption model that provides tenants with isolated network and compute resources within the VCF platform.

48. **Workload Domain:** A logical unit within a VCF Instance comprising one or more vSphere clusters with shared networking (NSX) and storage (vSAN) configurations, designed for specific workload types.

49. **vSAN:** VMware's hyper-converged storage platform that aggregates local disks across ESXi hosts into a shared datastore, providing software-defined storage for VCF workloads.

50. **Zero-Trust Security:** A security model where no implicit trust is granted based on network location, implemented in VCF through NSX micro-segmentation with default-deny firewall policies.

---

## Closing Thoughts

Certification is not the destination. It is the starting point. The credential gets you in the room. What you do once you are there --- the designs you create, the problems you solve, the environments you build and operate --- that is what defines your career.

I have seen hundreds of professionals transform their careers through VMware certification. The ones who get the most value are not the ones who memorize the most facts. They are the ones who internalize the concepts deeply enough to apply them in novel situations. They understand why VCF is architected the way it is, not just how to click through the workflows.

If you have made it through this chapter --- through the blueprints, the practice questions, the study plans, and the glossary --- you are already ahead of most candidates. Now put that knowledge to work. Build a lab, complete the HOL labs, join the study group, and schedule your exam. Set a date, commit to it, and execute.

I will see you on the other side --- certified, confident, and ready to build the next generation of private cloud infrastructure.

*--- Pranav Patel, Technical Account Manager, VMware by Broadcom*


---

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


---

# Chapter 14: Reference and Appendix

This chapter serves as the definitive reference companion for VMware Cloud Foundation 9. Whether you need a quick definition during a design session, the exact build number for a support case, or the right PowerCLI cmdlet for an automation script, the material that follows is organized for rapid lookup. Keep it bookmarked, keep it printed, keep it close.

---

## VCF 9 Glossary of Terms

The following glossary covers the essential terminology used throughout VMware Cloud Foundation 9.0 and this book. Terms are listed alphabetically with concise definitions.

**Affinity Rule** — A DRS policy that constrains virtual machine placement, either keeping VMs together on the same host (affinity) or separating them across hosts (anti-affinity) to satisfy availability or licensing requirements.

**Aria Operations** — See VCF Operations. The former name for the monitoring and analytics platform, now unified under the VCF Operations brand in VCF 9.

**Aria Suite Lifecycle Manager** — See VCF Operations Lifecycle Manager. The former lifecycle management tool for Aria products, rebranded and folded into VCF Operations management in VCF 9.

**Auto Deploy** — A PXE-based provisioning mechanism for ESXi hosts, deprecated in VCF 9.0 in favor of VCF Installer workflows and image-based lifecycle management.

**Availability Zone (AZ)** — A fault-isolation boundary within a VCF deployment, typically mapped to a physical rack, row, or data center to protect workloads from localized failures.

**Baselines (vLCM)** — The legacy vSphere Update Manager approach to patching ESXi hosts using baseline groups. Removed in VCF 9.0; replaced entirely by vSphere Lifecycle Manager image-based workflows.

**Bill of Materials (BOM)** — The validated, tested matrix of software component versions shipped with each VCF release. All components in a BOM are guaranteed interoperable.

**Brownfield Deployment** — The process of converting an existing vSphere environment into a managed VCF instance, as opposed to a greenfield (net-new) deployment.

**Bring-Up** — The initial automated deployment process that creates the management domain, including the first vCenter, NSX Manager cluster, SDDC Manager, and supporting services.

**Certificate Authority (CA)** — The trusted entity that signs TLS certificates for VCF management components. VCF supports both the embedded VMware Certificate Authority (VMCA) and external enterprise CAs.

**Cloud Builder** — The deployment appliance used in VCF 5.x and earlier to orchestrate the initial bring-up of a management domain. Replaced by the VCF Installer in VCF 9.0.

**Cluster** — A logical grouping of ESXi hosts within a workload domain that share common compute, storage, and networking resources. The fundamental unit of capacity in VCF.

**Cluster Image** — The desired-state software specification for all hosts in a vSphere cluster, including ESXi version, drivers, and firmware. Managed through vSphere Lifecycle Manager.

**Compliance** — The state where a host or cluster matches its desired software image or configuration profile. Non-compliant resources require remediation.

**Configuration Profile** — A cluster-level configuration specification that replaces the deprecated Host Profiles capability, allowing administrators to define and enforce ESXi host settings declaratively.

**Core-Based Licensing** — The licensing model for VCF 9, where entitlement is calculated per physical CPU core rather than per socket or per VM.

**Datastore** — A logical storage container visible to ESXi hosts. In VCF 9, datastores are typically vSAN, VMFS on Fibre Channel, or NFS. vVols datastores are deprecated.

**Desired State Configuration** — The operational model in VCF 9 where infrastructure is defined as a target specification and the platform continuously reconciles actual state toward that target.

**Distributed Resource Scheduler (DRS)** — The vSphere feature that automatically balances compute workloads across hosts in a cluster by migrating VMs via vMotion.

**Distributed Switch (VDS)** — A centrally managed virtual switch that spans all hosts in a cluster, providing consistent networking policy and simplifying network operations.

**Domain** — See Workload Domain.

**Edge Cluster** — A group of NSX Edge nodes that provide north-south routing, NAT, VPN, and load balancing services at the boundary between overlay and physical networks.

**Edge Transport Node** — An NSX Edge virtual appliance or bare-metal node that participates in the NSX data plane, handling traffic between logical and physical networks.

**Enhanced Linked Mode (ELM)** — The former capability allowing multiple vCenter Server instances to share a common SSO domain for unified management. Deprecated in VCF 9.0; replaced by VCF Operations grouping.

**ESXi** — The bare-metal hypervisor that runs directly on server hardware, providing the compute virtualization layer in VCF. Version 9.0.0.0 ships with VCF 9.0.

**Fault Domain** — A logical grouping of hosts within a vSAN cluster that maps to a physical failure boundary such as a rack, ensuring data availability when an entire domain fails.

**Fibre Channel** — A high-speed network protocol used for storage area networking. VCF 9 supports Fibre Channel as a supplementary storage option alongside vSAN.

**Fleet** — The highest-level organizational construct in VCF 9. A fleet encompasses all VCF instances managed by a single set of fleet-level management components (VCF Operations and VCF Automation).

**Fleet Management** — The centralized operational layer in VCF 9 that provides unified lifecycle management, monitoring, and automation across all VCF instances in a fleet.

**Geneve** — The network encapsulation protocol used by NSX to create overlay networks. Geneve tunnels transport Layer 2 frames across a Layer 3 physical network.

**Greenfield Deployment** — A net-new VCF installation where no prior vSphere infrastructure exists. The VCF Installer creates the entire stack from bare metal.

**Hardening** — The process of applying security configurations to VCF components to reduce attack surface, guided by the VCF STIG documentation and security baselines.

**Hardware Compatibility List (HCL)** — The Broadcom-maintained list of validated server, storage, and network hardware for use with VCF. Critical for supportability.

**High Availability (HA)** — The vSphere capability that automatically restarts virtual machines on surviving hosts when a host failure is detected.

**Host Transport Node** — An ESXi host prepared for NSX overlay networking by installing NSX kernel modules and configuring transport node profiles.

**Identity Federation** — The recommended authentication model in VCF 9 that integrates vCenter SSO with external identity providers (such as Okta, Azure AD, or ADFS) using SAML or OIDC, replacing the deprecated IWA.

**Image-Based Lifecycle** — The VCF 9 approach to host patching and upgrades where a complete software image (ESXi plus drivers and firmware) is defined, compared against running hosts, and remediated as a unit.

**Integrated Windows Authentication (IWA)** — A legacy SSO authentication mechanism removed in VCF 9.0. Administrators must migrate to LDAPS or identity federation before upgrading.

**JSON Specification** — The declarative input format used by SDDC Manager and the VCF Installer to define deployment parameters for domains, clusters, and hosts.

**Kubernetes** — The container orchestration platform integrated into VCF through vSphere with Tanzu (Supervisor clusters), enabling both VM and container workloads on the same infrastructure.

**Lifecycle Management** — The coordinated process of patching, upgrading, and maintaining all VCF components (ESXi, vCenter, NSX, SDDC Manager, VCF Operations) through a validated, sequenced workflow.

**Logical Segment** — An NSX overlay network segment that provides Layer 2 connectivity to workloads independent of the physical network topology.

**Management Domain** — The first and foundational domain in a VCF instance. It hosts all management components (vCenter, NSX Manager, SDDC Manager) and fleet-level services (VCF Operations, VCF Automation). It does not run tenant workloads.

**Management Plane** — The collective set of management services (SDDC Manager, vCenter, NSX Manager) that control and configure the VCF instance.

**Multi-Instance** — A VCF 9 deployment model where multiple VCF instances are grouped under a single fleet for centralized management while maintaining independent lifecycle and failure domains.

**Network I/O Control (NIOC)** — A VDS feature that classifies and prioritizes network traffic types (vMotion, vSAN, VM, management) to ensure bandwidth guarantees during contention.

**Network Pool** — A collection of VLANs reserved by SDDC Manager for assigning to workload domains during deployment, covering vMotion, vSAN, and host overlay transport traffic.

**NFS Datastore** — A network-attached storage datastore using the Network File System protocol, supported as supplementary storage in VCF 9 workload domains.

**NSX** — The network virtualization and security platform in VCF 9, providing overlay networking, micro-segmentation, distributed firewalling, load balancing, and VPN services. Version 9.0.0.0 ships with VCF 9.0.

**NSX Manager** — The centralized management appliance (deployed as a three-node cluster for production) that provides the API, UI, and control plane for NSX.

**NSX Policy Mode** — The single supported API and UI mode in NSX 9.0 for configuring networking and security resources. The legacy Manager mode and Manager API have been removed.

**Overlay Network** — A virtual network created by NSX that operates independently of the physical network, using Geneve encapsulation to provide tenant isolation and workload mobility.

**Per-Core Licensing** — See Core-Based Licensing.

**Policy API** — The declarative, intent-based NSX API that replaced the imperative Manager API. All new NSX configurations in VCF 9 must use the Policy API.

**PowerCLI** — See VCF PowerCLI.

**Precheck** — An automated validation step performed by SDDC Manager or VCF Operations before lifecycle operations (upgrades, expansions, domain deployments) to identify and surface potential issues.

**Principal Storage** — The primary storage type assigned to a workload domain at creation time. In most VCF deployments, this is vSAN.

**Private Cloud** — The end-state operational model delivered by VCF 9, where infrastructure is consumed as a service with self-service provisioning, metering, and policy-driven governance.

**Provider Portal** — The VCF Automation administrative interface used by infrastructure operators (providers) to configure organizations, resource allocation, and service offerings.

**SDDC Manager** — The lifecycle and operations management appliance at the heart of each VCF instance. It orchestrates domain deployments, host commissioning, lifecycle updates, and certificate management.

**Segment** — See Logical Segment.

**Service Account** — A non-interactive user account used by VCF components for inter-service communication, subject to credential rotation policies.

**Single Sign-On (SSO)** — The authentication service embedded in vCenter Server that provides centralized identity management for all VCF management components.

**Stretched Cluster** — A vSAN cluster that spans two physical sites with a witness host at a third site, providing active-active workload placement and automated failover for site-level resilience.

**Subscription License** — The VCF 9 license model that uses subscription-based license files instead of the legacy 25-character license keys. License files are applied through VCF Operations.

**Supervisor Cluster** — A vSphere cluster enabled with Kubernetes capabilities through the Supervisor, allowing operators to run both VMs and Kubernetes pods on the same infrastructure.

**Tanzu Kubernetes Grid (TKG)** — The Kubernetes distribution integrated with vSphere Supervisor that provisions conformant Kubernetes clusters for application developers.

**Tenant** — An organizational entity in VCF Automation that consumes infrastructure resources through self-service portals, isolated from other tenants by policy and network boundaries.

**Tier-0 Gateway** — The NSX logical router that provides north-south connectivity between the overlay network and the physical network. Each workload domain typically has its own Tier-0 gateway.

**Tier-1 Gateway** — An NSX logical router connected to a Tier-0 gateway that provides routing and service insertion for individual network segments within a workload domain.

**Transit Gateway** — A new VCF 9 networking construct that simplifies connectivity between workload domains and external networks by centralizing routing policy.

**Transport Node Profile** — An NSX configuration template that defines overlay transport settings (TEP pool, uplink profile, transport zone membership) applied to ESXi hosts when they are prepared for NSX.

**Transport Zone** — An NSX construct that defines the span of an overlay or VLAN-backed network. Hosts in the same transport zone can communicate over the associated logical segments.

**Unified Appliance** — The consolidated NSX deployment model in VCF 9 where NSX Manager, controller, and central control plane functions run in a single appliance type.

**Upgrade Precheck** — See Precheck.

**VCF Automation** — The fleet-level automation and self-service platform in VCF 9, formerly known as vRealize Automation / Aria Automation. Provides multi-tenancy, catalog-based provisioning, and infrastructure-as-code capabilities.

**VCF Installer** — The new deployment tool in VCF 9 that replaces Cloud Builder. It handles greenfield bring-up, brownfield conversion, and initial management domain creation.

**VCF Instance** — A discrete VCF deployment comprising one management domain and zero or more workload domains, all managed by a single SDDC Manager. Multiple instances can be grouped into a fleet.

**VCF Operations** — The fleet-level monitoring, alerting, capacity management, and compliance platform in VCF 9. Replaces the former Aria Operations / vRealize Operations product line.

**VCF Operations for Logs** — The centralized log management component that collects, indexes, and analyzes logs from all VCF components for troubleshooting and audit purposes.

**VCF Operations for Networks** — The network monitoring and analytics component that provides flow analysis, topology visualization, and network troubleshooting for NSX-managed environments.

**VCF Operations Lifecycle Manager** — The component responsible for deploying, upgrading, and managing the lifecycle of VCF Operations components themselves.

**VCF PowerCLI** — The PowerShell-based command-line automation toolkit for VCF 9, formerly VMware PowerCLI. Includes modules for vSphere, NSX, SDDC Manager, VCF Operations, and VCF Automation management.

**VCF SDK** — The software development kit providing Java and Python libraries for programmatic interaction with VCF APIs, including auto-generated client bindings for SDDC Manager and VCF Operations.

**Virtual Distributed Switch** — See Distributed Switch (VDS).

**Virtual Private Cloud (VPC)** — A tenant-facing networking construct in VCF 9 that provides isolated network environments with self-service subnet creation and security policy management.

**vMotion** — The live migration technology that moves running virtual machines between hosts without downtime, used by DRS for load balancing and by maintenance mode operations.

**VMFS** — Virtual Machine File System, a clustered file system used on Fibre Channel and iSCSI storage for hosting VM files. Supported as supplementary storage in VCF 9.

**vSAN** — The hyperconverged storage platform integrated into ESXi that aggregates local disks across cluster hosts into a shared datastore. All-flash architectures are required in VCF 9; hybrid vSAN is no longer supported.

**vSAN ESA (Express Storage Architecture)** — The next-generation vSAN storage architecture optimized for NVMe devices, offering simplified configuration, improved performance, and storage efficiency.

**vSAN OSA (Original Storage Architecture)** — The traditional vSAN architecture using cache and capacity disk groups. Still supported in VCF 9 but ESA is the recommended path forward.

**vSphere Clustering Service (vCLS)** — Agent VMs formerly required to support DRS operations. Deprecated in VCF 9.0 and recommended to be placed in retreat mode. DRS and HA continue to function without vCLS VMs.

**vSphere Configuration Profiles** — The replacement for the deprecated Host Profiles, providing cluster-level declarative configuration management for ESXi hosts.

**vSphere Foundation (VVF)** — The entry-level VMware platform offering that provides core vSphere and vSAN capabilities without the full VCF management stack. Shares the same ESXi and vCenter versions as VCF 9.

**vSphere Lifecycle Manager (vLCM)** — The image-based lifecycle management tool embedded in vCenter that defines, validates, and remediates ESXi host software across clusters.

**vSphere Pods** — Lightweight VM-based containers that run directly on the ESXi hypervisor through the Supervisor, providing strong isolation with near-native container performance.

**vSphere Trust Authority** — A security feature for attestation of ESXi host integrity. Removed in VCF 9.0.

**Witness Host** — A lightweight ESXi appliance deployed at a third site in stretched cluster configurations to serve as the tiebreaker for vSAN quorum decisions.

**Workload Domain** — A logical unit of application-ready infrastructure comprising one or more vSphere clusters managed by a dedicated vCenter and NSX Manager pair. Workload domains provide lifecycle isolation, security boundaries, and independent scaling.

**Workload Management** — The vSphere capability that enables Supervisor clusters and Kubernetes workload support on VCF infrastructure.

---

## VCF 9.0 Bill of Materials — Component Version Reference

The following table documents the validated component versions shipped with VCF 9.0.0.0 (GA, June 2025). All components in this BOM have been tested for interoperability. Always verify against the official Broadcom release notes for the most current patch levels.

| Component | Version | Build Number |
|---|---|---|
| VCF Installer | 9.0.0.0 | 24781669 |
| SDDC Manager | 9.0.0.0 | 24703748 |
| ESXi | 9.0.0.0 | 24755229 |
| vCenter Server | 9.0.0.0 | 24755230 |
| NSX | 9.0.0.0 | 24733065 |
| vSAN | 9.0.0.0 | (integrated with ESXi 9.0) |
| VCF Automation | 9.0.0.0 | 24701403 |
| VCF Operations | 9.0.0.0 | 24695812 |
| VCF Operations Lifecycle Manager | 9.0.0.0 | 24695816 |
| VCF Operations Cloud Proxy | 9.0.0.0 | 24695833 |
| VCF Operations for Logs | 9.0.0.0 | (included in VCF Operations bundle) |
| VCF Operations for Networks | 9.0.0.0 | (included in VCF Operations bundle) |
| VCF PowerCLI | 9.0.0 | 24798382 |

**Subsequent releases:**

- **VCF 9.0.1.0** (September 2025): Incremental update with patched ESXi, vCenter, and NSX builds, plus VCF Operations enhancements.
- **VCF 9.0.2.0** (January 2026): Maintenance release with Fleet Manager improvements, lifecycle updates, and additional brownfield conversion capabilities.

Note that VCF 9 adopts unified versioning across all components. The individual product version numbers (such as "vSphere 9.0 U1" or "NSX 4.2") that existed in prior releases are eliminated. Every component simply carries the VCF release version.

---

## Certification Quick Reference Card

VMware Cloud Foundation certifications were restructured under Broadcom to align with the VCF-centric product strategy. The following table summarizes the current certification landscape.

### Foundation Level

| Certification | Exam Code | Focus |
|---|---|---|
| VCP-VCF Administrator | 2V0-11.25 | Day-to-day VCF operations, domain management, lifecycle, troubleshooting |
| VCP-VCF Architect | 2V0-13.25 | VCF solution design, sizing, deployment model selection |

### Advanced Level (VCAP)

| Certification | Exam Code | Focus |
|---|---|---|
| VCAP-VCF Administration | 3V0-11.26 | Advanced VCF administration, complex lifecycle scenarios, multi-domain operations |
| VCAP-VCF Design | 3V0-12.26 | Enterprise VCF architecture, multi-site design, capacity planning |
| VCAP-VCF Automation | 3V0-13.26 | VCF Automation, self-service operations, infrastructure-as-code, Kubernetes integration |

### Preparation Resources

- **Official exam guides**: Available at broadcom.com/support/education/vmware/certification
- **VMware {code} VCP-VCF Study Group**: Peer-led study sessions and shared resources
- **Hands-on Labs**: Free lab environments for practice (see Essential Resources below)
- **VMUG Advantage**: Includes 365-day evaluation licenses and exam discounts

### Certification Tips

1. VCF 9 exams emphasize fleet management, VCF Operations, and VCF Automation concepts that did not exist in prior vSphere-only certifications.
2. Hands-on experience with SDDC Manager, the VCF Installer, and VCF PowerCLI is essential.
3. Understand the full lifecycle: greenfield deployment, brownfield conversion, domain expansion, patching, and upgrade sequencing.
4. NSX Policy mode is the only testable NSX configuration model. Legacy Manager mode knowledge is no longer assessed.

---

## Essential Resource Links

The following resources represent the most valuable references for VCF 9 practitioners. Each link is annotated with its primary use case.

### Official Broadcom Documentation

- **VCF 9 Technical Documentation (TechDocs)** — techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0.html
  The authoritative source for all VCF 9 documentation: planning guides, deployment procedures, operations manuals, and release notes.

- **VCF 9.0 Release Notes** — techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-9-0-and-later/9-0/release-notes.html
  BOM details, what's new, known issues, and product support notes for every VCF 9.x release.

- **Configuration Maximums Tool** — configmax.broadcom.com
  Interactive tool for looking up supported limits for vCenter, ESXi, vSAN, NSX, and other components. Filter by product and VCF 9.0 release to find specific maximums for cluster sizes, VM counts, network scale, and storage limits.

- **VMware Compatibility Guide** — interopmatrix.broadcom.com
  Hardware and software interoperability verification. Essential before procurement and upgrade planning.

- **Broadcom Knowledge Base** — knowledge.broadcom.com
  Searchable repository of KBs, known issues, workarounds, and resolution procedures. The first stop for troubleshooting.

### Developer and Automation Resources

- **Broadcom Developer Portal** — developer.broadcom.com
  API documentation, SDK downloads, and OpenAPI specifications for SDDC Manager, VCF Operations, VCF Automation, and NSX.

- **VCF API Reference** — developer.broadcom.com/xapis/vmware-cloud-foundation-api/latest/
  Complete SDDC Manager REST API documentation with request/response examples.

- **VCF PowerCLI Portal** — developer.broadcom.com/powercli
  Module documentation, download links, and getting-started guides for VCF PowerCLI 9.0.

- **VCF SDK (Java and Python)** — developer.broadcom.com/sdks/vcf-api-specification/latest/
  Auto-generated client libraries for programmatic VCF management.

- **VCF API Changelog** — techdocs.broadcom.com (Release Notes > What's New > CLI, API, SDK)
  Consolidated summary of API additions, removals, and deprecations across VCF releases.

### Learning and Community

- **VMware Hands-on Labs (HOL)** — labs.hol.vmware.com
  Free, browser-based lab environments for guided VCF 9 experiences. Key labs include:
  - HOL-2610-01-VCF-L: VCF 9.0 Fundamentals (Installer, VPCs, Transit Gateways)
  - HOL-2610-02-VCF-L: VCF 9.0 Automation (tenant setup, Kubernetes deployments)
  - HOL-2610-03-VCF-L: VCF 9.0 Operations (health monitoring, chargeback)

- **William Lam's Blog** — williamlam.com
  The most comprehensive community resource for VCF lab deployments, nested ESXi automation, and undocumented tips. Essential reading:
  - Ultimate Lab Resource for VCF 9.0 (williamlam.com/2025/06/ultimate-lab-resource-for-vcf-9-0.html)
  - Automated VCF 9.0 Lab Deployment Script (williamlam.com/2025/09/automated-vmware-cloud-foundation-vcf-9-0-lab-deployment-script.html)
  - Minimal VCF 9.0 Lab Setup (williamlam.com/2025/07/minimal-vmware-cloud-foundation-vcf-9-0-lab-setup.html)
  - VCF 9 resource hub (williamlam.com/vmware-cloud-foundation-9)

- **William Lam's GitHub** — github.com/lamw
  Automation scripts for nested VCF deployments, including vcf-automated-lab-deployment and vcf-9x-in-box repositories.

- **VMware User Group (VMUG)** — vmug.com
  Local and virtual community events, UserCon conferences, and VMUG Advantage membership for evaluation licenses and certification discounts.

- **vExpert Program** — vexpert.vmware.com
  Recognition program for community contributors. vExperts receive early access to betas, NFR licenses, and direct channels to product teams.

### Education and Certification

- **Broadcom Education** — broadcom.com/support/education/vmware
  Official training courses, certification exams, and digital badges for VCF 9.

- **VCF STIG Documentation** — techdocs.broadcom.com (search "VCF STIG")
  Security Technical Implementation Guides for hardening VCF environments to DoD and enterprise compliance standards.

---

## VCF PowerCLI Command Cheat Sheet

VCF PowerCLI 9.0 consolidates what was formerly VMware PowerCLI into a VCF-aligned release. The following cmdlets represent the most frequently used commands, organized by operational category.

### Installation and Connection

```powershell
# Install VCF PowerCLI from PowerShell Gallery
Install-Module -Name VCF.PowerCLI -Scope CurrentUser

# Connect to vCenter Server
Connect-VIServer -Server vcenter.lab.local -User administrator@vsphere.local

# Connect to NSX Manager
Connect-NsxServer -Server nsx-mgr.lab.local -User admin

# Connect to SDDC Manager
Connect-VcfSddcManager -Server sddc-mgr.lab.local -User administrator@vsphere.local
```

### Host and Cluster Operations

```powershell
# List all ESXi hosts
Get-VMHost

# Get host hardware information
Get-VMHost | Select-Object Name, ConnectionState, NumCpu, MemoryTotalGB

# Enter maintenance mode
Set-VMHost -VMHost esxi01.lab.local -State Maintenance

# Get cluster details
Get-Cluster

# Get DRS recommendations
Get-DrsRecommendation -Cluster "Cluster01"
```

### Virtual Machine Management

```powershell
# List all VMs
Get-VM

# Create a new VM
New-VM -Name "TestVM" -ResourcePool "Cluster01" -Datastore "vsanDatastore"

# Power operations
Start-VM -VM "TestVM"
Stop-VM -VM "TestVM" -Confirm:$false
Restart-VM -VM "TestVM" -Confirm:$false

# Take a snapshot
New-Snapshot -VM "TestVM" -Name "Pre-Upgrade" -Description "Before patching"

# Get VM guest OS information
Get-VM | Get-VMGuest
```

### Networking

```powershell
# List distributed switches
Get-VDSwitch

# List port groups
Get-VDPortGroup

# Get VM network adapters
Get-VM "TestVM" | Get-NetworkAdapter

# Set network adapter
Get-VM "TestVM" | Get-NetworkAdapter | Set-NetworkAdapter -NetworkName "Production-PG"
```

### Storage and vSAN

```powershell
# List datastores
Get-Datastore

# Get vSAN cluster information
Get-VsanClusterConfiguration -Cluster "Cluster01"

# Get vSAN disk groups
Get-VsanDiskGroup

# Get vSAN health status
Get-VsanHealthSummary -Cluster "Cluster01"

# Get storage policies
Get-SpbmStoragePolicy
```

### SDDC Manager Operations (VMware.Sdk.Vcf.SddcManager)

```powershell
# Discover available SDDC Manager operations
Get-VcfSddcManagerOperation -Method Get
Get-VcfSddcManagerOperation -Method Post
Get-VcfSddcManagerOperation -Method Put

# List workload domains
Invoke-VcfSddcManagerOperation -OperationId GetDomains

# Get host commission status
Invoke-VcfSddcManagerOperation -OperationId GetHosts

# Get lifecycle update bundles
Invoke-VcfSddcManagerOperation -OperationId GetBundles

# Get network pools
Invoke-VcfSddcManagerOperation -OperationId GetNetworkPools

# Run precheck validations
Invoke-VcfSddcManagerOperation -OperationId ValidateDomainCreationSpec
```

### VCF Operations (VMware.Sdk.Vcf.Ops)

```powershell
# Discover available VCF Operations commands
Get-VcfOpsOperation -Method Get

# Get monitored resources
Invoke-VcfOpsOperation -OperationId GetResources

# Get alerts
Invoke-VcfOpsOperation -OperationId GetAlerts

# Get compliance status
Invoke-VcfOpsOperation -OperationId GetComplianceStatus
```

### Certificate Management

```powershell
# Get certificate information for hosts
Get-VMHost | Get-VMHostCertificate

# Get vCenter certificate details
Get-VIServer | Select-Object Name, CertificateInfo
```

### Lifecycle and Patching

```powershell
# Get cluster image compliance
Get-Cluster | Get-ClusterImageCompliance

# Get available ESXi images
Get-EsxImageProfile

# Test cluster compliance
Test-Compliance -Entity (Get-Cluster "Cluster01")
```

---

## Deprecated and Removed Features: VCF 5.x to 9.0

The transition from VCF 5.x to VCF 9.0 represents the most significant architectural shift in VCF history. This section provides a comprehensive reference of everything that changed, was deprecated, or was removed. Plan accordingly before upgrading.

### Terminology Changes

| Former Term (VCF 5.x) | New Term (VCF 9.0) | Notes |
|---|---|---|
| VMware PowerCLI | VCF PowerCLI | Aligned with VCF versioning and release cycle |
| Cloud Builder | VCF Installer | Complete replacement; new appliance and workflow |
| Aria Operations / vRealize Operations | VCF Operations | Integrated as fleet-level service |
| Aria Automation / vRealize Automation | VCF Automation | Integrated as fleet-level service |
| Aria Suite Lifecycle Manager | VCF Operations Lifecycle Manager | Manages VCF Operations component lifecycle |
| Aria Operations for Logs | VCF Operations for Logs | Rebranded under VCF Operations umbrella |
| Aria Operations for Networks | VCF Operations for Networks | Rebranded under VCF Operations umbrella |
| NSX-T Data Center / NSX | NSX (version 9.0) | Simplified naming, unified with VCF versioning |
| vSphere 8.x Update numbering | Unified VCF version (9.0.x.x) | No more independent vSphere version labels |
| License Key (25-character) | Subscription License File | License files replace legacy keys |
| Host Profiles | Configuration Profiles | Cluster-level declarative configuration |
| NSX Manager Mode + Policy Mode | Policy Mode (only) | Manager mode entirely removed |

### Deprecated Features (Present but Planned for Removal)

The following features exist in VCF 9.0 but are officially deprecated and will be removed in a future release. Do not build new dependencies on them.

| Feature | Deprecation Details | Recommended Alternative |
|---|---|---|
| vSphere Virtual Volumes (vVols) | Deprecated in VCF 9.0; planned removal in VCF 9.3 | Migrate to vSAN or VMFS datastores |
| vSphere Clustering Service (vCLS) | Deprecated; recommend retreat mode immediately | DRS and HA operate without vCLS agent VMs |
| vSphere Auto Deploy | Deprecated; will be removed in a future ESXi release | Use VCF Installer and image-based lifecycle |
| Enhanced Linked Mode (ELM) | Deprecated; supported only for upgrade compatibility | Use VCF Operations grouping for multi-vCenter management |
| vSphere Host Profiles | Deprecated in vCenter 9.0 | Use vSphere Configuration Profiles (cluster-level) |

### Removed Features (No Longer Available in VCF 9.0)

The following features have been completely removed. Environments relying on them must migrate before upgrading to VCF 9.0.

| Feature | Details | Migration Path |
|---|---|---|
| NSX Manager Mode / Manager API | Removed entirely; only Policy mode remains | Convert all configurations to NSX Policy API before upgrade |
| Integrated Windows Authentication (IWA) | Removed from vCenter SSO | Migrate to Active Directory over LDAPS or identity federation (SAML/OIDC) |
| vSphere Lifecycle Manager Baselines | Legacy VUM baseline-based patching removed | Transition all clusters to vLCM image-based lifecycle |
| vSphere Trust Authority | Removed in VCF 9.0 | No direct replacement; use TPM-based attestation where required |
| CIM Providers on ESXi | Removed; legacy hardware monitoring protocol | Use native ESXi APIs and vendor-specific management tools |
| SLP (Service Location Protocol) on ESXi | Removed for security hardening | No replacement needed; services discovered through other mechanisms |
| Smart Card Authentication (ESXi) | Removed from ESXi 9.0 | Use identity federation for administrative access |
| RSA SecurID Authentication (ESXi) | Removed from ESXi 9.0 | Use identity federation with MFA |
| Hybrid vSAN (HDD + SSD) | Not supported in VCF 9.0 | Migrate to all-flash vSAN (OSA or ESA) before upgrade |
| Cloud Builder Appliance | Replaced entirely | Use VCF Installer for all deployment operations |
| Independent vSphere/NSX versioning | Eliminated in favor of unified VCF versioning | All components carry the VCF release version number |

### Dropped Hardware Support

| Hardware Type | Details |
|---|---|
| Spinning disk (HDD) for vSAN capacity | All-flash required; no hybrid vSAN support |
| Servers without TPM 2.0 | VCF 9 requires TPM 2.0 for host attestation |
| Legacy NICs without Geneve offload | NSX 9.0 performance features require modern NIC capabilities |
| Non-NVMe boot devices for ESA | vSAN ESA requires NVMe storage devices |

### Upgrade Planning Checklist

Before upgrading from VCF 5.x to VCF 9.0, verify the following:

1. All NSX configurations have been migrated from Manager mode to Policy mode.
2. All vLCM clusters use image-based lifecycle (no baseline-managed clusters remain).
3. IWA authentication has been migrated to LDAPS or identity federation.
4. vVols datastores have been evacuated and VMs migrated to vSAN or VMFS.
5. Hybrid vSAN clusters have been converted to all-flash.
6. Host Profiles have been replaced with Configuration Profiles.
7. All servers meet VCF 9.0 HCL requirements, including TPM 2.0.
8. vCLS has been evaluated for retreat mode.
9. CIM-based monitoring tools have been replaced with API-based alternatives.
10. Backup and recovery procedures have been validated for all management components.

---

## Acknowledgments

A work of this scope is never the product of a single mind. The VMware Cloud Foundation ecosystem is built and sustained by an extraordinary community of engineers, architects, operators, and educators whose contributions extend far beyond any single product release.

**William Lam**, Distinguished Platform Engineering Architect at Broadcom, deserves particular recognition. His tireless work documenting nested lab deployments, minimal resource configurations, and automation scripts at williamlam.com has made VCF accessible to thousands of practitioners who lack enterprise-scale hardware. The automated VCF lab deployment scripts, the minimal resource guides, and the continuously updated VCF 9 resource hub represent an unmatched contribution to the community. Every chapter in this book was tested on infrastructure first proven in William's nested lab configurations.

The **VCF Product Management and Engineering teams** at Broadcom built what this book describes. The architectural vision that unified disparate products into a coherent private cloud platform, the fleet management model that finally delivers on the promise of infrastructure at scale, and the relentless focus on lifecycle simplification reflect years of listening to customer feedback and making hard trade-offs. The deprecation decisions cataloged in this chapter were not made lightly; they were made to move the platform forward.

The **vExpert community** and **VMware User Groups (VMUG)** worldwide provide the connective tissue between product teams and practitioners. The blog posts, conference sessions, study groups, and candid feedback loops that characterize this community are what keep VMware technology grounded in operational reality. The VCP-VCF study groups, the VMUG UserCon presentations, and the countless late-night Slack discussions about upgrade sequencing and NSX Policy migration all shaped the guidance in this book.

The technical reviewers, community bloggers, and content creators whose work informed the research for this book — including the authors at vInfrastructure Blog, vNinja.net, My Cloudy World, Digital Thought Disruption, and dozens of others — represent the best of the VMware ecosystem. Their willingness to document what they learn, including the failures and workarounds, makes the entire community stronger.

The author's work as a Technical Account Manager at VMware by Broadcom provided daily exposure to customer deployments, product teams, and the real-world challenges that shaped every chapter. Special thanks to the customers and colleagues who asked hard questions — this book is the answer.

---

*VMware Cloud Foundation 9: The Definitive Guide*
*Pranav Patel*
*Technical Account Manager, VMware by Broadcom*

