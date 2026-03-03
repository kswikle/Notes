# Everpure CTO Meeting Prep — Tech Stack & Differentiators

**Date:** March 2, 2026
**Meeting with:** CTO Rob Lee (Chief Technology & Growth Officer)
**Context:** Q4/FY26 earnings released Feb 25, 2026

---

## The 60-Second Version

Everpure (formerly Pure Storage) is not a typical storage vendor. The
entire company is built on a **proprietary hardware-software co-design**
thesis: they designed their own flash modules (DirectFlash) and their
own operating system (Purity), which work together in ways that
off-the-shelf SSDs physically cannot replicate. CTO **Rob Lee** was
the founding architect of FlashBlade (now a $1B+ product line) and
holds **75+ patents** in distributed systems and storage. The tech
strategy is evolving from "best flash arrays" to a full **Enterprise
Data Cloud** — a unified data platform spanning storage, management,
intelligence, and AI-readiness. The 1touch acquisition adds a data
intelligence layer that makes this vision concrete.

---

## Know Your CTO: Rob Lee

- **Title:** Chief Technology & Growth Officer (CTGO)
- **Joined Pure:** 2013 (one of the earliest senior hires)
- **Before this:** 12 years at Oracle — Architect on programming
  language runtimes for the Oracle RDBMS and high-performance
  distributed transaction processing (Oracle Coherence)
- **Education:** BS and M.Eng in Electrical Engineering & Computer
  Science from MIT
- **At Pure:** Joined as founding member of the **FlashBlade team**.
  Led software architecture from concept to launch for the product
  that became a $1B+ business. Promoted to VP & Chief Architect
  (~2017), then CTO (August 2021), now CTGO.
- **Patents:** 75+ in distributed systems, language runtimes, and
  storage systems (up from 33 in 2019 — prolific inventor)
- **Personal:** Serves on the board of Bay Area Underwater Explorers
  and Cordell Marine Sanctuary Foundation (ocean conservation)

*Good rapport angle: He's a builder, not a corporate CTO. He
personally architected the product that became FlashBlade. Ask him
what the original design constraints were back in 2013 and how
they've evolved.*

### His Public Technical Vision (Recent Quotes)

- **On AI and storage:** "AI and performance are somewhat synonymous,
  but performance for AI isn't just one thing — training, fine-tuning,
  and inference all have very different performance profiles."
  (Jan 2025)
- **On data modernization:** "With AI technologies, even historical
  data that was previously archived on the cheapest option suddenly
  needs to be performant, secure and modernised." (Computer Weekly,
  2025)
- **On lock-in:** Consistently advocates for open architectures and
  open networking. Points to Ethernet as the model. Warns that
  "making the wrong technical choice and needing to re-platform
  because of insufficient performance or lack of application support
  is costly."
- **On FlashBlade//EXA:** "The combination of FlashBlade S and
  FlashBlade EXA now allows us to address the entirety, the entire
  spectrum of AI needs." (Pure//Accelerate 2025)

---

## The Technology Stack (Layer by Layer)

### Layer 1: DirectFlash Modules (DFMs) — The Hardware Foundation

This is Everpure's deepest moat. Everything else builds on it.

| Aspect | DirectFlash (Everpure) | Traditional SSD (Dell, NetApp, HPE) |
|--------|----------------------|--------------------------------------|
| **Architecture** | Manages raw NAND flash at the media level | SSD has its own controller + flash translation layer (FTL) |
| **DRAM overhead** | Near zero per module (metadata centralized in Purity) | ~32 GB DRAM per 30 TB SSD for indirection tables |
| **Over-provisioning** | Centralized at system level | Each SSD reserves ~20% spare space independently |
| **Max module size** | 150 TB today, 300 TB coming | ~60 TB max for commercial SSDs |
| **Density advantage** | ~5x (Pure claims "30x more dense than everybody else" with 150 TB DFMs) | Limited by per-drive FTL complexity |
| **Reliability** | 3x more reliable than SSDs, 6x more reliable than HDDs | Standard |
| **Upgradability** | DFMs upgraded independently, non-disruptively | Drive swap = data migration |

**Why this matters technically:** By eliminating the per-drive flash
translation layer, Everpure centralizes wear leveling, garbage
collection, and error correction in software (Purity). This means:
1. They can improve media management with software updates over time
2. They avoid duplicated overhead across hundreds of drives
3. They can scale module size without scaling controller complexity
4. They can achieve margins of 75-85% on hyperscaler deals because
   they license the IP/modules while hyperscalers procure their own NAND

**Meta validation:** Meta has certified Everpure's DFMs as the storage
medium of choice for its next-generation data centers.

---

### Layer 2: Purity — The Operating System

Purity is the unified storage OS that runs across every Everpure
product. Think of it as what makes the hardware intelligent.

**Core capabilities:**
- **Unified protocol support:** Block (FC, iSCSI, NVMe-oF), File
  (NFS, SMB), and Object — all from the same platform
- **Always-on data reduction:** Deduplication + compression at a
  granularity competitors can't match (sub-block level). Only unique
  data blocks are stored on flash.
- **Encryption by default:** "Encrypt everything" approach — all data
  encrypted at rest with no performance penalty, no user config needed
- **QoS built-in:** Per-workload IO guarantees in a shared flash pool
- **Non-disruptive everything:** Software updates ship every 1-3
  months. Hardware upgrades (controllers, DFMs, connectivity) happen
  live. 99.9999% availability maintained through upgrades.

**Data protection:**
- **ActiveDR:** Continuous replication with near-zero RPO, single-
  command failover
- **ActiveCluster:** Synchronous active-active replication — config
  changes propagate simultaneously to both arrays, zero RPO/RTO with
  automatic failover

**The key insight:** Because Purity is the single OS across all
products (FlashArray, FlashBlade, cloud), skills and automation
transfer everywhere. This is a major TCO advantage vs. Dell/NetApp
where different product lines run different software stacks.

---

### Layer 3: The Product Portfolio

#### FlashArray Family (Scale-Up Block + File)

| Model | Sweet Spot | Key Specs |
|-------|-----------|-----------|
| **FlashArray//XL 190 R5** (newest) | Highest-performance workloads (large DBs, containerized apps) | 36 GB/s throughput, <150µs latency, up to 5.78 PB effective in 5U. Intel Emerald Rapids CPUs, PCIe Gen 5, NVRAM on DFM. 50% lower latency / 2x perf vs prior gen. GA Q4 FY26. |
| **FlashArray//X R5** | Mission-critical production | 73 TB to 3.3 PB effective. DirectMemory available on //X70 and //X90 for improved read latency. |
| **FlashArray//C R5** | Capacity-optimized (QLC flash) | Up to 8.9 PB effective. Same Purity OS, same data services, just tuned for $/TB. |
| **FlashArray//E** | Archive, backup, disk replacement | Price-competitive with HDD-based systems but with flash reliability. For tier 2 workloads. |
| **FlashArray//ST** | Newest addition (2025) | Details emerging. |

*Note: FlashArray is scale-up, not scale-out. Each model is a
controller pair. For more parallel performance, you add another
array and manage via Fusion.*

#### FlashBlade Family (Scale-Out File + Object)

| Model | Sweet Spot | Key Specs |
|-------|-----------|-----------|
| **FlashBlade//S** | Mid-range unstructured data, analytics, AI fine-tuning | Scale-out architecture, strong metadata performance via key-value store |
| **FlashBlade//EXA** | AI training at scale, HPC, exabyte-class workloads | 1+ TB/s write, 2+ TB/s read, 3.4 TB/s per rack. Disaggregated metadata/data architecture. Uses off-the-shelf third-party data nodes + standard networking (open architecture). Native object support in preview. |

**FlashBlade//EXA deep dive:**
- Designed specifically to keep GPU clusters fully fed — the #1 AI
  infrastructure challenge is idle GPUs waiting on storage
- **MLPerf results:** 2x faster than closest competitor in less than
  half a rack of storage. The test proved EXA can keep all simulated
  GPUs at 90%+ utilization
- **SPEC Storage AI Image Benchmark:** Highest published results
  (announced Q4 FY26 earnings)
- First EXA customer: a GPU cloud provider who performance-tested
  against a competitor, switched to EXA, placed multiple orders
- Dozens more customers in advanced discussions
- Integrates with NVIDIA AI Data Platform / DGX systems

---

### Layer 4: Pure Fusion — The Intelligent Control Plane

Fusion is what turns a fleet of individual arrays into a **unified,
policy-driven storage mesh.**

- **What it does:** Virtualizes all storage (FlashArray + FlashBlade,
  on-prem + cloud) into a single manageable fleet. Provisioning shifts
  from individual tickets to fleet-wide policies.
- **Architecture:** Built directly into Purity (not a separate product).
  Runs as a **local** control plane hosted on your arrays — not in the
  cloud. Protocol agnostic (block, file, object, FC, iSCSI, NVMe).
- **Cost:** Zero. No licensing fee. Included with Purity.
- **AI-driven placement:** Fusion handles workload balancing and
  optimization centrally, including AI-driven placement decisions
  when used with Portworx.
- **Management paradigm shift:** Moves from device-centric admin to
  **intent-based policy orchestration** across the entire estate.

---

### Layer 5: Pure1 & AI Copilot — AIOps Management

Pure1 is the fleet management and observability platform.

- **Pure1 Meta:** Built-in AIOps engine for predictive service
  management — anticipates capacity needs and failures weeks in advance
- **AI Copilot (GA June 2025):** Industry's first generative AI
  copilot for storage. Natural language interface for:
  - Performance troubleshooting
  - Security auditing ("Are any volumes vulnerable to data eradication?")
  - Executive reporting and compliance
  - Documentation search and CLI help
- **MCP Integration (Model Context Protocol):** The copilot can now
  **act**, not just advise. Trigger workflows in natural language —
  migrate VMs to OpenShift, rebalance workloads — and the copilot
  orchestrates end to end.
- **Powered by ChatGPT** (enterprise-grade, SOC 2 Type 2 compliant).
  Data encrypted, private, auto-deleted after 30 days, never used
  for model training.
- **Included** in all active subscriptions and support contracts.
  No extra cost.

---

### Layer 6: Portworx — Cloud-Native / Kubernetes Storage

Acquired by Pure Storage, Portworx is the Kubernetes data services
platform most used by Global 2000 companies.

- **What it does:** Persistent storage, HA, data protection, security,
  and cloud mobility for containerized applications
- **Recent (2025):** Portworx Enterprise 3.3 added **KubeVirt support**
  — run VMs and containers on a single Kubernetes platform. Major VMware
  migration play.
- **AI/ML:** As-a-service delivery for databases and AI/ML models,
  including vector databases (Milvus, PostgreSQL, Elasticsearch) and
  graph databases (Neo4j)
- **Pure Fusion integration:** Portworx PVC creation triggers Fusion
  for AI-driven placement, optimizing capacity and performance
- **Runs on:** Red Hat OpenShift, EKS, AKS, GKE, SUSE Rancher,
  upstream Kubernetes

---

### Layer 7: 1touch / Kontextual — Data Intelligence (Incoming)

The pending acquisition (expected close Q2 FY27) adds a **data
intelligence and context layer** on top of the storage platform.

**What Kontextual does:**
- AI-first data intelligence platform (LLM-driven)
- Discovers, classifies, contextualizes, and enriches enterprise data
  across any environment — cloud, on-prem, mainframe
- **Multidimensional Data Graph:** Links every data element to its
  identity, lineage, usage, access patterns, and risk signals
- **Active Data Fabric:** Non-disruptive real-time layer that maps and
  classifies data at rest and in motion
- Replaces legacy tools (one Fortune 500 retailer replaced Microsoft
  Purview, reducing false positives 40%+ and expanding visibility)

**Performance claims:**
- 98.6% classification accuracy
- 11x faster than legacy solutions
- 500% YoY bookings growth, zero churn
- Deployed across Fortune 50 companies protecting 300M+ individuals'
  data

**Strategic significance:** This is the missing piece that turns
Everpure from "where you store data" to "where your data becomes
AI-ready." Rob Lee authored the blog post on this acquisition,
framing it as bridging the gap between managing datasets and managing
information through contextual data intelligence.

---

## The Evergreen Model — The Technical Business Model

The Evergreen architecture is not just a pricing model — it's an
engineering philosophy baked into the hardware.

| Tier | Model | Key Feature |
|------|-------|-------------|
| **Evergreen//One** | Storage-as-a-Service (consumption-based) | Scale up/down instantly, pay for usage, SLAs on performance + availability, 15-min white-glove support |
| **Evergreen//Forever** | Buy once, subscribe to innovation | Non-disruptive hardware + software upgrades forever. No forklift replacements. |
| **Evergreen//Flex** | Fleet-level hybrid | Moves stranded capacity across the fleet. Owns hardware, pays for usage. |
| **Evergreen//Foundation** | Basic subscription | Software updates + ability to purchase capacity or modernize anytime |

**How non-disruptive upgrades actually work:**
- Controllers, blades, DFMs, connectivity — all upgraded independently
  while the system is live, data in place
- **Ever Modern:** Free controller/blade upgrades every 3 years on
  renewal (6 years for //E family)
- **Ever Agile:** Upgrade anytime to latest-gen with full-value
  trade-in credits
- **Capacity Consolidation:** Move to denser media with trade-in
  credits, no data migration
- **Track record:** 10,000+ non-disruptive controller upgrades to
  3,000+ customers since 2015
- Software updates ship every 1-3 months, included for all subscribers

**Why competitors can't easily replicate this:** The non-disruptive
upgrade path depends on the DFM + Purity co-design. When the hardware
is commodity SSDs, swapping storage usually means migrating data. When
you control the flash layer end-to-end, you can decouple upgrades.

---

## Competitive Positioning — How Everpure Wins

### vs. Dell EMC

| Dimension | Everpure | Dell EMC |
|-----------|----------|----------|
| Flash architecture | Proprietary DFMs, manages raw NAND | Commodity SSDs, standard FTL |
| Max module density | 150 TB (300 TB coming) | Limited by SSD market (~60 TB max) |
| Storage OS | Single OS (Purity) across all products | Multiple software stacks across PowerMax, PowerStore, PowerFlex, Unity |
| Upgrade model | Non-disruptive, data in place | Typically requires data migration on major refreshes |
| AI storage | FlashBlade//EXA (purpose-built) | No direct equivalent at EXA scale |
| Kubernetes | Portworx (acquired, #1 in Global 2000) | CSI drivers, less integrated |
| Bundling | Storage-focused pure play | Bundles servers + networking (lock-in advantage) |

### vs. NetApp

| Dimension | Everpure | NetApp |
|-----------|----------|--------|
| Flash architecture | Proprietary DFMs | Commodity SSDs (AFF series) |
| Strength | All-flash innovation, AI/HPC scale, density | Hybrid cloud data services, ONTAP ecosystem, native cloud integrations (CVO) |
| Cloud strategy | Portworx for K8s + Evergreen//One for STaaS | Native storage services from AWS/Azure/GCP (BlueXP) |
| Non-disruptive upgrades | Hardware + software, fully independent | Controller-only swaps via ARL (limited models, professional services) |
| AI positioning | FlashBlade//EXA, MLPerf leader | AFF A-series, less AI-specific marketing |

### vs. HPE

| Dimension | Everpure | HPE |
|-----------|----------|-----|
| Flash architecture | Proprietary DFMs | Alletra uses commodity SSDs |
| As-a-service | Evergreen//One (native) | GreenLake (broader but less storage-specific) |
| Data intelligence | 1touch/Kontextual (AI-driven) | Zerto for DR, less on data classification |
| Ironic overlap | HPE's former CFO (Robbiati) now at Everpure, running same subscription playbook |

---

## Analyst & Industry Recognition

- **Gartner Magic Quadrant for Enterprise Storage Platforms (2025):**
  Leader for the **12th consecutive year**, positioned **highest in
  execution and furthest in vision**
- **Gartner Magic Quadrant for Infrastructure Platform Consumption
  Services (2025):** Also named a Leader
- **Gartner Peer Insights:** 4.9/5 overall rating, 98% willing to
  recommend (689 reviews)
- **Net Promoter Score:** Consistently top 1% of B2B companies globally
- **750+ patents** across the company

---

## Sustainability & Efficiency (CTO Talking Point)

Energy efficiency is a core technical differentiator, not just a
marketing angle:

- **85% less energy** vs. competing all-flash storage per TB
- **96% less physical space** (three large freezers → three pizza boxes)
- **80-85% less water** to operate
- **5x more data per watt** of power consumption per TiB
- DirectFlash density means fewer modules, fewer racks, less cooling
- Evergreen extends product lifespan 10+ years (less e-waste)
- Evergreen//One includes an **energy efficiency SLA** (guaranteed
  watts per TiB)
- **Net zero commitment by 2040** (Scope 1 + market-based Scope 2)

*In AI-era data centers where power is the binding constraint, this
is increasingly a competitive differentiator, not just an ESG checkbox.*

---

## Rob Lee's Three-Stage Vision for Everpure

This is the framework Lee uses to describe where the company is headed:

| Stage | Focus | Status |
|-------|-------|--------|
| **1. Storage Management** | Provisioning, performance optimization, hardware lifecycle | Established. Pure's core for 15 years. |
| **2. Dataset Management** | Policy enforcement, lifecycle, protection orchestration across hybrid/multi-cloud | Where Fusion + EDC operate today. Active buildout. |
| **3. Data Management** | Contextual understanding of data itself — relationships, lineage, ownership, business relevance, semantic meaning | What 1touch/Kontextual unlocks. The future. |

His thesis: **The industry is moving from managing where data lives
to understanding what data means.** Everpure's bet is that the
company closest to the data physically (storage) is best positioned
to add intelligence on top.

---

## Smart Questions to Ask the CTO

1. "You built FlashBlade from scratch starting in 2013. What
   architectural decision from that era are you most glad you made —
   and what would you do differently?"
2. "DirectFlash is the obvious moat. How do you think about the risk
   of hyperscalers eventually building their own flash management
   in-house vs. continuing to license yours?"
3. "With 1touch, you're adding a data intelligence layer. How deep
   does that integration go — is Kontextual aware of Purity-level
   metadata, or does it sit above the stack?"
4. "FlashBlade//EXA uses third-party data nodes to scale out. That's
   a departure from the vertically integrated DFM model. How do you
   think about the open vs. proprietary tradeoff at exabyte scale?"
5. "The AI Copilot now has MCP integration — it can act, not just
   advise. What guardrails exist before it makes changes to production
   infrastructure?"
6. "You've been running Purity as a single OS across all products.
   As the portfolio expands (EXA, Portworx, Kontextual), does the
   single-OS strategy hold, or will things fork?"
7. "300 TB DFMs are coming. What's the theoretical ceiling, and what
   breaks first — the physics of NAND density or the economics?"
8. "Your Gartner positioning is highest in execution and furthest in
   vision, 12 years running. What do you see as the biggest technical
   risk to sustaining that?"

---

## Glossary (Quick Reference)

| Term | What It Means |
|------|---------------|
| **DFM / DirectFlash Module** | Everpure's proprietary flash modules — manages raw NAND at the media level, bypassing standard SSD controllers. The core hardware moat. |
| **Purity** | Unified storage operating system across all Everpure products. Handles data reduction, encryption, replication, QoS, and media management. |
| **FlashArray** | Scale-up block + file storage platform. Models: //XL (top perf), //X (mission-critical), //C (capacity), //E (archive). |
| **FlashBlade** | Scale-out file + object storage platform. Models: //S (mid-range), //EXA (AI/exabyte scale). |
| **Pure Fusion** | Intelligent control plane that virtualizes all storage into a policy-driven fleet mesh. Built into Purity, zero cost. |
| **Pure1** | Fleet management + AIOps observability platform. Includes AI Copilot. |
| **AI Copilot** | GenAI interface for storage management via natural language. Can now act via MCP integration. |
| **Portworx** | Kubernetes-native data services platform. Persistent storage, HA, DR for containerized apps. |
| **Kontextual** | 1touch's AI-first data intelligence platform. Classifies, contextualizes, and enriches enterprise data. |
| **Evergreen** | Subscription model for storage. Enables non-disruptive hardware + software upgrades, no forklift replacements. |
| **Enterprise Data Cloud (EDC)** | Everpure's unified architecture vision — storage + management + intelligence + AI-readiness. |
| **ActiveDR / ActiveCluster** | DR replication technologies. ActiveDR = continuous async. ActiveCluster = synchronous active-active (zero RPO/RTO). |
| **NVMe-oF** | NVMe over Fabrics — protocol for accessing flash storage over network at near-local latency. |
| **FTL** | Flash Translation Layer — the controller logic inside commodity SSDs that Everpure eliminates by centralizing it in Purity. |
| **MCP** | Model Context Protocol — enables AI Copilot to execute (not just suggest) infrastructure operations. |
| **QLC NAND** | Quad-Level Cell flash — denser, cheaper flash used in //C and //E capacity tiers. |
| **PSTG** | Ticker symbol (unchanged after rebrand to Everpure). |

---

*Sources: Everpure Q4 FY26 Earnings Release (Feb 25, 2026), Everpure
Investor Relations, Pure Storage technical documentation, Rob Lee
interviews (Computer Weekly, Pure Storage Blog, Cloud Wars), Gartner
Magic Quadrant reports (2025), 1touch.io press releases, Pure//
Accelerate 2025 keynotes, MLPerf/SPEC Storage benchmark publications.*
