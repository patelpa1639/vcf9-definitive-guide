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
