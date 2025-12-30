# VulnSight

**Risk-Based Vulnerability Prioritization Platform**

A free, open-source threat intelligence platform that helps security teams prioritize vulnerabilities based on real-world exploitability rather than just CVSS scores.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Status](https://img.shields.io/badge/status-active%20development-green.svg)
![Platform](https://img.shields.io/badge/platform-web-lightgrey.svg)

---

## 🎯 The Problem

Traditional vulnerability management tools rank threats by CVSS scores, resulting in:
- **70-90% of vulnerabilities marked as "critical"**
- Security teams overwhelmed with alerts
- No context on what's actually being exploited in the wild

## 💡 Our Solution

VulnPriority aggregates **real-world threat intelligence** to answer the question: *"Which vulnerabilities should I fix first?"*

We prioritize based on:
- ✅ **Active exploitation** (CISA KEV)
- ✅ **Exploit availability** (ExploitDB, Metasploit, Nuclei)
- ✅ **Prediction scores** (EPSS)
- ✅ **Community discussions** (Reddit security communities)
- ✅ **Threat actor associations** (MITRE ATT&CK)
- ✅ **Security news mentions** (RSS feeds)

---

## 🔍 Features

### Core Capabilities
- **Upload vulnerability scans** (Nessus, OpenVAS, CSV)
- **Automatic enrichment** with 10+ threat intelligence sources
- **Risk-based scoring** that surfaces the critical 1-5%
- **Trending CVEs dashboard** showing what's hot in the security community
- **CVE timeline tracking** from disclosure to exploitation

### Threat Intelligence Sources
| Source | Data Provided |
|--------|---------------|
| CISA KEV | Known exploited vulnerabilities |
| EPSS | Exploit prediction scoring |
| NIST NVD | CVE details and CVSS scores |
| MITRE ATT&CK | Threat actor TTPs |
| ExploitDB | Public exploits |
| Metasploit | Weaponized exploits |
| Nuclei Templates | Scanner detection templates |
| GitHub | Proof-of-concept code |
| AlienVault OTX | Threat pulses and IOCs |
| Security RSS | News from 12+ sources |
| Reddit | Community discussions (r/netsec, r/cybersecurity) |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Frontend (React)                        │
│                   Dashboard / Threat Intel                   │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│                    Backend API (Python)                      │
│              FastAPI / Authentication / REST                 │
└─────────────────────────┬───────────────────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│   Scanner   │   │   Threat    │   │  Database   │
│   Parsers   │   │   Intel     │   │ PostgreSQL  │
│             │   │  Enrichment │   │             │
└─────────────┘   └─────────────┘   └─────────────┘
```

### Tech Stack
- **Frontend:** React, Tailwind CSS
- **Backend:** Python, FastAPI
- **Database:** PostgreSQL
- **Cache:** Redis
- **Deployment:** Docker

---

## 📊 How It Works

1. **Upload** your vulnerability scan results
2. **Enrich** - We automatically query 10+ threat intel sources
3. **Prioritize** - Our algorithm ranks by real-world risk
4. **Act** - Focus on what matters, ignore the noise

### Risk Scoring Algorithm

```
Risk Score = Base CVSS 
           + KEV Bonus (+50 if actively exploited)
           + Exploit Bonus (+30 if public exploit exists)
           + EPSS Factor (scaled 0-20)
           + Trending Bonus (+10 if in security news)
           × Asset Criticality Multiplier
```

---

## 🔒 Security & Privacy

- **No user data collection** - We don't track users
- **No vulnerability data sharing** - Your scans stay private
- **Self-hosted option** - Run entirely on your infrastructure
- **Read-only integrations** - We only fetch public threat data

See [SECURITY.md](SECURITY.md) for our full security policy.

---

## 🎯 Use Cases

### For Security Teams
- Prioritize patch management based on real risk
- Track trending threats affecting your stack
- Get early warning on emerging vulnerabilities

### For MSSPs
- Provide clients with actionable vulnerability reports
- Demonstrate risk reduction over time
- Focus remediation efforts efficiently

### For Researchers
- Monitor CVE discussions across security communities
- Track exploit development timelines
- Analyze threat actor targeting patterns

---

## 📈 Roadmap

- [x] Core vulnerability prioritization engine
- [x] CISA KEV integration
- [x] EPSS scoring
- [x] Exploit database queries
- [x] MITRE ATT&CK mapping
- [x] Security news RSS feeds
- [x] User authentication with 2FA
- [ ] Reddit community monitoring
- [ ] Email alerts for critical CVEs
- [ ] Jira/ServiceNow integration
- [ ] PDF report generation
- [ ] API for external integrations

---

## 🤝 Contributing

We welcome contributions! Please see our contributing guidelines (coming soon).

Areas where we need help:
- Additional scanner parsers
- New threat intel integrations
- UI/UX improvements
- Documentation

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 📧 Contact

- **Project Lead:** [Your Name]
- **Email:** [your-email]
- **Twitter:** [your-handle] (optional)

---

## ⭐ Acknowledgments

This project leverages data from:
- [CISA](https://www.cisa.gov/) - Known Exploited Vulnerabilities
- [FIRST.org](https://www.first.org/epss/) - EPSS Scores
- [NIST NVD](https://nvd.nist.gov/) - National Vulnerability Database
- [MITRE ATT&CK](https://attack.mitre.org/) - Threat Intelligence
- [Reddit](https://www.reddit.com/) - Security Community Discussions

---

*Built with ❤️ for the cybersecurity community*
