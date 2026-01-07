# AI-Q NVIDIA Research Assistant Blueprint

## Overview

The AI-Q NVIDIA Research Assistant blueprint allows you to create a deep research assistant that can run on-premise, allowing anyone to create detailed research reports using on-premise data and web search.

---

## 🏗️ TIA Works: Construction Intelligence Architecture

### **The Living Contract Model as Source of Truth**

TIA Works is a construction intelligence platform built around a central concept: the **Living Contract Model**. This model represents the contractual truth of a project, encompassing scope, budget, and schedule. Every piece of data that flows into the system—whether from legacy systems, real-time site sensors, document ingestion, or team communications—gets continuously reconciled against this living model.

**The goal is not simply to answer questions about documents. It is to create an intelligence layer that keeps projects on budget and on schedule by detecting variances, conflicts, and risks before they become problems.**

### The Living Contract Model

The Living Contract Model is the source of truth representing three interconnected dimensions:

| **SCOPE** | **BUDGET** | **SCHEDULE** |
|-----------|------------|--------------|
| What is being built | Contract value | Baseline schedule |
| Contract requirements | Committed costs | Actual progress |
| Specifications | Projected final cost | Forecast completion |
| Approved changes | Cost to complete | Critical path |

### System Architecture

The architecture centers on the Living Contract Model, with reconciliation agents continuously comparing incoming data against this source of truth:

```
┌──────────────────────────────────────────────────────────┐
│        LIVING CONTRACT MODEL                             │
│        (Scope + Budget + Schedule = Truth)               │
└─────────────────────┬────────────────────────────────────┘
                      ↓
┌──────────────────────────────────────────────────────────┐
│        RECONCILIATION AGENTS                             │
│   Budget Agent  |  Schedule Agent  |  Scope Agent        │
└─────────────────────┬────────────────────────────────────┘
                      ↓
┌──────────────────────────────────────────────────────────┐
│                    DATA SOURCES                          │
│                                                          │
│  Legacy Systems    Project Management   Real-Time Site  │
│  • Job Costing     • Job File          • DeepStream     │
│  • Procurement     • Submittals/RFIs   • Sensors/GPS    │
│  • Dispatch        • Drawings          • WhatsApp       │
│                                                          │
│  Team Intelligence                                       │
│  • Meeting Summaries  • Daily Reports  • Field Updates  │
└──────────────────────────────────────────────────────────┘
```

### The Reconciliation Loop

This is the **heartbeat of the system**. Every input that enters TIA Works gets evaluated against the Living Contract Model:

1. **Does this affect BUDGET?** Compare: Committed Cost vs Contract Value vs Projected Final
2. **Does this affect SCHEDULE?** Compare: Actual Progress vs Baseline vs Forecast
3. **Does this affect SCOPE?** Compare: What is being built vs What is in the contract
4. **Is there a CONFLICT?** Does this contradict something else we know?

**Example: Real-Time Reconciliation**
```
Input: WhatsApp message: "Ductwork in Building B delayed 2 weeks - insulation inspection failed"

→ Agent parses: {trade: HVAC, building: B, delay: 2 weeks, cause: inspection failure}
→ Agent reconciles: Building B HVAC is on critical path
→ Agent calculates: This pushes substantial completion by 8 days
→ Agent checks contract: Liquidated damages are $5,000/day
→ Agent alerts: CRITICAL: $40K LD exposure. Recommend acceleration.
→ Agent learns: Pattern (inspection failure → delay) added to model
```

### What the SLM Must Learn

The Small Language Model deployed on the Jetson Orin is not just for answering questions. It must understand construction logic deeply enough to perform reconciliation:

**Contract Logic:**
- How change orders affect budget and schedule
- Scope changes vs means/methods distinctions
- Liquidated damages, retention, payment terms
- Submittal and approval workflow impacts

**Construction Relationships:**
- Trade dependencies (sequencing logic)
- Critical path analysis
- Procurement lead times and schedule impacts
- Weather, inspections, coordination effects

**Pattern Recognition:**
- Early warning signs of problems
- Common causes of delays and overruns
- RFI patterns indicating design issues
- Subcontractor performance indicators

### The Data Flywheel

Each project makes the system smarter for the next project. The data flywheel continuously improves the model:

```
Job Data → Agent Actions → Outcomes → Training Data → Improved Model → Job Data...
```

The model learns from real outcomes:
- "When this contractor sends this type of message, delay usually follows"
- "RFIs about structural connections have 80% chance of change order"
- "Jobs in this jurisdiction average 3 weeks longer for inspections"

### Core Agent Tasks (NVIDIA Agent Toolkit)

The NVIDIA Agent Toolkit (NAT) agents perform five continuous operations:

| Stage | Operation | Description |
|-------|-----------|-------------|
| **1. INGEST** | Data Collection | Take in data from all sources (APIs, documents, sensors, messages) |
| **2. PARSE** | Extraction | Extract structured information (costs, dates, scope items, entities) |
| **3. RECONCILE** | Comparison | Compare against the Living Contract Model (scope, budget, schedule) |
| **4. DETECT** | Analysis | Identify variances, conflicts, risks, and early warning signs |
| **5. ALERT** | Action | Notify stakeholders when things drift from plan, recommend actions |

### Technology Stack

**Cloud Layer (Training & Large Model Inference):**
- Nemotron-4 340B Instruct: Synthetic data generation from documents
- Nemotron-4 340B Reward: Quality filtering of training data
- NeMo Customizer: LoRA fine-tuning of base models
- NVIDIA Blueprints: RAG, Data Flywheel, Video Search

**Edge Layer (Field Deployment):**
- Jetson AGX Orin 64GB: 275 TOPS edge compute
- Nemotron-3 Nano (or Llama 3.2): Fine-tuned SLM for construction intelligence
- NVIDIA Agent Toolkit: Reconciliation agent framework
- DeepStream: Video analytics and sensor processing
- Triton Inference Server: Model serving with TensorRT-LLM
- Supabase: Real-time data sync (WhatsApp, field updates)

### Customization Goal for this Repository

Transform the AI-Q Research Assistant into the **Reconciliation Research Engine** that continuously audits job site activities against the Living Contract Model, detects deviations, and generates actionable intelligence to keep projects on cost and on schedule.

---

## 🔗 Related Repositories & Integration Architecture

### Associated TIA Works Repositories:

#### 1. **NIM Repository** (Local)
**Location:** `C:\Users\MatthewSmith\Projects\NIM`  
**Purpose:** NVIDIA Inference Microservices examples and RAG patterns

**Key Components:**
- **NVIDIA NIM Overview** - Documentation on optimized inference microservices
- **Basic RAG Examples** - LangChain and LlamaIndex implementations
- **Advanced RAG Patterns:**
  - `multimodal_rag` - Process contracts, charts, photos, blueprints
  - `structured_data_rag` - Query cost schedules, living contracts, structured construction data
  - `multi_turn_rag` - Follow-up questions for deeper investigation
  - `query_decomposition_rag` - Break complex compliance questions into targeted searches
- **test_rag.py** - Working test scripts for document upload and queries

**Integration Value:**
- Provides proven RAG patterns for construction document processing
- Multi-modal support for drawings, photos, schedules, and contracts
- Query decomposition enables complex compliance analysis
- Test scripts accelerate development of compliance-specific queries

#### 2. **snow-phone Repository** (HC-Build)
**Purpose:** WhatsApp interface for field workers to interact with DeCerTu intelligence platform

**Key Features:**

**SEEK Mode (Query Interface):**
- Field workers text questions via WhatsApp
- Routes to TIA API → searches project data → returns answers with citations
- Examples: "What's tomorrow's delivery schedule?" "Where is electrical panel floor 3?" "Show HVAC drawings"

**PROVIDE Mode (Evidence Capture):**
- 📸 Progress photos → Auto-tagged, matched to schedule
- 🎥 Delivery videos → VSS processes to identify materials, vehicles, vendors
- 🎤 Voice notes → Transcribed and searchable
- 📄 Documents/receipts → OCR'd and filed to correct project

**Gang Box Recordings:**
- Voice-activated crew meeting transcription
- Extracts action items, identifies safety topics
- Updates project board automatically

**Proactive Alerts:**
- Delivery notifications ("🚚 Arriving in 30 min")
- Safety alerts (weather, hazards)
- Schedule updates

**Integration Flow:**
```
WhatsApp ← → snow-phone ← → TIA API ← → DeCerTu Intelligence
                                    ↓
                          [This ent_rag_search repo]
                                    ↓
                         Context-Aware RAG answers
                         VSS Blueprint video analysis
                         Reconciliation engine
```

**Integration Value:**
- Zero-friction field data collection (everyone knows WhatsApp)
- Real-time evidence capture as work happens
- Automatic organization and tagging via AI
- Hands-free voice interface for field conditions
- Bridges office intelligence with field reality

#### 3. **DeepStream → TIA Works PoC** (deepstream_TMI)
**Purpose:** Real-time video analytics pipeline for construction site monitoring

**Architecture:**
```
Ring Cameras → DeepStream (AWS/Jetson) → JSON Events → Kafka → TIA Works → TimescaleDB
                    ↓
          Object Detection (ResNet10)
          Object Tracking (NvDCF)
          Metadata Extraction
```

**Key Components:**

**Phase 1 - AWS PoC (95% Complete):**
- AWS EC2 g4dn.xlarge (NVIDIA T4 GPU, ~$0.53/hr)
- NVIDIA DeepStream 8.0 container
- Primary detector: ResNet10 (person, car, truck, bicycle)
- Object tracker with persistent IDs
- JSON event serializer
- Test video validation (150MB sample)

**Event Schema:**
```json
{
  "event_id": "550e8400-...",
  "timestamp": "2026-01-05T14:32:15.123Z",
  "camera_id": "cam_lobby_01",
  "object_id": 42,
  "class": "person",
  "confidence": 0.94,
  "bbox": {"x": 0.35, "y": 0.42, "width": 0.12, "height": 0.28},
  "metadata": {
    "speed": 1.2,
    "direction": 87,
    "zone_id": "zone_entrance",
    "dwell_time_sec": 3.4
  }
}
```

**Phase 2 - Kafka Integration (Next):**
- DeepStream → Kafka topic (`deepstream.detections`)
- TIA Kafka Connector → TimescaleDB
- Ring-MQTT bridge for RTSP streams
- Target: <5 sec end-to-end latency, no event loss, 7-day retention

**Phase 3 - Edge Deployment (Jetson AGX Orin 64GB):**
- Same DeepStream SDK (no code changes from AWS)
- Process 8-16+ camera streams simultaneously
- Low latency (no cloud hop), lower opex
- 64GB RAM, 2048 CUDA cores
- Hardware video decode (8x 4K@30fps)
- Deployment: Ring Cameras → Jetson AGX Orin → Kafka → TIA Works

**Performance Targets:**
- Phase 1: 30 FPS, 10+ events/sec, <100ms detection latency
- Phase 2: <5 sec end-to-end, no event loss
- Phase 3: 8+ concurrent streams, 99.9% uptime

**Integration Value:**
- Real-time visual monitoring of job site activities
- Automated detection of personnel, vehicles, equipment
- Object tracking with persistent IDs across frames
- Dwell time and zone analytics for safety compliance
- Integration with compliance engine via structured events
- Time-series analytics for trend detection
- Edge processing for low latency and reduced bandwidth

**Construction Use Cases:**
- Safety monitoring (PPE detection, restricted zone violations)
- Delivery tracking (vehicle arrival, material identification)
- Workforce analytics (crew size, productivity patterns)
- Equipment utilization (idle time, movement tracking)
- Security (unauthorized access, after-hours activity)
- Progress verification (visual confirmation of completed work)

**Quick Start (AWS):**
```bash
ssh -i deepstream-key.pem ubuntu@3.236.97.46
cd deepstream_TMI
docker-compose up deepstream-app
tail -f deepstream/output/events.json
```

---

## 🏗️ Proposed Ultimate Construction Intelligence Platform

### Integration Architecture Vision:

```
┌─────────────────────────────────────────────────────────────┐
│                    Field Layer (Input)                       │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │  WhatsApp    │  │ Field Photos │  │ Gang Box     │     │
│  │  Interface   │  │  & Videos    │  │  Recordings  │     │
│  │ (snow-phone) │  │              │  │              │     │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘     │
│         │                  │                  │             │
│  ┌──────┴──────────────────┴──────────────────┴───────┐   │
│  │  Ring Cameras (8-16 streams)                        │   │
│  │  → DeepStream on Jetson AGX Orin                    │   │
│  │  → Real-time object detection/tracking              │   │
│  │  → Structured JSON events                           │   │
│  └─────────────────────────┬────────────────────────────┘   │
└────────────────────────────┼─────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────┐
│              Intelligence Processing Layer                   │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  TIA API Gateway (snow-phone backend)                │  │
│  │  • User authentication & routing                     │  │
│  │  • Intent classification (SEEK vs PROVIDE)           │  │
│  │  • Media processing & storage                        │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       ↓                                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Kafka Event Streaming                               │  │
│  │  • deepstream.detections (real-time video events)    │  │
│  │  • field.evidence (WhatsApp submissions)             │  │
│  │  • compliance.alerts (automated notifications)       │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       ↓                                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  DeCerTu Intelligence Core                           │  │
│  │  • VSS Blueprint (video/photo analysis)              │  │
│  │  • DeepStream event processing                       │  │
│  │  • Reconciliation engine (contract matching)         │  │
│  │  • Context-aware processing                          │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       ↓                                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  AI-Q Research Assistant (ent_rag_search)            │  │
│  │  • Contract compliance research                      │  │
│  │  • Cost & schedule deviation detection               │  │
│  │  • Automated compliance reports                      │  │
│  │  • Living contract synchronization                   │  │
│  │  • Predictive risk analysis                          │  │
│  │  • Video analytics integration                       │  │
│  └────────────────────┬─────────────────────────────────┘  │
│                       ↓                                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Advanced RAG Layer (NIM patterns)                   │  │
│  │  • Multimodal RAG (drawings, photos, contracts)      │  │
│  │  • Structured data RAG (schedules, budgets)          │  │
│  │  • Query decomposition (complex compliance)          │  │
│  │  • Multi-turn conversations                          │  │
│  └────────────────────┬─────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────────┐
│                  Foundation Layer                            │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ NVIDIA NIMs │  │TimescaleDB/ │  │  Document   │        │
│  │   LLMs      │  │Vector Store │  │   Storage   │        │
│  │  Embedding  │  │  (Milvus)   │  │   (MinIO)   │        │
│  │  Reranking  │  │             │  │             │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

### Integration Benefits:

**For Field Workers:**
- Ask questions via familiar WhatsApp interface
- Capture evidence hands-free (photos, video, voice)
- Get instant answers from project intelligence
- Zero training required

**For Project Managers:**
- Real-time compliance monitoring across all job site activities
- Evidence-backed answers to stakeholder questions (seconds vs. hours)
- Proactive alerts before issues become crises
- Automatic correlation of field evidence to contract terms

**For Executives:**
- Portfolio-wide risk visibility
- Predictive insights on cost/schedule performance
- Audit trails with citations to source documents
- Confidence in data-driven decision making

**Data Flow Examples:**

**Example 1: Manual Evidence Submission**
```
1. Foreman takes photo of concrete pour → WhatsApp
2. snow-phone processes → TIA API
3. VSS analyzes quality → DeCerTu
4. ent_rag_search checks against contract specs
5. NIM multimodal RAG confirms compliance
6. Result: "✅ Concrete pour meets contract spec 5.2.3
   Evidence: Photo timestamp, contract citation, spec document"
7. Automatic update to project board and compliance dashboard
```

**Example 2: Automated Video Analytics**
```
1. Ring camera captures delivery truck arrival → DeepStream
2. Object detection: "truck" + "person" + dwell time 8.3 min
3. Event published → Kafka → TIA Works
4. ent_rag_search queries: "Expected delivery today?"
5. Matches contract schedule: "Acme HVAC - 4 AHU units"
6. VSS analyzes video for cargo identification
7. Reconciliation engine confirms delivery vs contract
8. Alert to PM: "✅ Scheduled delivery confirmed
   Timestamp: 2026-01-07 09:43:21
   Contract item: AHU-042 through AHU-045
   Video evidence: 8.3 min unloading time"
9. Automatic material receipt logging + schedule update
```

**Example 3: Safety Compliance**
```
1. DeepStream detects person in restricted zone (cam_zone_5)
2. Event: object_class=person, zone_id=restricted, dwell_time=12s
3. Kafka → compliance.alerts topic
4. ent_rag_search checks contract safety requirements
5. Violation detected: "Section 8.3.2 - No entry without escort"
6. Alert to safety officer + field supervisor via snow-phone
7. WhatsApp notification: "⚠️ Safety Alert - Unauthorized zone entry
   Location: Zone 5 (East wing foundation)
   Time: 14:23:15
   Duration: 12 seconds
   Required action: Immediate supervisor review"
```

### Development Roadmap Notes for Ali:

**Phase 1: Foundation (Current)**
- Deploy ent_rag_search base system
- Test NIM RAG patterns with construction documents
- Validate core compliance monitoring workflows

**Phase 2: Field Integration (Next)**
- Integrate snow-phone WhatsApp interface
- Connect evidence capture to compliance engine
- Implement real-time alerting

**Phase 3: Intelligence Loop (Future)**
- Close loop: field evidence → compliance analysis → predictive insights → field alerts
- Gang box meeting analysis → automatic task creation
- Delivery video → reconciliation → budget impact

**Repository Connection Decision Points:**
1. Keep repositories separate with API boundaries? (Recommended for modularity)
2. Merge into monorepo? (Simpler deployment, harder to maintain)
3. Docker Compose orchestration across repos? (Middle ground)

**Ali - Your decision on architecture approach will determine integration strategy.**

---

## Table of Contents

- [AI-Q NVIDIA Research Assistant Blueprint](#ai-q-nvidia-research-assistant-blueprint)
  - [Overview](#overview)
  - [Table of Contents](#table-of-contents)
  - [Key Features](#key-features)
  - [Target Audience](#target-audience)
  - [Software Components](#software-components)
  - [Technical Diagram](#technical-diagram)
  - [Minimum System Requirements](#minimum-system-requirements)
    - [Disk Space](#disk-space)
    - [OS Requirements](#os-requirements)
    - [Deploy Options](#deploy-options)
    - [Drivers](#drivers)
    - [Hardware Requirements](#hardware-requirements)
      - [Docker Compose](#docker-compose)
      - [Helm](#helm)
      - [Running with hosted NVIDIA NIM Microservices](#running-with-hosted-nvidia-nim-microservices)
    - [API Keys](#api-keys)
  - [Next Steps](#next-steps)
  - [License](#license)
  - [Security Considerations](#security-considerations)

## Key Features

- **Deep Research**: Given a report topic and desired report structure, an agent (1) creates a report plan, (2) searches data sources for answers, (3) writes a report, (4) reflects on gaps in the report for further queries, (5) finishes a report with a list of sources.
- **Parallel Search**: During the research phase, multiple research questions are searched in parallel. For each query, the RAG service is consulted and an LLM-as-a-judge is used to check the relevancy of the results. If more information is needed, a fallback web search is performed. This search approach ensures internal documents are given preference over generic web results while maintaining accuracy. Performing query search in parallel allows for many data sources to be consulted in an efficient manner.
- **Human-in-the-loop**: Human feedback on the report plan, interactive report edits, and Q&A with the final report.
- **Data Sources**: Integration with the NVIDIA RAG blueprint to search multimodal documents with text, charts, and tables. Optional web search through Tavily.
- **Demo Web Application**: Frontend web application showcasing end-to-end use of the AI-Q Research Assistant.


## Target Audience

- *Research Analysts:* This blueprint can be deployed by IT organizations to provide an on-premise deep research application for analysts
- *Developers:* This blueprint serves as a reference architecture for teams to adapt to their own AI research applications  

## Software Components 

The AI-Q Research Assistant blueprint provides these components:

- **Demo Frontend**: A docker container with a fully functional demo web application is provided. This web application is deployed by default if you follow the getting started guides and is the easiest way to quickly experiment with deep research using internal data sources via the NVIDA RAG blueprint. The source code for this demo web application is not distributed. 
- **Backend Service via RESTful API**: The main AI-Q Research Assistant code is distributed as the `aiq-aira` Python package located in the `/aira` directory. These backend functions are available directly or via a RESTful API.
- **Middleware Proxy**: An nginx proxy is deployed as part of the getting started guides. This proxy enables frontend web applications to interact with a single backend service. In turn, the proxy routes requests between the NVIDIA RAG blueprint services and the AI-Q Research Assistant service.

Additionally, the blueprint uses these components:

- [**NVIDIA NeMo Agent Toolkit**](https://github.com/NVIDIA/NeMo-Agent-Toolkit)
  Provides a toolkit for managing a LangGraph codebase. Provides observability, API services and documentation, and easy configuration of different LLMs.
- [**NVIDIA RAG Blueprint**](https://github.com/NVIDIA-AI-Blueprints/rag)
  Provides a solution for querying large sets of on-premise multi-modal documents.
- [**NVIDIA NeMo Retriever Microservices**](https://developer.nvidia.com/nemo-retriever?sortBy=developer_learning_library%2Fsort%2Ffeatured_in.nemo_retriever%3Adesc%2Ctitle%3Aasc&hitsPerPage=12)
- [**NVIDIA NIM Microservices**](https://developer.nvidia.com/nim?sortBy=developer_learning_library%2Fsort%2Ffeatured_in.nim%3Adesc%2Ctitle%3Aasc&hitsPerPage=12) 
  Used through the RAG blueprint for multi-modal document ingestion.
  Provides the foundational LLMs used for report writing and reasoning, including the llama-3_3-nemotron-super-49b-v1_5 reasoning model.
- [**Web search powered by Tavily**](https://tavily.com/)
  Supplements on-premise sources with real-time web search.

## Technical Diagram  

![Architecture Diagram](https://assets.ngc.nvidia.com/products/api-catalog/aiq/diagram.jpg?)

## Minimum System Requirements 

### Disk Space

250 GB minimum

### OS Requirements

Ubuntu 22.04

### Deploy Options 

[Docker Compose](docs/get-started/get-started-docker-compose.md)  
[NVIDIA AI Workbench](deploy/workbench/README.md#get-started)  
[Helm](docs/get-started/get-started-helm.md)

### Drivers

NVIDIA Container ToolKit  
GPU Driver -  530.30.02 or later  
CUDA version - 12.6 or later

> **Note:** Mixed MIG support in Helm deployment requires GPU operator 25.3.2 or higher and GPU Driver 570.172.08 or higher.

### Hardware Requirements

The following are the hardware requiremnts for running all services locally using Docker Compose and Helm Chart deployment.

#### Docker Compose

Use | Service(s)| Recommended GPU* 
--- | --- | --- 
Nemo Retriever Microservices for multi-modal document ingest | `graphic-elements`, `table-structure`, `paddle-ocr`, `nv-ingest`, `embedqa` | 1 x H100 80GB*  <br /> 1 x A100 80GB <br /> 1 x B200 <br /> 1 x RTX PRO 6000
Reasoning Model for Report Generation and RAG Q&A Retrieval | `llama-3_3-nemotron-super-49b-v1_5` with a FP8 profile  | 1 x H100 80 GB* <br /> 2 x A100 80GB <br /> 2 x B200 <br /> 1 x RTX PRO 6000
Instruct Model for Report Generation | `llama-3.3-70b-instruct` | 2 x H100 80GB* <br /> 4 x A100 80GB <br /> 2 x B200 <br /> 2 x RTX PRO 6000
**Total** | Entire AI-Q Research Blueprint | 4 x H100 80GB* <br /> 7 x A100 80GB <br /> 5 x B200 <br /> 4 x RTX PRO 6000

  *This recommendation is based off of the configuration used to test the blueprint. For alternative configurations, view the [RAG blueprint documentation](https://github.com/NVIDIA-AI-Blueprints/rag/blob/main/docs/support-matrix.md).

#### Helm

| Option | RAG Deployment | AI-Q Research Assistant Deployment | Total Hardware Requirement |
|--------|----------------|-----------------|---------------------------|
| Single Node - MIG Sharing | [Use MIG sharing](https://github.com/NVIDIA-AI-Blueprints/rag/blob/main/docs/mig-deployment.md) | [Default Deployment](#deploy-the-ai-q-research-assistant) | 4 x H100 80GB for RAG<br/>2 x H100 80GB for AI-Q Research Assistant<br/> |
| Multi Node | [Default Deployment](https://github.com/NVIDIA-AI-Blueprints/rag/blob/main/docs/deploy-helm.md) | [Default Deployment](#deploy-the-ai-q-research-assistant) | 8 x H100 80GB for RAG<br/>2 x H100 80GB for AI-Q Research Assistant<br/>---<br/>9 x A100 80GB for RAG<br/>4 x A100 80GB for AI-Q Research Assistant<br/>---<br/>9 x B200 for RAG<br/>2 x B200 for AI-Q Research Assistant<br/>---<br/>8 x RTX PRO 6000 for RAG<br/>2 x RTX PRO 6000 for AI-Q Research Assistant|


#### Running with hosted NVIDIA NIM Microservices

This blueprint can be run entirely with hosted NVIDIA NIM Microservices, see [https://build.nvidia.com/](https://build.nvidia.com/) for details.


### API Keys
- NVIDIA AI Enterprise developer licence required to local host NVIDIA NIM Microservices.
- NVIDIA [API catalog](https://build.nvidia.com/) or [NGC](https://org.ngc.nvidia.com/setup/personal-keys) API Keys for container download and access to hosted NVIDIA NIM Microservices
- [TAVILY API Key](https://tavily.com) for optional web search


## Next Steps

- Use the [Get Started Notebook](notebooks/get_started_nvidia_api.ipynb) to deploy the blueprint with Docker and interact with the sample web application  
- Deploy with [Docker Compose](docs/get-started/get-started-docker-compose.md) or [Helm](docs/get-started/get-started-helm.md)
- Customize the research assistant starting with the [Local Development Guide](docs/local-development.md)  

## License

This project will download and install additional third-party open source software projects. Review the license terms of these open source projects before use, found in [License-3rd-party.txt](LICENSE-3rd-party.txt). 

GOVERNING TERMS: AIQ blueprint software and materials are governed by the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0), and: (a) the models, other than the Llama-3.3-Nemotron-Super-49B-v1 model, are governed by the [NVIDIA Community Model License](https://www.nvidia.com/en-us/agreements/enterprise-software/nvidia-community-models-license/); (b) the Llama-3.3-Nemotron-Super-49B-v1 model is governed by the [NVIDIA Open Model License Agreement](https://developer.download.nvidia.com/licenses/nvidia-open-model-license-agreement-june-2024.pdf), and (c) the NeMo Retriever extraction is released under the [Apache-2.0 license](https://github.com/NVIDIA/nv-ingest/blob/main/LICENSE).

ADDITIONAL INFORMATION: For NVIDIA Retrieval QA Llama 3.2 1B Reranking v2 model, NeMo Retriever Graphic Elements v1 model, and NVIDIA Retrieval QA Llama 3.2 1B Embedding v2: [Llama 3.2 Community License Agreement](https://www.llama.com/llama3_2/license/), Built with Llama. For Llama-3.3-70b-Instruct model, [Llama 3.3 Community License Agreement](https://www.llama.com/llama3_3/license/), Built with Llama.

## Security Considerations

- **Prompt Content Filtering**: The AI-Q Research Assistant includes input validation that detects and blocks user prompts containing suspicious text patterns (e.g., "ignore all instructions", "DROP TABLE", "eval()", etc.). This helps reduce the risk of prompt injection but does not provide protection against SQL injection, code execution, or XSS—those require proper security controls at the database, application, and output layers respectively. See [Prompt Content Filtering Tests](docs/security-testing.md) for basic testing examples.
- The AI-Q Research Assistant Blueprint doesn't generate any code that may require sandboxing.
- The AI-Q Research Assistant Blueprint is shared as a reference and is provided "as is". The security in the production environment is the responsibility of the end users deploying it. When deploying in a production environment, please have security experts review any potential risks and threats; define the trust boundaries, implement logging and monitoring capabilities, secure the communication channels, integrate AuthN & AuthZ with appropriate access controls, keep the deployment up to date, ensure the containers/source code are secure and free of known vulnerabilities.
- A frontend that handles AuthN & AuthZ should be in place as missing AuthN & AuthZ could provide ungated access to customer models if directly exposed to e.g. the internet, resulting in either cost to the customer, resource exhaustion, or denial of service.
- The AI-Q Research Assistant doesn't require any privileged access to the system.
- The end users are responsible for ensuring the availability of their deployment.
- The end users are responsible for building the container images and keeping them up to date.
- The end users are responsible for ensuring that OSS packages used by the developer blueprint are current.
- The logs from nginx proxy, backend, and demo app are printed to standard out. They can include input prompts and output completions for development purposes. The end users are advised to handle logging securely and avoid information leakage for production use cases.
