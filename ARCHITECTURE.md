# Architecture Overview

This document provides a high-level overview of VulnPriority's architecture.

---

## System Overview

```
                                    ┌─────────────────────────────────────┐
                                    │           External APIs             │
                                    │  ┌─────┐ ┌─────┐ ┌─────┐ ┌───────┐ │
                                    │  │CISA │ │EPSS │ │ NVD │ │Reddit │ │
                                    │  │ KEV │ │     │ │     │ │       │ │
                                    │  └──┬──┘ └──┬──┘ └──┬──┘ └───┬───┘ │
                                    └─────┼──────┼──────┼───────┼───────┘
                                          │      │      │       │
                                          ▼      ▼      ▼       ▼
┌──────────────┐                  ┌─────────────────────────────────────┐
│              │                  │      Threat Intelligence Service     │
│    Users     │                  │  ┌─────────────────────────────────┐ │
│              │                  │  │         Cache Layer (Redis)     │ │
└──────┬───────┘                  │  └─────────────────────────────────┘ │
       │                          └──────────────────┬──────────────────┘
       │ HTTPS                                       │
       ▼                                             │
┌─────────────────────────────────────────────────────────────────────────┐
│                              Frontend                                    │
│  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐ │
│  │ Dashboard │ │  Threat   │ │   Vuln    │ │  Assets   │ │ Settings  │ │
│  │           │ │   Intel   │ │   List    │ │           │ │           │ │
│  └───────────┘ └───────────┘ └───────────┘ └───────────┘ └───────────┘ │
│                              React + Tailwind                           │
└──────────────────────────────────┬──────────────────────────────────────┘
                                   │ REST API
                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                              Backend API                                 │
│  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐ │
│  │   Auth    │ │  Scans    │ │   Intel   │ │  Assets   │ │ Dashboard │ │
│  │  Routes   │ │  Routes   │ │  Routes   │ │  Routes   │ │  Routes   │ │
│  └───────────┘ └───────────┘ └───────────┘ └───────────┘ └───────────┘ │
│                           FastAPI + Python                              │
└──────────────────────────────────┬──────────────────────────────────────┘
                                   │
                    ┌──────────────┼──────────────┐
                    ▼              ▼              ▼
             ┌───────────┐  ┌───────────┐  ┌───────────┐
             │  Scanner  │  │Enrichment │  │Prioritize │
             │  Parsers  │  │  Engine   │  │  Engine   │
             └───────────┘  └───────────┘  └───────────┘
                    │              │              │
                    └──────────────┼──────────────┘
                                   ▼
                          ┌───────────────┐
                          │  PostgreSQL   │
                          │   Database    │
                          └───────────────┘
```

---

## Component Details

### Frontend (React)

| Component | Purpose |
|-----------|---------|
| Dashboard | Overview of risk posture, trending CVEs, stats |
| Threat Intel | Live threat intelligence feed, CVE lookup |
| Vulnerabilities | List and filter discovered vulnerabilities |
| Assets | Manage scanned assets and criticality |
| Upload | Upload and process vulnerability scans |
| Settings | User profile, 2FA, preferences |

### Backend API (FastAPI)

| Module | Endpoints |
|--------|-----------|
| Auth | `/auth/login`, `/auth/register`, `/auth/2fa/*` |
| Scans | `/scans/upload`, `/scans/{id}` |
| Intel | `/intel/trending`, `/intel/cve/{id}`, `/intel/kev` |
| Assets | `/assets/`, `/assets/top-risk` |
| Dashboard | `/dashboard/stats`, `/dashboard/news` |

### Threat Intelligence Service

Responsible for fetching and caching external threat data:

```
┌─────────────────────────────────────────────────────────────┐
│                 Threat Intel Service                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │  KEV Client  │    │ EPSS Client  │    │  NVD Client  │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
│                                                              │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │ExploitDB     │    │  Metasploit  │    │   Nuclei     │  │
│  │Client        │    │   Client     │    │   Client     │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
│                                                              │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │ GitHub POC   │    │    Reddit    │    │  RSS Feeds   │  │
│  │   Client     │    │   Client     │    │   Client     │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              Unified Cache Layer (Redis)             │   │
│  │         TTL-based caching per source type            │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Scanner Parsers

| Format | Parser |
|--------|--------|
| Nessus (.nessus) | XML parser extracting CVEs, hosts, severity |
| OpenVAS | XML parser for OpenVAS reports |
| CSV | Generic CSV with CVE column detection |

### Prioritization Engine

```python
# Simplified scoring logic (conceptual)

def calculate_risk_score(cve, asset):
    score = cve.cvss_score * 10  # Base: 0-100
    
    if cve.in_cisa_kev:
        score += 50  # Actively exploited
    
    if cve.has_public_exploit:
        score += 30  # Exploit available
    
    if cve.epss_score > 0.5:
        score += 20  # High exploitation probability
    
    if cve.trending_in_news:
        score += 10  # Community attention
    
    score *= asset.criticality_multiplier  # 1.0 - 2.0
    
    return min(score, 100)
```

---

## Data Flow

### 1. Scan Upload Flow

```
User uploads scan file
        │
        ▼
┌─────────────────┐
│ Detect file type│
│ (Nessus/OpenVAS)│
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Parse & Extract│
│  CVEs, Hosts    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Store raw data  │
│ in PostgreSQL   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Trigger async   │
│ enrichment job  │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Query all intel │
│ sources for CVEs│
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Calculate risk  │
│ scores & rank   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Update database │
│ notify frontend │
└─────────────────┘
```

### 2. Threat Intel Refresh Flow

```
Scheduled job (every 6 hours)
        │
        ▼
┌─────────────────┐
│ Fetch CISA KEV  │──────► Cache (24hr TTL)
└─────────────────┘
        │
        ▼
┌─────────────────┐
│ Fetch EPSS bulk │──────► Cache (24hr TTL)
└─────────────────┘
        │
        ▼
┌─────────────────┐
│ Fetch RSS feeds │──────► Cache (1hr TTL)
└─────────────────┘
        │
        ▼
┌─────────────────┐
│ Fetch Reddit    │──────► Cache (30min TTL)
│ security posts  │
└─────────────────┘
        │
        ▼
┌─────────────────┐
│ Re-score any    │
│ affected CVEs   │
└─────────────────┘
```

---

## Caching Strategy

| Data Source | Cache TTL | Reason |
|-------------|-----------|--------|
| CISA KEV | 24 hours | Updates ~weekly |
| EPSS Scores | 24 hours | Updates daily |
| NVD CVE Data | 6 hours | Updates frequently |
| Exploit DBs | 6 hours | New exploits published |
| Reddit Posts | 30 minutes | Real-time discussions |
| RSS Feeds | 1 hour | News updates |

---

## Security Considerations

### Authentication
- JWT tokens with short expiry (15 min)
- Refresh tokens with longer expiry (7 days)
- Optional TOTP-based 2FA
- Password hashing with bcrypt

### API Security
- Rate limiting per user/IP
- Input validation on all endpoints
- SQL injection prevention (parameterized queries)
- XSS prevention (output encoding)

### External API Calls
- All outbound calls use HTTPS
- API keys stored in environment variables
- Timeout limits on all requests
- Circuit breaker for failing services

---

## Deployment

### Docker Compose (Development)

```yaml
services:
  frontend:
    build: ./frontend
    ports: ["3000:3000"]
    
  backend:
    build: ./backend
    ports: ["8000:8000"]
    environment:
      - DATABASE_URL=postgresql://...
      - REDIS_URL=redis://...
      
  postgres:
    image: postgres:15
    
  redis:
    image: redis:7
```

### Production Considerations

- Use managed database (RDS, Cloud SQL)
- Deploy behind load balancer with TLS
- Enable horizontal scaling for backend
- Use CDN for frontend assets
- Set up monitoring and alerting

---

## Future Architecture

Planned improvements:
- Message queue for async processing (RabbitMQ/Redis Streams)
- Webhook support for real-time alerts
- GraphQL API option
- Multi-tenant support with data isolation

---

*Last updated: December 2025*
