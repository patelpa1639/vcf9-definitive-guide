# VMware Cloud Foundation 9 Certifications -- Research Notes

Research compiled March 2026. All content written in original words based on publicly available sources.

---

## 1. VCP-VCF Administrator Exam (2V0-17.25)

### Exam Logistics

The VCP-VCF Administrator exam carries the code 2V0-17.25 and targets professionals who deploy, operate, and manage VMware Cloud Foundation 9.0 environments. The exam consists of 60 questions, allows 135 minutes of testing time, and requires a scaled passing score of 300 out of 500. It is delivered through Pearson VUE testing centers and costs 250 USD. The exam replaced earlier iterations (2V0-11.24 for VCF 5.x, then 2V0-11.25) as VMware transitioned to role-based certification aligned with VCF 9.

### Candidate Profile

A minimally qualified candidate should have at least one year of general IT experience and a minimum of six months working directly with VCF or its major components (vSphere, vSAN, NSX). Foundational knowledge of enterprise infrastructure concepts such as server hardware, storage protocols, networking, DNS, NTP, and certificate management is expected.

### Blueprint Sections and Key Objectives

**Section 1 -- IT Architectures, Technologies, and Standards**
Covers cloud computing models, VMware infrastructure components, and the industry standards that influence how VCF environments are planned and deployed. This section establishes baseline terminology and conceptual understanding.

**Section 2 -- VMware Cloud Foundation Fundamentals**
Focuses on the integrated stack architecture of VCF, licensing models, and how compute, storage, and networking layers function together. Sub-areas include:

- Private cloud vision, principles, and use cases
- VMware compute fundamentals: component deployment, vSphere cluster configuration, virtual machine management
- VMware storage fundamentals: vSphere storage configuration, vSAN deployment models, storage policies, resilience options
- VMware network fundamentals: networking components, virtual fabrics, connectivity services

**Section 3 -- Plan and Design the VMware by Broadcom Solution**
Tests the ability to assess business requirements, size infrastructure capacity, design resilient topologies, and recommend VCF configurations aligned with organizational goals and growth projections.

**Section 4 -- Deploy, Configure, and Operate VMware Cloud Foundation**
This is the heaviest section and is broken into several sub-domains:

- *VCF Deploy and Configure:* Deployment models, Network Gateway setup, networking configuration, Workload Domain creation, and Supervisor configuration
- *VCF Manage:* Fleet Management, identity and RBAC, licensing, certificates, passwords, vCenter import, lifecycle management (upgrades, patching)
- *VCF Operations:* Monitoring, dashboards, alerting, compliance, and drift detection
- *VCF Consume and Automate:* Automation deployment, organizational tenancy, networking, governance policies

Sections 3 and 4 together account for roughly 60 to 70 percent of exam questions, reflecting the practical emphasis on hands-on administration and design decisions.

### Question Format

The exam uses multiple-choice questions that test understanding of core definitions, feature behavior, and architectural principles. It also includes scenario-based items that present real-world situations requiring candidates to analyze requirements, evaluate trade-offs, and select the best operational approach.

**Sources:**
- https://www.vmexam.com/vmware/vmware-vcp-vcf-admin-certification-exam-syllabus
- https://blogs.vmware.com/cloud-foundation/2024/11/15/get-certified-deep-dive-into-vmwares-new-vcp-vvf-admin-and-vcp-vcf-admin-exams/
- https://broad-cloud.nl/vcp-vcf-9-administrator-exam-experience/
- https://docs.broadcom.com/doc/vmware-cloud-foundation-administrator-exam-guide

---

## 2. VCP-VCF Support Exam (2V0-15.25)

### Exam Logistics

The VCP-VCF Support exam is coded 2V0-15.25 and validates a candidate's ability to support and troubleshoot VMware Cloud Foundation 9.0 environments. Like the Administrator exam, it contains 60 items, allows 135 minutes, uses a scaled passing score of 300, and is proctored through Pearson VUE.

### Candidate Profile

Candidates should have at least one year of experience specifically supporting and troubleshooting VCF components, plus a minimum of two years of overall IT experience. The exam is designed for support engineers, operations staff, and anyone responsible for diagnosing and resolving issues in production VCF environments.

### Unique Troubleshooting Focus

Where the Administrator exam emphasizes implementation and day-to-day management, the Support exam zeroes in on troubleshooting. The separation into role-based exams means the Support track can go deeper into diagnostic methodology and break-fix scenarios. Key troubleshooting domains include:

- VCF fleet deployment troubleshooting
- Compute troubleshooting (vSphere, ESXi host issues)
- Storage troubleshooting (vSAN health, disk failures, policy violations)
- Networking troubleshooting (vSphere Distributed Switches, NSX configuration, routing, gateways, services)
- Operations troubleshooting (monitoring tools, alerting, log analysis)
- Workload mobility issues
- VCF upgrade and conversion problems, scaling issues
- Certificate management troubleshooting, including replacement and auto-renewal processes
- License-related issue diagnosis

The troubleshooting domains collectively account for the majority of exam questions, making hands-on break-fix experience essential preparation.

### Question Format

Similar to the Administrator exam, the Support exam uses multiple-choice and scenario-based questions. Questions tend to progress in difficulty and require applying diagnostic concepts to realistic situations rather than simply recalling facts.

**Sources:**
- https://www.broadcom.com/doc/vmware-cloud-foundation-support-exam-guide
- https://www.scribd.com/document/943333388/2V0-15-25-Exam-Updated-Questions-VMware-Cloud-Foundation-9-0-Support

---

## 3. VCF Architect Exam (2V0-13.24 / 2V0-13.25)

### Exam Logistics

The original VCF Architect exam is coded 2V0-13.24 and covers VCF 5.2. An updated version, 2V0-13.25, has been released for VCF 9.0. Both follow the same format: 60 items, 135 minutes, scaled passing score of 300, delivered via Pearson VUE.

### Design Mindset

This exam stands apart from the Administrator and Support tracks because it tests architectural thinking rather than operational skills. Candidates must demonstrate they can translate business requirements into VCF designs. The exam presents scenario-based questions that mimic real customer engagements, with multiple requirements and constraints. Success depends on identifying relevant details, prioritizing customer needs, and choosing solutions that balance competing concerns.

The Architect exam emphasizes the AMPRS framework: Availability, Manageability, Performance, Recoverability, and Security. Candidates must understand how these qualities influence design decisions across every layer of VCF.

### Candidate Profile and Prerequisites

Candidates should have one to two years of experience designing VMware-based solutions, including at least six months working specifically with VCF. A minimally qualified candidate can design a VCF solution that meets stakeholder requirements and understands VCF architecture across compute, storage, networking, and cloud management layers. They should be able to distinguish between business requirements, technical requirements, functional requirements, and non-functional requirements.

### Blueprint Sections

**Section 1 -- IT Architectures, Technologies, and Standards**
Foundational section covering cloud models and industry standards.

**Section 2 -- VMware by Broadcom Solution Architecture**
Tests the ability to differentiate between various VCF architecture options based on different scenarios. Covers standard architectures, consolidated architectures, multiple instances, and availability zones.

**Section 3 -- Gather and Analyze Requirements, Create Conceptual and Logical Designs**
This is the heart of the exam. Candidates must demonstrate they can gather business objectives, create conceptual models, and develop logical and physical designs. Specific design areas include:

- Network infrastructure design
- VCF Management Domain design
- VCF Workload Domain design
- VCF Edge Cluster design
- VCF Cloud Automation design
- VCF Cloud Operations design

Each area requires understanding prerequisites and the reasoning behind design decisions.

### Key Design Concepts Tested

- RPO and RTO concepts and their influence on architecture
- Conceptual, logical, and physical design phases
- Workload domain sizing and placement
- NSX architecture decisions
- VMware Aria Suite (formerly vRealize) component architecture
- Stretched clusters and site resilience
- Light coverage of HCX and Kubernetes

### Study Resources Recommended by Those Who Passed

- The official exam study guide from Broadcom (line-by-line review recommended)
- The VCF 5.2 Design Guide for understanding prerequisites, types, and topologies
- VMware Validated Solutions documentation for Aria Suite architecture
- Jeffrey Kusters' blog for architectural definitions and VCDX-style terminology

**Sources:**
- https://docs.broadcom.com/doc/vmware-cloud-foundation-architect-exam-guide
- https://harrythambi.com/posts/vcf/vcp-vcf-architect-exam-experience/
- https://varchitected.com/?p=10
- https://thevirtualbit.com/vmware-cloud-foundation-5-2-architect-exam-2v0-13-24-exam-experience/

---

## 4. Certification Lifecycle and Program Structure

### Three-Track VCP Framework

Broadcom has organized VCF certifications into three professional tracks:

1. **VCP-VCF Administrator** -- Validates deployment and management skills
2. **VCP-VCF Support** -- Validates troubleshooting and support skills
3. **VCP-VCF Architect** -- Validates design expertise including performance, security, and capacity planning

### Expiration and Renewal

All VCF certifications now expire after 36 months. Renewal requires passing the current version of the relevant exam. Credentials are linked to the specific exam passed and the certification date rather than to a calendar year. Training completion alone is not sufficient for earning the certification badge; passing the exam is mandatory.

### No Cross-Credit from Previous Programs

Exams passed under the previous certification structure cannot be applied to the new VCF certifications. Candidates must take and pass the current exams.

### Advanced Certifications (VCAP and VCDX)

Beyond VCP, the certification ladder extends to VCAP (VMware Certified Advanced Professional) and VCDX (VMware Certified Design Expert). In November 2025, Broadcom announced the next evolution of the VCDX program, positioning it as the certification for private cloud experts. VCDX holders command some of the highest salaries in the VMware ecosystem, with Glassdoor reporting average compensation around 200,000 USD for senior cloud professionals at that level.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/07/29/vcf-certification-updates-a-streamlined-path-to-private-cloud-expertise/
- https://blogs.vmware.com/cloud-foundation/2025/11/13/the-next-evolution-of-vmware-certified-distinguished-expert-vcdx-certification/
- https://docs.broadcom.com/docs/vmw-certification-paths

---

## 5. Hands-On Labs (HOL) Catalog for VCF 9.0

VMware Hands-on Labs provide free, self-paced, on-demand lab environments that require no software installation. Labs can be repeated as many times as needed and are available around the clock through labs.hol.vmware.com.

### HOL-2610-01-VCF-L: What's New in VCF 9.0 -- Platform

Introduces core VCF 9.0 concepts including private clouds, Fleets, and Instances. Walks through the deployment process using the VCF Installer. Covers productivity enhancements such as vSphere Pods, Virtual Private Clouds (VPCs), and Transit Gateways.

### HOL-2610-02-VCF-L: What's New in VCF 9.0 -- Automation

Provides guided experience through tenant setup using the quick start wizard. Covers the Provider Portal (infrastructure, access, identity features), Organization Management (content libraries, IaaS policies), and hands-on VM provisioning and Kubernetes cluster deployments.

### HOL-2610-03-VCF-L: What's New in VCF 9.0 -- Operations

Addresses private cloud health monitoring, network flow analysis, vSAN and storage management, security operations, and chargeback implementation.

### Related Labs

- **HOL-2633-01-VCF-L: Unifying VM and Kubernetes Management with vSphere Supervisor** -- Teaches Supervisor concepts, configuration, and deployment of both Kubernetes clusters and vSphere Pods with virtual machines.
- **HOL-2630-01-VCF-L: What's New with vSphere in VCF 9.0** -- Covers unified licensing, NVMe-enhanced Memory Tiering, zero-downtime Live Patching, multi-vendor cluster management, and Lifecycle Manager enhancements.
- **HOL-2532-01-VCF-L: Getting Started with VMware Cloud Foundation** -- A foundational lab recommended by multiple exam passers as essential preparation for understanding VCF fundamentals.

Note: A lab numbered HOL-2610-04 was not found in any official VMware catalog or blog post as of the research date. The HOL-2610 series appears to consist of three labs (01, 02, 03). The related labs listed above (HOL-2633 and HOL-2630) may be what some sources reference as the broader VCF 9 lab collection.

**Sources:**
- https://blogs.vmware.com/cloud-foundation/2025/06/19/vmware-cloud-foundation-9-0-hands-on-labs-dive-in-and-unlock-your-potential/
- https://labs.hol.vmware.com/HOL/catalog?products=VMware+Cloud+Foundation+(VCF)
- https://vdan.cz/vmware/vcf/deep-dive-whats-new-in-vmware-cloud-foundation-9-0-platform-hol-2610-01-vcf-l/

---

## 6. VMUG Advantage Exam Vouchers

### Membership Cost and Core Benefit

VMUG Advantage membership costs 210 USD per year and includes a 125 USD discount on a VCP-VCF certification exam.

### Exam Discount Tiers

VMUG Advantage subscribers can access exam discounts through two mechanisms:

- **Standard benefit:** 20 percent off VMware certification exams available at any time
- **Enhanced Broadcom promotion:** Broadcom has periodically provided VMUG Advantage subscribers a 50 percent discount on VCP and VCAP exams

### Free Exam Opportunities

VMUG has run a free VCP exam application program where active Advantage members can apply for a chance to take their VCP exam at no cost. An application window ran from December 2024 through February 2025, with winners announced on specific dates. Free certification attempts have also been offered at VMUG Connect events (for example, the April 2025 event in St. Louis).

### Post-Certification Home Lab Licenses

Starting in March 2025, VMUG Advantage members who earn VCP-VCF certification gain access to exclusive VMware licenses for personal, non-production use. VCP-VCF holders receive VMware Cloud Foundation licenses, while VCP-VVF holders receive vSphere Foundation licenses. These 3-year licenses provide an ongoing incentive to certify and maintain an active home lab.

### At VMware Explore

Full Event Pass holders at VMware Explore 2025 received one certification exam included with their registration.

**Sources:**
- https://www.vmug.com/membership/vmug-advantage-membership/
- https://virtualg.uk/vmware-certification-exam-discount-codes/
- https://blogs.vmware.com/cloud-foundation/2025/04/14/free-home-lab-licenses-for-vmware-certified-professionals/
- https://blogs.vmware.com/cloud-foundation/2025/08/20/unlock-your-private-cloud-potential-learning-certification-highlights-at-vmware-explore-2025/

---

## 7. Study Tips from People Who Passed

### Common Preparation Strategies

Multiple bloggers who passed VCF certification exams converge on similar advice:

**Read the blueprint line by line.** Every successful candidate emphasizes starting with the official exam guide from Broadcom and treating each objective as a checklist item. For unfamiliar topics, one author recommends copying relevant sections from official documentation into a personal study document for later review and memorization.

**Use multiple learning formats.** Successful candidates typically combine reading, video content, and hands-on practice. One candidate converted the exam guide PDF into an audio format using NotebookLM, creating a 70-minute podcast-style review hosted by two AI voices.

**Do not underestimate NSX, Aria, and Tanzu/Kubernetes coverage.** Multiple authors note that VCF is far more than vSphere and vCenter. The exam covers vSAN, NSX networking, VMware Aria Suite (operations, automation, logs), and Kubernetes through vSphere Supervisor. Candidates who focus only on compute topics will be caught off guard.

**Complete the Hands-on Labs.** Nearly every successful candidate cites the free VMware HOL environments as critical preparation. The "Getting Started with VMware Cloud Foundation" lab (HOL-2532) is the most commonly recommended starting point.

**Join the community study group.** The VMware {code} VCP-VCF study group ran six weekly sessions hosted by Broadcom technical staff (Stephan McTighe, James Tiffany, Gareth Edwards). All sessions are recorded and available on YouTube. Topics covered include SDDC Manager overview, VCF deployment, workload domains, day-2 operations, upgrades, patching, Aria Suite, NSX, Kubernetes Service, vSphere Supervisor, vSAN, and host maintenance.

**Use practice questions wisely.** Nate Hudson's study guide containing 215 sample questions is specifically called out as helpful for understanding the types of questions on the exam and identifying knowledge gaps.

**Watch the 15-part VCF technical series on YouTube.** Multiple candidates report that topics from this series appeared directly on their exam.

### Specific Exam-Day Advice

- Schedule the exam during a distraction-free period
- Use the question flagging and answer strikethrough features built into the Pearson VUE interface
- The exam is scenario-heavy; read each question carefully and identify what is actually being asked before reviewing the answer options
- For the Architect exam specifically, think like a consultant solving a customer problem rather than a technician recalling facts

### What Surprised Candidates

- The depth of VCF Operations and VCF Automation coverage
- The shift in the 2V0-17.25 exam toward implementation skills over troubleshooting (troubleshooting is now in the separate Support exam)
- The emphasis on VCF 9 architecture changes, particularly Fleets and Instances

**Sources:**
- https://vloreblog.com/2025/11/17/vcf-9-0-admin-exam-prep/
- https://thedxt.ca/2025/05/passing-vcp-vcf-admin-exam/
- https://broad-cloud.nl/vcp-vcf-9-administrator-exam-experience/
- https://vmexplorer.com/2025/11/17/my-notes-and-tips-around-taking-the-2v0-17-25-vmware-cloud-foundation-9-0-administrator/
- https://harrythambi.com/posts/vcf/vcp-vcf-architect-exam-experience/
- https://blogs.vmware.com/code/2025/02/13/join-the-vmware-code-vcp-vcf-study-group/

---

## 8. Broadcom Enterprise Learning Subscription (ELS)

### What It Is

The VMware Enterprise Learning Subscription is Broadcom's digital learning platform for IT teams. It provides around-the-clock access to the full catalog of VCF training content, delivered by VMware Certified Instructors and top VMware experts.

### What Is Included

- Access to all full-length On Demand courses covering the VCF portfolio
- All features and content of VMware Customer Connect Learning (formerly VMware Learning Zone)
- Video training and certification exam preparation materials
- On Demand courses and Lab Connect courses
- Two complimentary VCP exam vouchers per student per year of the contract
- Access to exam prep materials and community forums

### Key Training Courses for VCF 9

Two primary courses are recommended by those who have passed:

1. **VMware Cloud Foundation 9: Build, Manage and Secure** -- Covers deployment, post-deployment tasks, fleet management, workload domains, networking, storage, and certificates
2. **VMware Cloud Foundation: Automation and Operations** -- Addresses operations and automation topics not fully covered in the first course

### Pricing Model

Specific pricing is not publicly listed and requires contacting a Broadcom Learning Specialist or sales representative. Multi-year contracts offer savings of up to 25 percent. The minimum enrollment is five students for enterprise customers and three students for commercial customers.

### VCF Learning Entitlements

Broadcom also provides learning entitlements bundled with VCF software purchases. A "Getting Started with your VCF Learning Entitlements" guide is available from VMware documentation to help customers understand and activate their included training access.

**Sources:**
- https://www.broadcom.com/support/education/vmware/learning-paths/enterprise-learning-subscription
- https://docs.broadcom.com/doc/vmware-enterprise-learning-subscription-datasheet
- https://www.vmware.com/docs/vmw-getting-started-with-your-vcf-learning-entitlements-ebook

---

## 9. Free Study Resources

### Official Blueprints and Exam Guides

Broadcom publishes detailed exam guides for each certification track as downloadable PDFs. These documents list every objective and sub-objective that may appear on the exam. They are the single most important free resource for exam preparation.

- VCF Administrator Exam Guide: https://docs.broadcom.com/doc/vmware-cloud-foundation-administrator-exam-guide
- VCF Support Exam Guide: https://www.broadcom.com/doc/vmware-cloud-foundation-support-exam-guide
- VCF Architect Exam Guide: https://docs.broadcom.com/doc/vmware-cloud-foundation-architect-exam-guide

### VMware TechDocs and Official Documentation

The official VMware documentation site (docs.vmware.com and techdocs.broadcom.com) contains product guides, deployment guides, administration guides, and troubleshooting guides for every VCF component. Successful candidates recommend reading the official documentation for each item in the exam blueprint.

### Hands-on Labs

VMware Hands-on Labs at labs.hol.vmware.com are completely free, require no software installation, and can be repeated without limit. The VCF 9.0 labs (HOL-2610 series) and the foundational Getting Started lab (HOL-2532) are the most relevant for certification preparation.

### Community Blogs

Several community blogs provide detailed exam experiences and study notes:

- **vLore Blog** (vloreblog.com) -- VCF 9.0 Admin exam prep post with downloadable study notes PDF
- **theDXT** (thedxt.ca) -- Detailed walkthrough of passing the VCP-VCF Admin exam, including resource links
- **Broad-Cloud.NL** (broad-cloud.nl) -- VCP VCF 9 Administrator exam experience with emphasis on VCF 9 architecture changes
- **vmexplorer** (vmexplorer.com) -- Notes and tips for the 2V0-17.25 exam
- **Harry Thambi** (harrythambi.com) -- VCP VCF Architect exam experience
- **vThinkBeyondVM** (vthinkbeyondvm.com) -- VCF learning journey and specialist exam tips
- **vChamp** (vchamp.net) -- VCP-VCF Administrator certification experience

### VMware {code} Community Resources

- The VMware {code} VCP-VCF study group YouTube playlist (six recorded sessions)
- The VMware {code} Community Exam Guide (comprehensive Google Doc with notes on each tested section, updated for the 2025 exam guide)
- Broadcom Community Programs Discord server with a VCP-VCF study group channel

### YouTube Content

- A 15-part VCF technical series frequently cited by exam passers
- Recorded study group sessions from the VMware {code} community
- Individual contributor channels covering VCF topics

### Official VMware Blog Posts

- "Get Certified: Deep Dive into VMware's New VCP-VVF Admin and VCP-VCF Admin Exams" -- the definitive official blog post explaining exam structure and objectives (https://blogs.vmware.com/cloud-foundation/2024/11/15/get-certified-deep-dive-into-vmwares-new-vcp-vvf-admin-and-vcp-vcf-admin-exams/)
- "VCF Certification Updates: A Streamlined Path to Private Cloud Expertise" -- explains the 2025 certification program changes (https://blogs.vmware.com/cloud-foundation/2025/07/29/vcf-certification-updates-a-streamlined-path-to-private-cloud-expertise/)

**Sources:**
- https://blogs.vmware.com/code/2025/02/13/join-the-vmware-code-vcp-vcf-study-group/
- https://vloreblog.com/2025/11/17/vcf-9-0-admin-exam-prep/
- https://thedxt.ca/2025/05/passing-vcp-vcf-admin-exam/

---

## 10. Career Impact of VCF Certification

### Salary Data

VCF certification carries meaningful salary implications across different levels of the certification ladder:

- **VCP-level professionals** typically earn between 90,000 and 110,000 USD annually
- **VCP-DCV holders** can command up to 130,000 USD or more
- **VCDX-certified professionals** report average compensation exceeding 140,000 USD, with senior cloud roles averaging around 200,000 USD according to Glassdoor
- More than 90 percent of LinkedIn job postings requiring VMware certification list starting salaries above 100,000 USD

### Promotion and Confidence

Survey data from Broadcom and partner organizations suggests that 63 percent of certified professionals received a job promotion or were anticipating one after becoming certified. Additionally, 82 percent reported more confidence in pursuing new job opportunities.

### Market Demand

Demand for VMware-certified professionals remains strong as organizations continue to build and manage hybrid cloud environments. The shift to VCF as the unified platform amplifies the value of VCF-specific certifications because VCF consolidates what used to be separate product certifications (vSphere, vSAN, NSX, Aria) into a single platform credential.

### Strategic Value of VCP-VCF Administrator

The VCP-VCF Administrator certification is positioned as the most strategic option for professionals looking to advance into modern cloud administration roles. It validates the ability to manage integrated software-defined data centers and work with hybrid cloud technology. As more enterprises adopt VCF as their private cloud foundation, professionals with this credential are well-positioned for roles in cloud infrastructure management, hybrid cloud operations, and platform engineering.

### Growth Areas

Several specialization areas within the VMware ecosystem are expected to see growing demand through 2026:

- **Tanzu and Kubernetes management** as enterprises modernize applications and adopt cloud-native patterns
- **NSX networking and security** as software-defined networking becomes standard in enterprise environments
- **VCF architecture and design** as organizations plan private cloud deployments at scale

### Certification as Career Insurance

In the context of the Broadcom acquisition and ongoing changes to VMware licensing and product strategy, VCF certification serves a dual purpose: it validates current technical skills and signals to employers that the professional is keeping pace with platform evolution. Organizations investing in VCF want assurance that their teams have up-to-date knowledge, making current certification a differentiator in hiring and retention decisions.

**Sources:**
- https://examsindex.com/blog/vmware-certifications-that-will-lead-the-market-in-2026/
- https://www.payscale.com/research/US/Certification=VMware_Certified_Professional_(VCP)/Salary
- https://www.certlibrary.com/blog/comprehensive-guide-to-vmware-certification-pathways-in-2025-and-beyond/
- https://www.isecprep.com/2025/09/29/vcp-vcf-administrator-vs-other-vmware-tracks/
- https://blogs.vmware.com/cloud-foundation/2025/07/29/vcf-certification-updates-a-streamlined-path-to-private-cloud-expertise/
