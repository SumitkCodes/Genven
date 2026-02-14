# Requirements Document: GENVEN Legal Awareness Platform

## Overview

GENVEN is an AI-powered legal awareness platform that explains laws, acts, sections, and court judgments using only verified government sources. It is NOT a legal advice tool - it provides educational information to help citizens, students, and NGOs understand the legal framework without case-specific guidance or predictions.

## Key Terms

- **RAG_Engine**: Retrieval-Augmented Generation system for legal content retrieval and explanation
- **Legal_Knowledge_Base**: Repository of official laws, acts, sections, and court judgments
- **Official_Source**: Government websites, court databases, verified legal repositories
- **Legal_Domain**: Area of law (criminal, civil, constitutional, family, labor, property, consumer)
- **Neutral_Explanation**: Factual description without opinion, prediction, or advice

## Requirements

### 1. Natural Language Query Processing

Users can ask legal questions in plain language (10-500 characters). System identifies legal domain within 3 seconds, requests clarification for ambiguous queries, and rejects advice-seeking queries with appropriate disclaimers.

### 2. Legal Domain Classification

System classifies queries into legal domains (criminal, civil, constitutional, family, labor, property, consumer). Supports multiple domains per query and prompts for clarification when domain cannot be determined.

### 3. Law and Section Retrieval

Retrieves top 3 relevant Acts and Sections from knowledge base. Displays full Act name, year, and section number. Shows complete official text without modification.

### 4. Court Judgment Retrieval

Retrieves 2-5 related court judgments with case name, court, year, and citation. Prioritizes Supreme Court and High Court over lower courts.

### 5. Neutral Explanation Generation

Generates clear explanations without bias, predictions, or advice. Breaks down complex legal language into simple terms. Presents multiple interpretations without favoring one.

### 6. Official Citations and Source Links

Every Act, Section, and Judgment includes official citation and clickable source link. Displays source type (government website, court database). Only verified official sources are displayed.

### 7. Disclaimers and Legal Boundaries

Prominent disclaimer on first access and every response page stating GENVEN does not provide legal advice. Rejects prohibited queries (advice, predictions, petition drafting) with explanation. Recommends consulting qualified lawyers.

### 8. Search and Browse

Search by Act name, Section number, or keywords. Browse Acts organized by legal domain. Filter judgments by court level, year, and domain. Pagination for results exceeding 20 items.

### 9. User Role Management

Three roles: Public User (no auth required), Content Reviewer, System Administrator. Role-based access control for admin functions. Strong password requirements. All admin actions logged.

### 10. Content Review and Moderation

AI responses with risk indicators (advice language, predictions, opinions) flagged for review. Review queue in admin interface. Reviewers can approve, reject, or edit explanations while preserving official legal text.

### 11. Knowledge Base Management

Admin interface to add/update Acts, Sections, Judgments. Requires official source URL, citation, and metadata. Version tracking for Act amendments. Citation validation before saving.

### 12. Response Explainability

Shows which Sections and Judgments were used. Displays confidence indicator (high/medium/low). Lists sources in relevance order. Explains RAG process. Warns on low confidence responses.

### 13. Performance

Response time under 5 seconds for 95% of queries. 99% uptime during business hours. Supports 100+ concurrent users. System remains available during knowledge base updates.

### 14. Security and Privacy

TLS 1.3+ encryption. No PII storage without consent. Anonymized query logs. GDPR compliance. Data deletion within 30 days on request.

### 15. Audit and Logging

Logs all queries, rejected queries, admin actions, and content modifications. Retains logs for 12 months.

### 16. Bias Detection

Scans for bias indicators (subjective language, one-sided presentation, advice terms). Flags biased responses for review. Retrieves multiple judicial perspectives. Uses neutral response templates.

### 17. Scalability

Horizontal scaling support. Maintains performance with 10,000+ documents. Supports new legal domains without redesign. API endpoints for NGO/government integration. Multi-language content storage.

### 18. Error Handling

Clear error messages. Suggests alternatives when no results found. Provides examples for vague queries. User-friendly technical error messages. Contextual help tips.

### 19. Low-Bandwidth Support

Caches frequently accessed content. Serves cached content offline. Optimizes payload for 2G connections. Prioritizes text over images in low-bandwidth mode.

### 20. Accessibility

WCAG 2.1 Level AA compliance. Screen reader support. Keyboard navigation. 4.5:1 contrast ratio for normal text. Text alternatives for all non-text content.

## Technical Requirements

**Accuracy**: Only official sources, verifiable citations, content reviewed before publication

**Performance**: <5s response time (95%), 100+ concurrent users, 99% uptime

**Security**: TLS 1.3+, role-based access, anonymized logs, GDPR compliance

**Data**: Acts (text, year, source URL), Sections (number, text, domain), Judgments (case, citation, court, year, summary, URL)

## User Roles

**Public User**: Query and browse, no authentication required

**Content Reviewer**: Review/approve/reject AI responses, edit explanations, requires authentication

**System Administrator**: Manage knowledge base, user accounts, system configuration, requires elevated authentication

## Constraints

**Legal**: No advice/predictions/petition drafting, only official sources, prominent disclaimers

**Ethical**: Strict neutrality, bias detection, multiple perspectives, user privacy

**Technical**: Indian legal system (MVP), English only (MVP), browser-based, internet required

**Resource**: Student prototype, cost-effective infrastructure, open-source tools preferred

## Assumptions

- Government/court websites provide accessible content
- Users have basic internet access and digital literacy
- MVP covers core domains (criminal, civil, constitutional)
- Focus on Supreme Court and High Court judgments initially
- Periodic content updates (monthly/quarterly)
