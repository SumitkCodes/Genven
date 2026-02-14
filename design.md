# Design Document: GENVEN Legal Awareness Platform

## Overview

GENVEN uses Retrieval-Augmented Generation (RAG) architecture to provide neutral legal information. Five components: Frontend (React), Backend API (FastAPI), AI Engine (RAG + Bias Detection), Legal Knowledge Base (PostgreSQL + pgvector), Admin Module.

## Architecture

```
Frontend (React) → Backend API (FastAPI) → AI Engine (RAG) → Knowledge Base (PostgreSQL)
                                        ↓
                                   Admin Module
```

**Tech Stack:**
- Frontend: React, Tailwind CSS
- Backend: Python FastAPI, JWT auth, Redis cache
- AI: LangChain, GPT-4/Claude, Sentence Transformers, pgvector
- Database: PostgreSQL with pgvector extension
- Infrastructure: Docker, AWS/GCP/Azure, Nginx

## Components

### 1. Frontend
- Query interface with character counter (10-500)
- Results display with expandable sections
- Browse by legal domain
- Admin dashboard for content review
- Prominent disclaimers on all pages

### 2. Backend API

**Key Endpoints:**
```
POST /api/v1/query              - Submit query
GET  /api/v1/acts               - Browse acts
GET  /api/v1/judgments          - Search judgments
POST /api/v1/admin/login        - Admin auth
GET  /api/v1/admin/review-queue - Flagged responses
```

**Security:** TLS 1.3, JWT auth, rate limiting (10 req/min public, 100 req/min admin), input sanitization

### 3. AI Engine

**Query Processor:**
- Validates length (10-500 chars)
- Classifies legal domain (criminal, civil, constitutional, etc.)
- Detects prohibited patterns ("what should I do", "will I win", "draft petition")
- Uses BERT/RoBERTa for classification

**RAG Engine:**
- Converts query to embedding (Sentence Transformers)
- Semantic search retrieves top 3 sections, 2-5 judgments
- Prioritizes Supreme/High Court over lower courts
- Generates explanation with strict neutral prompt
- Validates citations preserved

**System Prompt:**
```
You are a legal information assistant. Explain WHAT the law states, not what someone should do.
Use only provided sources. Include exact citations. Do not predict outcomes or give advice.
Present multiple perspectives. Use neutral, factual language only.
```

**Bias Detection:**
- Scans for advice keywords ("you should", "recommend")
- Sentiment analysis for subjective language
- Flags predictions and one-sided presentations
- Risk levels: High (block), Medium (flag), Low (log)

### 4. Legal Knowledge Base

**Database Schema:**

```sql
-- Acts: id, name, year, legal_domain[], source_url
-- Sections: id, act_id, section_number, text, summary, version, effective_date
-- Judgments: id, case_name, citation, court, court_level, year, summary, full_text_url
-- Section_Embeddings: id, section_id, embedding VECTOR(768)
-- Query_Logs: id, query_hash, legal_domain[], response_status, user_ip_hash (anonymized)
-- Review_Queue: id, response_text, risk_level, risk_flags[], review_status
```

**Versioning:** Sections maintain version history for amendments with effective dates

### 5. Admin Module

- Content management (add/edit Acts, Sections, Judgments)
- Review queue (approve/reject/edit flagged responses)
- Analytics dashboard (query volume, domains, confidence, rejection rate)
- User management (role-based access control)

## Data Flow

**Example: "What is punishment for theft in India?"**

1. **Process:** Domain=Criminal, Intent=Info-seeking, Entities=["theft", "punishment"]
2. **Retrieve:** IPC Sections 378-379, Related judgments (State of Maharashtra v. Natwarlal)
3. **Generate:** Neutral explanation with citations
4. **Validate:** No bias detected, Citations verified, Confidence=High
5. **Display:** Explanation + Sources + Disclaimer

## Correctness Properties (55 Total)

**Query Processing (7):**
- P1: Accept queries 10-500 chars
- P2: Reject advice-seeking queries
- P3: Classify into legal domains
- P4: Prompt clarification for unclassifiable
- P5: Log domain for each query

**Retrieval (8):**
- P6: Retrieve relevant Acts/Sections
- P7: Display Act with name, year, section
- P8: Show official text unmodified
- P9: Retrieve min 3 sections
- P10: Retrieve related judgments
- P11: Display 2-5 judgments
- P12: Include case name, court, year, citation
- P13: Prioritize Supreme/High Court

**Neutrality (4):**
- P14: No advice language
- P15: No subjective phrases
- P47: Use neutral templates
- P46: Multiple perspectives

**Citations (5):**
- P16: Include official citation
- P17: Provide source URL
- P18: Display source type
- P19: Exclude unverified content
- P20: Validate official sources

**Disclaimers (3):**
- P21: Disclaimer on every page
- P22: Recommend lawyer consultation
- P2: Reject prohibited queries

**Search/Browse (4):**
- P24: Organize by domain
- P25: Display all sections for Act
- P26: Filter judgments correctly
- P27: Paginate >20 results

**Access Control (2):**
- P28: Role-based access
- P29: Strong password requirement

**Review (5):**
- P31: Flag risk indicators
- P32: Display in review queue
- P33: Update approval status
- P34: Prevent rejected display
- P35: Preserve legal text during edits

**Content Management (3):**
- P36: Validate required fields
- P37: Version amendments
- P38: Log content changes

**Explainability (3):**
- P39: Show source attribution
- P40: Display confidence indicator
- P41: Order by relevance

**Privacy (3):**
- P42: No PII storage
- P43: Anonymize identifiers
- P44: Log completeness

**Bias Detection (2):**
- P45: Execute bias scanning
- P31: Flag risky content

**Error Handling (4):**
- P48: Clear error messages
- P49: Suggest alternatives
- P50: Examples for vague queries
- P51: User-friendly technical errors

**Performance (1):**
- P52: Optimize payload <100KB

**Accessibility (3):**
- P53: Keyboard navigation
- P54: 4.5:1 contrast ratio
- P55: Text alternatives

**Logging (1):**
- P30: Log admin actions

## Security & Privacy

- TLS 1.3 encryption
- JWT authentication (1-hour expiration)
- Bcrypt password hashing
- Anonymized query logs (IP/session hashed)
- GDPR compliance
- Data deletion within 30 days on request

## Scalability

- Horizontal scaling support
- Redis caching (Acts/Sections: 1hr TTL, Queries: 30min TTL)
- Database connection pooling
- Vector index optimization (IVFFlat)
- API endpoints for NGO/government integration

## Limitations

- No legal advice/predictions/petition drafting
- Indian legal system only (MVP)
- English language only (MVP)
- Requires internet connectivity
- Depends on official source availability
- Student prototype with limited resources
