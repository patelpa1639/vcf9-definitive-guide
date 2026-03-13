# VMware Cloud Foundation 9: The Definitive Guide

### A Comprehensive Reference for Architects, Administrators, and Engineers

**By Pranav Patel**
Technical Account Manager, VMware by Broadcom

*First Edition — 2026*

*The views in this book are the author's own and do not represent official Broadcom positions.*

---

## Legal Notices and Trademark Attributions

**Copyright Notice**

Copyright © 2026 Pranav Patel. All rights reserved.

No part of this publication may be reproduced, distributed, or transmitted in any form or by any means, including photocopying, recording, or other electronic or mechanical methods, without the prior written permission of the author, except in the case of brief quotations embodied in critical reviews and certain other noncommercial uses permitted by copyright law.

**Disclaimer**

This book is an independent publication and is not affiliated with, authorized by, endorsed by, or sponsored by Broadcom Inc. or any of its subsidiaries or affiliates. The author is employed by VMware by Broadcom as a Technical Account Manager; however, this book represents the author's personal analysis and opinions, not the official positions of Broadcom Inc., VMware LLC, or any affiliated entity.

The information in this book is provided on an "as is" basis without warranty of any kind, express or implied. The author and publisher shall not be liable for any errors, omissions, or damages arising from the use of the information contained herein. Technical details, product features, pricing, licensing terms, and support policies described in this book are subject to change without notice. Readers should consult official Broadcom documentation and their account teams for the most current and authoritative information.

Statistical claims, analyst findings, and third-party research cited in this book are attributed to their original sources to the best of the author's ability. The author does not guarantee the accuracy of third-party data and encourages readers to consult original sources for verification.

**Trademark Attributions**

The following trademarks are the property of Broadcom Inc. and/or its subsidiaries and are used in this book for identification and reference purposes only:

VMware®, VMware Cloud Foundation™, vSphere®, ESXi™, ESX™, vCenter®, vCenter Server®, vSAN™, NSX®, vDefend™, HCX™, Aria™, Tanzu®, VMware Cloud Director™, VMware Validated Solutions™, SDDC Manager™, VCF Operations™, VCF Automation™, VCF Installer™, VMware Live Recovery™, VMware Private AI Foundation™, Avi™, and the VMware logo are trademarks or registered trademarks of Broadcom Inc. and/or its subsidiaries in the United States and other countries.

Additional third-party trademarks referenced in this book:

- NVIDIA®, vGPU™, BlueField®, CUDA®, Triton™, and Hopper™ are trademarks of NVIDIA Corporation.
- Intel®, Xeon®, Sapphire Rapids™, Granite Rapids™, and TDX™ are trademarks of Intel Corporation.
- AMD®, EPYC™, Genoa™, Turin™, and SEV-SNP™ are trademarks of Advanced Micro Devices, Inc.
- Kubernetes® is a registered trademark of The Linux Foundation.
- Nutanix® and AHV™ are trademarks of Nutanix, Inc.
- Microsoft®, Azure®, Windows®, and Hyper-V® are trademarks of Microsoft Corporation.
- Amazon Web Services™, AWS®, and Outposts™ are trademarks of Amazon.com, Inc.
- Red Hat®, OpenShift®, and Ansible® are trademarks of Red Hat, Inc.
- HashiCorp® and Terraform® are trademarks of HashiCorp, Inc.
- Pearson VUE® is a trademark of Pearson Education, Inc.
- Gartner® is a registered trademark of Gartner, Inc.

All other trademarks, service marks, and trade names referenced in this book are the property of their respective owners. The use of any trademark in this book does not imply any affiliation with or endorsement by the trademark owner.

**Examination Disclaimer**

The practice examination questions in Chapter 12 are original works created by the author for educational purposes. They are not sourced from, affiliated with, or endorsed by Broadcom Inc., Pearson VUE, or any official certification examination. These questions are designed to test general conceptual knowledge and should not be considered representative of actual examination content. Broadcom's official exam blueprints and study materials remain the authoritative source for certification preparation.

**Publication Information**

- First Edition: March 2026
- Current through: VCF 9.0.2 (January 2026)
- ISBN: Pending

For corrections, feedback, or permissions requests, contact the author via GitHub.

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
