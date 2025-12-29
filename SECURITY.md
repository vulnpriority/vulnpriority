# Security Policy

## Our Commitment

VulnPriority is built by security professionals, for security professionals. We take the security and privacy of our users seriously.

---

## Data Handling

### What We Collect

**From Users:**
- Account information (email, name) for authentication
- Uploaded vulnerability scan data (processed locally, not shared)

**From External Sources (Public Data Only):**
- CVE information from NIST NVD
- CISA Known Exploited Vulnerabilities
- EPSS scores from FIRST.org
- Public exploit references
- Public Reddit posts from security subreddits

### What We Do NOT Collect

- ❌ Personal browsing data
- ❌ System information beyond uploaded scans
- ❌ Private Reddit messages or user data
- ❌ Any data that isn't explicitly provided by users

---

## Third-Party Integrations

### Reddit API Usage

We access Reddit's Data API to monitor **public posts** in security-focused subreddits:
- r/netsec
- r/cybersecurity
- r/blueteam
- r/redteam
- r/malware
- r/AskNetsec

**Our Reddit integration:**
- ✅ Read-only access (no posting, voting, or commenting)
- ✅ Only fetches public post titles and metadata
- ✅ Does not collect Reddit user personal information
- ✅ Caches results to minimize API calls
- ✅ Complies with Reddit's Developer Terms and Data API Terms
- ✅ Links back to original Reddit posts with attribution

### Other Integrations

All external data sources we query are:
- Publicly available APIs
- Used in read-only mode
- Cached responsibly to reduce load
- Properly attributed

---

## Data Storage

### Self-Hosted Deployments
- All data remains on your infrastructure
- No telemetry or data sent to external servers
- You control data retention and deletion

### Security Measures
- Passwords hashed using bcrypt
- JWT tokens for session management
- Optional two-factor authentication (TOTP)
- Role-based access control
- Input validation and sanitization

---

## Vulnerability Disclosure

### Reporting Security Issues

If you discover a security vulnerability in VulnPriority, please report it responsibly:

1. **Email:** [your-security-email]
2. **Subject:** "VulnPriority Security Vulnerability"
3. **Include:**
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Any suggested fixes

### Response Timeline

- **Acknowledgment:** Within 48 hours
- **Initial Assessment:** Within 7 days
- **Fix Timeline:** Depends on severity
  - Critical: 24-72 hours
  - High: 7 days
  - Medium: 30 days
  - Low: 90 days

### What to Expect

- We will not take legal action against good-faith security researchers
- We will credit you in our security advisories (unless you prefer anonymity)
- We will keep you informed of our progress

---

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 2.x.x   | :white_check_mark: |
| 1.x.x   | :x:                |
| < 1.0   | :x:                |

---

## Security Best Practices for Users

### Deployment Recommendations

1. **Use HTTPS** - Always deploy behind TLS
2. **Firewall Rules** - Restrict access to trusted networks
3. **Regular Updates** - Keep the platform updated
4. **Strong Passwords** - Enforce password policies
5. **Enable 2FA** - Use two-factor authentication
6. **Audit Logs** - Monitor access logs regularly

### API Key Management

If you integrate external services:
- Store API keys in environment variables
- Never commit keys to version control
- Rotate keys periodically
- Use minimum required permissions

---

## Compliance

VulnPriority is designed to support compliance with:
- **GDPR** - Data minimization, user consent
- **SOC 2** - Security controls
- **ISO 27001** - Information security management

*Note: Compliance depends on your specific deployment and configuration.*

---

## Contact

For security-related inquiries:
- **Email:** [your-security-email]
- **PGP Key:** Available upon request

---

*Last updated: December 2025*
