# TIA Works Construction Intelligence System
## Complete System Architecture & Repository Integration Guide

**Last Updated:** January 7, 2026  
**Organization:** Harlan Charles Inc. | NVIDIA Inception Program  
**Purpose:** Living documentation for all TIA Works developers

---

## 🎯 System Mission

**Create an intelligence layer that keeps construction projects on budget and on schedule by detecting variances, conflicts, and risks before they become problems.**

TIA Works is not a document Q&A system. It is a **Living Contract Model reconciliation engine** that continuously compares every piece of job site data against the contractual source of truth (Scope + Budget + Schedule) to provide proactive, actionable intelligence.

---

## 🏗️ The Living Contract Model (Core Concept)

The **Living Contract Model** is the source of truth representing three interconnected dimensions:

| **SCOPE** | **BUDGET** | **SCHEDULE** |
|-----------|------------|--------------|
| What is being built | Contract value | Baseline schedule |
| Contract requirements | Committed costs | Actual progress |
| Specifications | Projected final cost | Forecast completion |
| Approved changes | Cost to complete | Critical path |

**Every data input gets reconciled against this model through continuous agent operations:**

1. **Does this affect BUDGET?** Compare: Committed Cost vs Contract Value vs Projected Final
2. **Does this affect SCHEDULE?** Compare: Actual Progress vs Baseline vs Forecast
3. **Does this affect SCOPE?** Compare: What is being built vs What is in the contract
4. **Is there a CONFLICT?** Does this contradict something else we know?

---

## 📊 Four Primary Data Sources

The Living Contract Model is fed by **four primary data source layers:**

```
┌─────────────────────────────────────────────────────────────────┐
│                 LIVING CONTRACT MODEL                            │
│           (Scope + Budget + Schedule = Truth)                    │
└─────────────────────┬───────────────────────────────────────────┘
                      ↓
        ┌─────────────┴─────────────┐
        │  RECONCILIATION AGENTS     │
        └─────────────┬───────────────┘
                      ↓
┌─────────────────────────────────────────────────────────────────┐
│                    DATA SOURCES (4 Layers)                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. LEGACY SYSTEMS          → Financial Reconciliation          │
│     • Job Costing                                               │
│     • Procurement                                               │
│     • Dispatch                                                  │
│                                                                  │
│  2. PROJECT MANAGEMENT      → Scope Reconciliation              │
│     • Job Files                                                 │
│     • Submittals/RFIs                                           │
│     • Drawings                                                  │
│                                                                  │
│  3. REAL-TIME SITE          → Site Activity Reconciliation      │
│     • deepstream_TMI (video analytics)                          │
│     • Sensors/GPS/RFID                                          │
│     • Ring Cameras                                              │
│                                                                  │
│  4. TEAM INTELLIGENCE       → Communication Reconciliation      │
│     • snow-phone (WhatsApp interface)                           │
│     • Meeting summaries                                         │
│     • Daily reports                                             │
│     • Field updates                                             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🗂️ Repository Architecture

### Repository Overview

| Repository | Primary Role | Data Flow Position | Key Technology |
|-----------|--------------|-------------------|----------------|
| **ent_rag_search** | Reconciliation Research Engine | Processing Layer | NVIDIA AI-Q, RAG, LangGraph |
| **deepstream_TMI** | Real-Time Video Analytics | Data Source #3 | DeepStream, Jetson, Kafka |
| **snow-phone** | Field Worker Interface | Data Source #4 | WhatsApp, Supabase, TIA API |
| **NIM** | RAG Pattern Library | Processing Layer | NVIDIA NIM, Advanced RAG |
| **tia-works-agents** | Agent Framework | Core Reconciliation | NVIDIA Agent Toolkit |
| **tia-works-schema** | Data Models | Foundation | PostgreSQL, TimescaleDB |
| **tia-works-data-flywheel** | Learning Pipeline | Intelligence Layer | Nemotron, NeMo Customizer |

---

## 📦 Repository Details

### 1. **ent_rag_search** - Reconciliation Research Engine
**GitHub:** `BluJoB/ent_rag_search`  
**Based On:** NVIDIA AI-Q Research Assistant Blueprint

**Purpose:**  
Transform the AI-Q Research Assistant into a construction-specific reconciliation research engine that continuously audits job site activities against the Living Contract Model.

**Core Capabilities:**
- **Contract Compliance Research** - Ingest and analyze contracts, living contract models, cost baselines, schedules
- **Cost & Schedule Deviation Detection** - Compare actual vs. planned across all data sources
- **Automated Compliance Reports** - Generate reports with variance analysis and recommendations
- **Living Contract Synchronization** - Monitor and alert on contract changes
- **Predictive Risk Analysis** - Pattern recognition for early warning signs

**What It Does:**
- Receives queries from field workers (via snow-phone)
- Searches across multimodal documents (contracts, drawings, schedules, photos)
- Performs complex compliance analysis using query decomposition
- Returns answers with citations to source documents
- Triggers reconciliation workflows when variances detected

**Key Technologies:**
- NVIDIA AI-Q Blueprint
- LangGraph agent framework
- NVIDIA NIM microservices (LLMs, embeddings, reranking)
- Milvus vector database
- MinIO object storage

**Integration Points:**
- **Consumes From:** snow-phone queries, deepstream_TMI events, legacy system APIs, project management data
- **Provides To:** TIA API responses, compliance alerts, reconciliation reports
- **Depends On:** NIM patterns (multimodal RAG, structured data RAG), tia-works-schema

**Success Metrics:**
- Query response time: <5 seconds
- Citation accuracy: >95%
- Variance detection rate: >90%

---

### 2. **deepstream_TMI** - Real-Time Video Analytics Pipeline
**Repository:** `deepstream_TMI` (AWS/Jetson deployment)

**Purpose:**  
Provide real-time video analytics that feed site observation data into TIA Works' Living Contract Model reconciliation system.

**Role in Ecosystem:**  
**Data Source #3 - Real-Time Site Reconciliation**

**What It Does:**
- Ingests camera streams (Ring cameras, IP cameras, RTSP sources)
- Detects and tracks objects (workers, equipment, vehicles, materials)
- Extracts structured event data (timestamps, locations, object classes, tracking IDs)
- Streams JSON events to TIA Works for contract reconciliation

**Event Schema:**
```json
{
  "event_id": "uuid",
  "timestamp": "2026-01-05T14:32:15.123Z",
  "camera_id": "site_zone_identifier",
  "object_id": 42,
  "class": "person|equipment|vehicle|material",
  "confidence": 0.94,
  "bbox": {"x": 0.35, "y": 0.42, "width": 0.12, "height": 0.28},
  "metadata": {
    "zone_id": "foundation_zone_b",
    "dwell_time_sec": 3.4,
    "activity": "forming"
  }
}
```

**Reconciliation Agent Integration:**
- **Labor reconciliation:** Detected worker counts vs budgeted crew sizes
- **Schedule validation:** Observed activities vs planned tasks
- **Equipment utilization:** Asset presence vs procurement/dispatch records
- **Material tracking:** Delivery detection vs procurement orders
- **Safety compliance:** PPE detection, hazard zone monitoring

**Key Technologies:**
- NVIDIA DeepStream 8.0
- Jetson AGX Orin 64GB (edge deployment)
- Kafka event streaming
- TimescaleDB time-series storage
- Ring-MQTT bridge

**Deployment Phases:**
- **Phase 1:** AWS EC2 PoC (95% complete)
- **Phase 2:** Kafka integration (next)
- **Phase 3:** Jetson edge deployment (8-16 camera streams per site)

**Integration Points:**
- **Consumes From:** Camera streams (RTSP, Ring cameras)
- **Provides To:** Kafka topic `deepstream.detections`, TimescaleDB events, tia-works-agents
- **Depends On:** tia-works-schema (event structure), tia-works-agents (event consumers)

**Success Metrics:**
- Phase 1: 30 FPS, 10+ events/sec, <100ms detection latency
- Phase 2: <5 sec end-to-end latency, 0% event loss
- Phase 3: 8-16 concurrent streams, 99.9% uptime

**Quick Start:**
```bash
ssh ubuntu@3.236.97.46
cd deepstream_TMI
docker-compose up deepstream-app
tail -f deepstream/output/events.json
```

---

### 3. **snow-phone** - Field Worker WhatsApp Interface
**GitHub:** `HC-Build/snow-phone`

**Purpose:**  
Provide zero-friction field interface via WhatsApp for workers to interact with the DeCerTu intelligence platform.

**Role in Ecosystem:**  
**Data Source #4 - Team Intelligence / Communication Reconciliation**

**What It Does:**

**SEEK Mode (Query Interface):**
- Field workers text questions via WhatsApp
- Routes to TIA API → searches project data → returns answers with citations
- Examples: "What's tomorrow's delivery schedule?" "Where is electrical panel floor 3?"

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
- Schedule updates from reconciliation engine

**Integration Flow:**
```
WhatsApp ← → snow-phone ← → TIA API ← → DeCerTu Intelligence
                                    ↓
                          ent_rag_search (RAG queries)
                                    ↓
                          Living Contract Model
```

**Key Technologies:**
- WhatsApp Business API / Meta CDN
- Supabase (real-time data sync)
- TIA API Gateway
- VSS Blueprint (video analysis)
- Context-aware RAG

**Integration Points:**
- **Consumes From:** WhatsApp messages, photos, videos, voice notes
- **Provides To:** TIA API queries, evidence uploads (S3), reconciliation triggers
- **Depends On:** ent_rag_search (answers), tia-works-agents (alert delivery)

**Value Proposition:**
- ✅ Zero training required (everyone knows WhatsApp)
- ✅ Offline-capable (messages queue when signal returns)
- ✅ Hands-free voice interface (perfect for field conditions)
- ✅ Instant evidence capture (as work happens, not end of day)
- ✅ Automatic organization via AI

---

### 4. **NIM** - NVIDIA Inference Microservices & RAG Patterns
**Location:** `C:\Users\MatthewSmith\Projects\NIM`

**Purpose:**  
Provide proven RAG patterns and NVIDIA inference microservice examples for construction document processing.

**What It Provides:**

**Basic RAG Examples:**
- LangChain implementation patterns
- LlamaIndex integration examples

**Advanced RAG Patterns:**
- **multimodal_rag** - Process contracts, charts, photos, blueprints
- **structured_data_rag** - Query cost schedules, living contracts, structured construction data
- **multi_turn_rag** - Follow-up questions for deeper investigation
- **query_decomposition_rag** - Break complex compliance questions into targeted searches

**Test Scripts:**
- `test_rag.py` - Document upload and query testing
- Working examples for rapid development iteration

**Key Technologies:**
- NVIDIA NIM microservices
- LangChain & LlamaIndex
- Vector databases (Milvus)
- Embedding models (NeMo Retriever)

**Integration Value:**
- Accelerates ent_rag_search customization with proven patterns
- Provides multi-modal support for construction documents
- Query decomposition enables complex compliance analysis
- Test scripts speed up development cycles

**Integration Points:**
- **Provides To:** ent_rag_search (RAG pattern implementations)
- **Used By:** Developers building custom reconciliation queries

---

### 5. **tia-works-agents** - Agent Framework
**Purpose:**  
Core reconciliation agent framework using NVIDIA Agent Toolkit (NAT).

**Core Agent Operations:**

| Stage | Operation | Description |
|-------|-----------|-------------|
| **1. INGEST** | Data Collection | Take in data from all sources (APIs, documents, sensors, messages) |
| **2. PARSE** | Extraction | Extract structured information (costs, dates, scope items, entities) |
| **3. RECONCILE** | Comparison | Compare against the Living Contract Model (scope, budget, schedule) |
| **4. DETECT** | Analysis | Identify variances, conflicts, risks, and early warning signs |
| **5. ALERT** | Action | Notify stakeholders when things drift from plan, recommend actions |

**Agent Types:**
- **Budget Agent** - Financial reconciliation (committed vs contract vs projected)
- **Schedule Agent** - Timeline reconciliation (actual vs baseline vs forecast)
- **Scope Agent** - Deliverables reconciliation (built vs contracted)
- **Conflict Detection Agent** - Cross-checks for contradictions
- **Pattern Learning Agent** - Data flywheel for predictive intelligence

**Key Technologies:**
- NVIDIA Agent Toolkit (NAT)
- LangGraph orchestration
- Event-driven architecture (Kafka)

**Integration Points:**
- **Consumes From:** All four data sources (legacy systems, project management, deepstream_TMI, snow-phone)
- **Provides To:** Alerts, recommendations, variance reports
- **Depends On:** tia-works-schema, ent_rag_search

---

### 6. **tia-works-schema** - Data Models & Schema Definitions
**Purpose:**  
Define unified data models for the Living Contract Model and all event schemas.

**What It Defines:**
- Living Contract Model schema (scope, budget, schedule)
- DeepStream event schema (video analytics)
- snow-phone message schema (WhatsApp interactions)
- Legacy system integration schemas (job costing, procurement)
- TimescaleDB table structures
- API contracts between services

**Key Technologies:**
- PostgreSQL / TimescaleDB
- JSON Schema validation
- OpenAPI specifications

**Integration Points:**
- **Used By:** All repositories for data interchange
- **Provides:** Schema validation, type definitions, API contracts

---

### 7. **tia-works-data-flywheel** - Learning Pipeline
**Purpose:**  
Implement the data flywheel that makes each project improve the next.

**What It Does:**
```
Job Data → Agent Actions → Outcomes → Training Data → Improved Model → Job Data...
```

**Learning Outcomes:**
- "When this contractor sends this type of message, delay usually follows"
- "RFIs about structural connections have 80% chance of change order"
- "Jobs in this jurisdiction average 3 weeks longer for inspections"

**Key Technologies:**
- Nemotron-4 340B Instruct (synthetic data generation)
- Nemotron-4 340B Reward (quality filtering)
- NeMo Customizer (LoRA fine-tuning)
- NVIDIA Data Flywheel Blueprint

**Integration Points:**
- **Consumes From:** All agent actions and outcomes
- **Provides To:** Improved SLM models for edge deployment
- **Trains:** Nemotron-3 Nano / Llama 3.2 for Jetson deployment

---

## 🔄 Complete Data Flow Examples

### Example 1: Manual Evidence Submission via WhatsApp
```
1. Foreman takes photo of concrete pour → WhatsApp
2. snow-phone receives → uploads to S3 → notifies TIA API
3. VSS Blueprint analyzes photo quality → extracts metadata
4. ent_rag_search queries Living Contract Model: "Contract spec for concrete?"
5. NIM multimodal RAG searches contract documents + drawings
6. Reconciliation agent compares: photo metadata vs contract spec 5.2.3
7. Result: "✅ Concrete pour meets contract requirements"
   Evidence: Photo timestamp, contract citation, spec document
8. Automatic update to project board and compliance dashboard
9. Data flywheel logs: successful verification for training
```

### Example 2: Automated Video Analytics Detection
```
1. Ring camera captures delivery truck arrival (09:43:21)
2. DeepStream detects: object_class=truck, dwell_time=8.3min, zone=loading_dock
3. Event published → Kafka topic: deepstream.detections
4. Reconciliation agent receives event → queries tia-works-agents
5. Schedule agent checks: "Expected delivery today?" → Yes: Acme HVAC, 4 AHU units
6. VSS analyzes video frames → confirms cargo matches AHU dimensions
7. Budget agent verifies: contract item AHU-042 through AHU-045
8. Alert to PM via snow-phone: "✅ Scheduled delivery confirmed
   Timestamp: 2026-01-07 09:43:21
   Contract items: AHU-042 to AHU-045
   Video evidence: 8.3 min unloading time"
9. Automatic material receipt logging + schedule progress update
10. Data flywheel learns: delivery pattern for this vendor
```

### Example 3: Safety Violation Detection
```
1. DeepStream detects: person in restricted zone (zone_5, 12 seconds)
2. Event: {object_class: person, zone_id: restricted, dwell_time: 12s}
3. Kafka → compliance.alerts topic
4. Scope agent queries ent_rag_search: "Safety requirements for zone 5?"
5. RAG returns: Contract section 8.3.2 - "No entry without escort"
6. Conflict detected: person present without escort record in dispatch system
7. Alert to safety officer + field supervisor via snow-phone:
   "⚠️ Safety Alert - Unauthorized zone entry
   Location: Zone 5 (East wing foundation)
   Time: 14:23:15, Duration: 12 seconds
   Required action: Immediate supervisor review"
8. Incident logged in compliance dashboard
9. Data flywheel pattern: zone violation alert type + response time
```

### Example 4: WhatsApp Query with Reconciliation
```
1. Foreman texts snow-phone: "Ductwork in Building B delayed 2 weeks - insulation inspection failed"
2. snow-phone parses: {trade: HVAC, building: B, delay: 2 weeks, cause: inspection failure}
3. TIA API routes to ent_rag_search
4. Schedule agent reconciles: Building B HVAC is on critical path
5. Agent calculates: Delay pushes substantial completion by 8 days
6. Budget agent checks contract: Liquidated damages = $5,000/day
7. Alert generated: "CRITICAL: $40K LD exposure. Recommend acceleration."
8. Response sent via snow-phone with:
   - Contract citation (liquidated damages clause)
   - Alternative mitigation strategies
   - Cost-benefit analysis of acceleration
9. Data flywheel learns: inspection failure → critical path impact pattern
```

---

## 🏛️ Technology Stack Summary

### Cloud Layer (Training & Large Model Inference)
- **Nemotron-4 340B Instruct** - Synthetic data generation from documents
- **Nemotron-4 340B Reward** - Quality filtering of training data
- **NeMo Customizer** - LoRA fine-tuning of base models
- **NVIDIA Blueprints** - RAG, Data Flywheel, Video Search

### Edge Layer (Field Deployment)
- **Jetson AGX Orin 64GB** - 275 TOPS edge compute
- **Nemotron-3 Nano / Llama 3.2** - Fine-tuned SLM for construction intelligence
- **NVIDIA Agent Toolkit** - Reconciliation agent framework
- **DeepStream 8.0** - Video analytics and sensor processing
- **Triton Inference Server** - Model serving with TensorRT-LLM

### Infrastructure
- **Kafka** - Event streaming between services
- **TimescaleDB** - Time-series event storage
- **PostgreSQL** - Relational data (contracts, schedules, budgets)
- **Milvus** - Vector database for RAG
- **MinIO** - Object storage for documents and media
- **Supabase** - Real-time sync for WhatsApp data

### APIs & Integration
- **TIA API Gateway** - Central routing and authentication
- **WhatsApp Business API** - Field worker interface
- **Ring-MQTT Bridge** - Camera stream access
- **Legacy System APIs** - Job costing, procurement, dispatch integration

---

## 📋 Development Roadmap

### Phase 1: Foundation (Current - Q1 2026)
- ✅ Deploy ent_rag_search base system
- ✅ DeepStream AWS PoC validation
- ✅ snow-phone WhatsApp interface prototype
- 🔄 Test NIM RAG patterns with construction documents
- 🔄 Validate core compliance monitoring workflows

### Phase 2: Integration (Q1-Q2 2026)
- 🚀 Kafka event streaming deployment
- 🚀 Connect deepstream_TMI to reconciliation agents
- 🚀 Integrate snow-phone evidence capture with compliance engine
- 🚀 Implement real-time alerting system
- 🚀 Deploy Living Contract Model schema

### Phase 3: Intelligence Loop (Q2 2026)
- 🚀 Close the loop: field evidence → compliance analysis → predictive insights → field alerts
- 🚀 Gang box meeting analysis → automatic task creation
- 🚀 Delivery video → reconciliation → budget impact automation
- 🚀 Pattern learning and data flywheel activation

### Phase 4: Edge Deployment (Q2-Q3 2026)
- 🚀 Flash Jetson AGX Orin devices
- 🚀 Deploy per-site edge compute with 8-16 camera streams
- 🚀 Fine-tune SLM models for construction domain
- 🚀 Multi-site coordination and portfolio analytics

### Phase 5: Scale & Optimize (Q3-Q4 2026)
- 🚀 Multi-project deployment across portfolio
- 🚀 Advanced pattern recognition and predictive analytics
- 🚀 Automated change order generation
- 🚀 Integration with accounting systems for real-time P&L

---

## 🎯 Success Metrics (System-Wide)

### Technical Performance
- **Query Response Time:** <5 seconds (field worker questions)
- **Video Processing:** 30 FPS per stream, <100ms detection latency
- **End-to-End Latency:** <5 seconds (event → reconciliation → alert)
- **System Uptime:** 99.9% availability
- **Event Loss Rate:** 0% (no dropped video analytics events)

### Business Impact
- **Variance Detection Rate:** >90% of budget/schedule deviations caught proactively
- **False Positive Rate:** <10% (alerts that don't require action)
- **Time to Answer:** Field worker questions answered 50x faster vs manual lookup
- **Cost Avoidance:** Track dollars saved through early variance detection
- **Schedule Recovery:** Days recovered through proactive intervention

### User Adoption
- **Field Worker Engagement:** >80% daily active users on snow-phone
- **Query Volume:** >100 questions per day per project
- **Evidence Submission:** >50 photos/videos per day per project
- **Alert Response Time:** <15 minutes from alert to acknowledgment

---

## 🤝 Cross-Repository Collaboration Guidelines

### When Making Changes:
1. **Check Dependencies:** Review this document for downstream impacts
2. **Update Schemas:** If changing data structures, update tia-works-schema first
3. **Test Integration:** Validate changes don't break cross-repo data flows
4. **Document Changes:** Update this document if architecture changes

### Communication Channels:
- **Architecture Questions:** Tag `@ali` (dev team leader)
- **Schema Changes:** Notify all repo maintainers before merging
- **Breaking Changes:** Require approval from 2+ repo owners
- **Integration Issues:** Create cross-repo GitHub issues with `[INTEGRATION]` tag

### Development Environment:
- **Local Development:** Use Docker Compose for multi-service testing
- **Staging:** Shared staging environment with all services integrated
- **Production:** Kubernetes deployment with service mesh

---

## 📞 Key Contacts

**System Architecture:** Matthew Smith (TIA Works)  
**Development Lead:** Ali (HC-Build)  
**NVIDIA Inception:** Harlan Charles Inc.  
**GitHub Organizations:** BluJoB, HC-Build

---

## 📚 Additional Resources

- **NVIDIA AI-Q Blueprint:** https://github.com/NVIDIA-AI-Blueprints/rag
- **NVIDIA Agent Toolkit:** https://github.com/NVIDIA/NeMo-Agent-Toolkit
- **DeepStream Documentation:** https://developer.nvidia.com/deepstream-sdk
- **Jetson AGX Orin:** https://developer.nvidia.com/embedded/jetson-agx-orin

---

**This is a living document. All developers should contribute updates as the system evolves.**

*Last Updated: January 7, 2026*
