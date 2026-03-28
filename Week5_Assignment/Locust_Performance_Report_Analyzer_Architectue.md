# Locust Performance Report Analyzer Agent - Architecture (Revised)

## 1. Overview
AI-powered, advisory-only system integrated with CI/CD to analyze Locust reports, correlate with Splunk logs, detect trends, and generate recommendations.

- Deployment: Amazon EKS
- Architecture: Modular Monolith (FastAPI)
- Vector DB: OpenSearch
- Metadata DB: MongoDB
- Object Storage: S3
- Trigger: Spinnaker
- LLM: Managed (hybrid with self-hosted embeddings)

---

## 2. High-Level Architecture

Flow:
1. Spinnaker triggers analysis after Locust execution
2. FastAPI API receives request
3. Reports stored in S3 + metadata in MongoDB
4. Preprocessing extracts structured metrics
5. Embeddings generated → OpenSearch
6. Agent (LangGraph) orchestrates analysis
7. Splunk queried (real-time + historical)
8. Trend engine compares historical runs
9. LLM generates RCA + recommendations
10. Reports generated (Markdown, HTML, PDF)
11. Output exposed via API, UI, CI artifacts

---

## 3. API-First Approach

All capabilities exposed via REST APIs.

Core APIs:
- POST /analyze
- GET /report/{run_id}
- GET /trend/{service}
- GET /logs/correlation

Supports:
- CI/CD integration
- UI consumption
- CLI usage

---

## 4. Application Architecture (Modular Monolith)

app/
 ├── api/
 ├── ingestion/
 ├── preprocessing/
 ├── analysis/
 ├── trends/
 ├── embeddings/
 ├── splunk/
 ├── advisory/
 ├── reporting/
 ├── storage/
 └── ui/

---

## 5. Database Layer

### MongoDB (Metadata)
Stores:
- test_runs
- endpoint_metrics
- SLA definitions
- analysis_results

### OpenSearch (Vector DB)
Stores embeddings for:
- Locust reports
- NFR data
- Advisory outputs
- Historical trends

### S3
Stores:
- Raw Locust reports
- Generated reports

---

## 6. Core Components

### Ingestion Service
- Accepts CSV/HTML reports and NFR files
- Stores data in S3 and MongoDB

### Preprocessing
- Parses CSV/HTML
- Extracts metrics (P95, P99, TPS, errors)

### Analysis Engine (Deterministic)
- SLA validation
- Error rate checks
- Throughput validation

### Trend Engine
- Compare last run, release, last 5 runs
- Detect regressions and anomalies

### Vector Service
- Generate embeddings
- Store and retrieve similar runs

### Splunk Service
- Fetch logs via time window
- Detect errors and latency sources

### Advisory Service (LLM)
- Generate summaries
- Root cause analysis
- Recommendations

### Reporting Engine
Generates:
- Markdown
- HTML
- PDF
- CSV

---

## 7. Web Layer

Features:
- Interactive dashboards
- Trend charts
- Drill-down per endpoint
- Log correlation overlays

Suggested Stack:
- React + ECharts

---

## 8. Data Flow

1. CI/CD triggers Locust
2. Reports generated
3. Analyzer ingests reports
4. Metrics stored
5. Analysis executed
6. Splunk logs fetched
7. LLM generates insights
8. Reports generated
9. Results published

---

## 9. Implementation Phases Alignment

Phase 1: Core ingestion + parsing + SLA checks  
Phase 2: Trend analysis  
Phase 3: Vector embeddings  
Phase 4: Splunk integration  
Phase 5: LLM advisory  
Phase 6: Multi-format reports  
Phase 7: CI/CD integration  
Phase 8: Production deployment (EKS)

---

## 10. Non-Functional Design

### Scalability
- Kubernetes auto-scaling
- Stateless FastAPI pods

### Performance
- Async processing
- Batch embeddings

### Observability
- Logs, metrics, tracing

### Security
- IAM roles
- Secure secrets
- Encrypted storage

---

## 11. Testing Strategy

- Unit tests (parsers, analysis)
- Integration tests (pipeline)
- Performance tests (large reports)

---

## 12. Simple Design Principles

- Modular monolith (no microservices)
- Clear separation of concerns
- Minimal complexity
- Scalable via Kubernetes

---

## 13. Future Enhancements

- Correlation ID support
- Predictive analytics
- Automated alerts
- RBAC/SSO

---

## 14. Summary

This architecture delivers:
- AI-driven performance insights
- Strong trend intelligence
- Deep log correlation
- Simple, scalable design
