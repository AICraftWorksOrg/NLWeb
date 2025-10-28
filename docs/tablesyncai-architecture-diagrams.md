# TableSyncAI + NLWeb Architecture Diagrams

## Phase 1: Natural Language Text Interface

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         TableSyncAI Application                              │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────┐    │
│  │                     TableSyncAI Backend                            │    │
│  │                                                                    │    │
│  │  ┌──────────────┐        ┌─────────────────┐                     │    │
│  │  │   Sync Jobs  │        │  Configuration  │                     │    │
│  │  │   Database   │        │    Database     │                     │    │
│  │  └──────┬───────┘        └────────┬────────┘                     │    │
│  │         │                         │                               │    │
│  │         └─────────────┬───────────┘                               │    │
│  │                       │                                           │    │
│  │         ┌─────────────▼────────────┐                             │    │
│  │         │   Data Extraction &      │                             │    │
│  │         │  Schema.org Transform    │                             │    │
│  │         └─────────────┬────────────┘                             │    │
│  │                       │                                           │    │
│  └───────────────────────┼───────────────────────────────────────────┘    │
│                          │                                                 │
│                          ▼                                                 │
│         ┌────────────────────────────────────┐                            │
│         │     Vector Store (Azure AI Search)  │                            │
│         │  - Sync job embeddings              │                            │
│         │  - Configuration data               │                            │
│         │  - Error logs                       │                            │
│         │  - Performance metrics              │                            │
│         └────────────────┬───────────────────┘                            │
│                          │                                                 │
│         ┌────────────────▼───────────────┐                                │
│         │      NLWeb Handler             │                                │
│         │  ┌──────────────────────────┐  │                                │
│         │  │  Pre-retrieval Analysis  │  │                                │
│         │  │  - Relevance check       │  │                                │
│         │  │  - Decontextualization   │  │                                │
│         │  │  - Query understanding   │  │                                │
│         │  └──────────┬───────────────┘  │                                │
│         │             │                   │                                │
│         │  ┌──────────▼───────────────┐  │                                │
│         │  │   Tool Selection         │  │                                │
│         │  │  - sync_status_search    │  │                                │
│         │  │  - error_analysis        │  │                                │
│         │  │  - performance_metrics   │  │                                │
│         │  │  - data_lineage          │  │                                │
│         │  └──────────┬───────────────┘  │                                │
│         │             │                   │                                │
│         │  ┌──────────▼───────────────┐  │                                │
│         │  │  Vector Retrieval        │  │                                │
│         │  │  - Semantic search       │  │                                │
│         │  │  - Hybrid search         │  │                                │
│         │  │  - Metadata filtering    │  │                                │
│         │  └──────────┬───────────────┘  │                                │
│         │             │                   │                                │
│         │  ┌──────────▼───────────────┐  │                                │
│         │  │  LLM Ranking             │  │                                │
│         │  │  - Relevance scoring     │  │                                │
│         │  │  - Snippet generation    │  │                                │
│         │  │  - Result formatting     │  │                                │
│         │  └──────────┬───────────────┘  │                                │
│         │             │                   │                                │
│         │  ┌──────────▼───────────────┐  │                                │
│         │  │  Response Generation     │  │                                │
│         │  │  - List mode             │  │                                │
│         │  │  - Summarize mode        │  │                                │
│         │  │  - Generate mode         │  │                                │
│         │  └──────────────────────────┘  │                                │
│         └────────────────┬───────────────┘                                │
│                          │                                                 │
└──────────────────────────┼─────────────────────────────────────────────────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │   Web    │    │  Mobile  │    │   API    │
    │  Client  │    │   App    │    │ Clients  │
    └──────────┘    └──────────┘    └──────────┘
```

### Query Processing Flow

```
User Query: "Show me failed syncs from yesterday"
     │
     ├─────────────────────────────────────────────────┐
     │                                                 │
     ▼                                                 ▼
┌─────────────────┐                         ┌──────────────────┐
│  Fast Track     │                         │  Full Analysis   │
│  (Parallel)     │                         │  (Parallel)      │
└─────────────────┘                         └──────────────────┘
     │                                                 │
     │                                      ┌──────────┴──────────┐
     │                                      │                     │
     │                                      ▼                     ▼
     │                              Relevance Check      Decontextualize
     │                                      │                     │
     │                                      ▼                     ▼
     │                              Required Info         Memory Check
     │                                      │                     │
     │                                      └──────────┬──────────┘
     │                                                 │
     │◀────────────────────────────────────────────────┘
     │
     ▼
┌────────────────────────────────────────┐
│         Tool Selection (LLM)           │
│  → Selected: sync_status_search        │
│  → Parameters:                         │
│      - status: "failed"                │
│      - time_range: "yesterday"         │
└────────────────┬───────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────┐
│      Vector Database Query             │
│  → Semantic search for failed syncs    │
│  → Filter: timestamp = yesterday       │
│  → Top 20 candidates                   │
└────────────────┬───────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────┐
│         LLM Ranking (GPT-4o)           │
│  → Score each candidate 0-100          │
│  → Generate snippet for each           │
│  → Filter: score > 70                  │
│  → Top 10 results                      │
└────────────────┬───────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────┐
│      Format Response (JSON)            │
│  {                                     │
│    "results": [                        │
│      {                                 │
│        "name": "Customer DB Sync",     │
│        "status": "failed",             │
│        "error": "Connection timeout",  │
│        "score": 95,                    │
│        "schema_object": {...}          │
│      },                                │
│      ...                               │
│    ]                                   │
│  }                                     │
└────────────────┬───────────────────────┘
                 │
                 ▼
        Stream to User Interface
```

### Authentication & Authorization Flow

```
┌─────────────┐
│   User      │
└──────┬──────┘
       │
       │ JWT Token from TableSyncAI
       │
       ▼
┌────────────────────────────────────┐
│  NLWeb API Gateway                 │
│  ┌──────────────────────────────┐  │
│  │  Auth Middleware             │  │
│  │  1. Validate JWT signature   │  │
│  │  2. Extract user context     │  │
│  │  3. Check permissions        │  │
│  └──────────┬───────────────────┘  │
│             │                       │
│             ▼                       │
│  ┌──────────────────────────────┐  │
│  │  Request Context             │  │
│  │  - user_id                   │  │
│  │  - organization_id           │  │
│  │  - permissions[]             │  │
│  └──────────┬───────────────────┘  │
└─────────────┼───────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│  NLWeb Handler                      │
│  Apply row-level security:          │
│  WHERE organization_id =            │
│        user.organization_id         │
└─────────────┬───────────────────────┘
              │
              ▼
        Filtered Results
```

## Phase 2: Voice Input Integration

### Voice Pipeline Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                      Voice Interface Layer                        │
│                                                                   │
│  ┌──────────────┐                                                │
│  │  User Device │                                                │
│  │  🎤 Microphone│                                                │
│  └──────┬───────┘                                                │
│         │                                                         │
│         │ Audio Stream (16kHz PCM)                               │
│         │                                                         │
│         ▼                                                         │
│  ┌────────────────────────────────────────┐                     │
│  │  Speech-to-Text (Azure Speech)         │                     │
│  │  - Real-time streaming recognition     │                     │
│  │  - Punctuation & formatting            │                     │
│  │  - Custom vocabulary for TableSyncAI   │                     │
│  └────────────────┬───────────────────────┘                     │
│                   │                                              │
│                   │ Transcribed Text                             │
│                   │ "Show me failed syncs from yesterday"        │
│                   │                                              │
└───────────────────┼──────────────────────────────────────────────┘
                    │
                    ▼
┌────────────────────────────────────────────────────────────────┐
│              NLWeb Processing (Phase 1)                         │
│  - Query analysis                                               │
│  - Tool selection                                               │
│  - Vector retrieval                                             │
│  - LLM ranking                                                  │
│  - Response generation                                          │
└────────────────┬───────────────────────────────────────────────┘
                 │
                 │ Text Response
                 │ "I found 3 failed syncs from yesterday..."
                 │
                 ▼
┌────────────────────────────────────────────────────────────────┐
│  ┌────────────────────────────────────────┐                    │
│  │  Voice-Optimized Prompt Processing     │                    │
│  │  - Shorten response (< 200 chars)      │                    │
│  │  - Remove technical jargon             │                    │
│  │  - Add conversational elements         │                    │
│  │  - Include follow-up question          │                    │
│  └────────────────┬───────────────────────┘                    │
│                   │                                             │
│                   │ Optimized Text                              │
│                   │                                             │
│                   ▼                                             │
│  ┌────────────────────────────────────────┐                    │
│  │  Text-to-Speech (Azure Neural TTS)     │                    │
│  │  - Voice: en-US-JennyNeural            │                    │
│  │  - Prosody: Medium rate, natural pitch │                    │
│  │  - Output: MP3 stream                  │                    │
│  └────────────────┬───────────────────────┘                    │
│                   │                                             │
└───────────────────┼─────────────────────────────────────────────┘
                    │
                    │ Audio Stream (MP3)
                    │
                    ▼
             ┌──────────────┐
             │  User Device │
             │  🔊 Speaker   │
             └──────────────┘
```

### Voice User Experience States

```
┌─────────────────────────────────────────────────────────────┐
│                    Voice Interaction States                 │
│                                                             │
│  ┌──────────┐  User presses    ┌─────────────┐           │
│  │  Idle    │  voice button     │  Listening  │           │
│  │  State   ├──────────────────▶│   State     │           │
│  │          │                   │  🎤 Active   │           │
│  └────▲─────┘                   └──────┬──────┘           │
│       │                                 │                   │
│       │                                 │ Speech detected   │
│       │                                 │                   │
│       │                         ┌───────▼──────┐           │
│       │                         │  Processing  │           │
│       │                         │    State     │           │
│       │                         │  ⏳ Thinking │           │
│       │                         └───────┬──────┘           │
│       │                                 │                   │
│       │                                 │ Response ready    │
│       │                                 │                   │
│       │                         ┌───────▼──────┐           │
│       │                         │  Speaking    │           │
│       │                         │    State     │           │
│       │                         │  🔊 Audio    │           │
│       │                         └───────┬──────┘           │
│       │                                 │                   │
│       └─────────────────────────────────┘                   │
│                Complete                                     │
│                                                             │
│  Visual Feedback:                                          │
│  - Listening: Animated waveform                            │
│  - Processing: Spinner/pulse                               │
│  - Speaking: Animated sound bars                           │
│  - Error: Red alert icon                                   │
│                                                             │
│  Audio Feedback:                                           │
│  - Start listening: "Beep" ♪                               │
│  - Stop listening: "Beep-down" ♫                           │
│  - Error: "Error tone" ♭                                   │
└─────────────────────────────────────────────────────────────┘
```

### Multi-Platform Voice Support

```
┌─────────────────────────────────────────────────────────────────┐
│                    Platform-Specific Integration                 │
│                                                                  │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐│
│  │   Web Browser   │  │   iOS Device    │  │  Android Device ││
│  │                 │  │                 │  │                 ││
│  │  Web Speech API │  │  AVFoundation   │  │  SpeechRecognizer││
│  │  ↓              │  │  ↓              │  │  ↓              ││
│  │  Browser STT    │  │  iOS STT        │  │  Android STT    ││
│  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘│
│           │                    │                     │          │
│           └────────────────────┼─────────────────────┘          │
│                                │                                │
│                    ┌───────────▼───────────┐                   │
│                    │  TableSyncAI NLWeb    │                   │
│                    │  Backend API          │                   │
│                    │  /api/nlweb/ask       │                   │
│                    └───────────┬───────────┘                   │
│                                │                                │
│           ┌────────────────────┼─────────────────────┐          │
│           │                    │                     │          │
│  ┌────────▼────────┐  ┌────────▼────────┐  ┌────────▼────────┐│
│  │  Browser TTS    │  │   iOS TTS       │  │  Android TTS    ││
│  │  ↓              │  │   ↓             │  │   ↓             ││
│  │  speechSynthesis│  │   AVSpeech      │  │   TextToSpeech  ││
│  │                 │  │   Synthesizer   │  │                 ││
│  └─────────────────┘  └─────────────────┘  └─────────────────┘│
│                                                                  │
│  Features by Platform:                                          │
│  ✅ Web: Instant, no app needed, limited voice options          │
│  ✅ iOS: Native UX, offline capable, best quality               │
│  ✅ Android: Native UX, Google voices, wide device support      │
└──────────────────────────────────────────────────────────────────┘
```

## Data Flow Diagram

```
┌───────────────────────────────────────────────────────────────────┐
│                   TableSyncAI Data Sources                         │
│                                                                    │
│  ┌──────────────┐  ┌───────────────┐  ┌─────────────────────┐   │
│  │  Sync Jobs   │  │ Configuration │  │  Error Logs         │   │
│  │  Database    │  │   Tables      │  │  & Diagnostics      │   │
│  └──────┬───────┘  └───────┬───────┘  └─────────┬───────────┘   │
│         │                  │                     │                │
│         └──────────────────┼─────────────────────┘                │
│                            │                                      │
└────────────────────────────┼──────────────────────────────────────┘
                             │
                             ▼
                ┌────────────────────────────┐
                │   Data Transformation      │
                │   to Schema.org Format     │
                │                            │
                │   Python ETL Pipeline      │
                └────────────┬───────────────┘
                             │
                             ▼
                ┌────────────────────────────┐
                │  Embedding Generation      │
                │  (text-embedding-3-large)  │
                │                            │
                │  Batch: 100 items          │
                │  Output: 3072-dim vectors  │
                └────────────┬───────────────┘
                             │
                             ▼
┌────────────────────────────────────────────────────────────────────┐
│              Vector Store (Azure AI Search)                         │
│                                                                     │
│  Collections:                                                       │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │  tablesyncai-index                                           │ │
│  │  - Document: SyncJob objects                                 │ │
│  │  - Vectors: Semantic embeddings (3072-dim)                   │ │
│  │  - Metadata: status, timestamps, org_id, user_id            │ │
│  │  - Hybrid Search: Vector + keyword                          │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                     │
│  Indexes:                                                           │
│  - Semantic configuration: "tablesyncai-semantic-config"           │
│  - Fields: name, description, error_message, status               │
│  - Filters: organization_id, user_id, timestamp, status           │
└─────────────────────────────────────────────────────────────────────┘
                             │
                             │ Query Time
                             ▼
                ┌────────────────────────────┐
                │   Semantic Search          │
                │   + Metadata Filtering     │
                │                            │
                │   Top K: 20 candidates     │
                └────────────┬───────────────┘
                             │
                             ▼
                ┌────────────────────────────┐
                │   LLM Ranking & Scoring    │
                │   (GPT-4o)                 │
                │                            │
                │   Top N: 10 results        │
                └────────────┬───────────────┘
                             │
                             ▼
                     Response to User
```

## Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      Azure Cloud Environment                     │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  Azure App Service (TableSyncAI + NLWeb)                   │ │
│  │  - Python 3.12                                             │ │
│  │  - Autoscaling: 2-10 instances                             │ │
│  │  - Health checks enabled                                   │ │
│  └────────────────┬───────────────────────────────────────────┘ │
│                   │                                              │
│  ┌────────────────┼───────────────────────────────────────────┐ │
│  │                │                                           │ │
│  ▼                ▼                                           ▼ │
│  ┌──────────┐  ┌──────────┐  ┌────────────────────────────┐   │
│  │ Azure    │  │ Azure    │  │ Azure OpenAI Service       │   │
│  │ AI Search│  │ Speech   │  │ - GPT-4o deployment        │   │
│  │ Service  │  │ Service  │  │ - text-embedding-3-large   │   │
│  │ (S1)     │  │          │  │ - Rate limiting: 100K TPM  │   │
│  └──────────┘  └──────────┘  └────────────────────────────┘   │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  Azure Monitor                                             │ │
│  │  - Application Insights                                    │ │
│  │  - Log Analytics                                           │ │
│  │  - Alerts & Dashboards                                     │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │  Azure Key Vault                                           │ │
│  │  - API keys                                                │ │
│  │  - Connection strings                                      │ │
│  │  - JWT secrets                                             │ │
│  └────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────┘
```

## Notes

- All diagrams use ASCII art for maximum compatibility
- Phase 1 focuses on text interface only
- Phase 2 adds voice capabilities on top of Phase 1
- Architecture is designed for scalability and maintainability
- Security and compliance are built-in at every layer
