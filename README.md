# PhishGuard — Phishing Intelligence Platform

> A browser-based SOC analyst toolkit for deep phishing log analysis, threat intelligence, and professional client reporting.

![Version](https://img.shields.io/badge/version-2.0-blue) ![License](https://img.shields.io/badge/license-MIT-green) ![No Backend](https://img.shields.io/badge/backend-none-lightgrey) ![Zero Dependencies](https://img.shields.io/badge/external%20CDN-none-brightgreen)

---

## Overview

PhishGuard is a single-file HTML tool built for SOC analysts to investigate phishing emails quickly and thoroughly. Paste any JSON security log — from Mimecast, Microsoft Defender, or any SIEM — and PhishGuard automatically extracts every field, runs six independent threat analysis modules, and generates a downloadable client-ready PDF report.

Everything runs entirely in the browser. No data is sent to any server. No accounts required.

---

## Features

### Log Ingestion
- Accepts any JSON security alert format (Mimecast, Microsoft 365 Defender, custom SIEM)
- Bulletproof parser handles malformed JSON, smart/curly quotes, and unescaped inner quotes in field values
- Extracts 20+ fields automatically: source IPs, URLs, subject, sender, recipients, SPF/DKIM/DMARC, action taken, delivery location, threats detected, timestamps, file hashes

### Six Analysis Modules

| Module | What It Does |
|---|---|
| **Subject Line Analysis** | Detects urgency language, fake Re: prefixes, embedded IDs, phishing keyword density |
| **IP Reputation** | Checks against known malicious IP ranges, estimates geo-risk, calculates abuse confidence score |
| **URL Analysis** | Detects hosting abuse (Google Sites, OneDrive), suspicious paths, URL shorteners, bad TLDs |
| **Email Authentication** | Parses and evaluates SPF, DKIM, and DMARC results with detailed findings |
| **Sender Analysis** | Flags free-email-to-corporate mismatch, delivery location, phish confidence level |
| **URL Sandbox** | Simulated detonation output + direct links to ANY.RUN, Hybrid Analysis, URLScan, Joe Sandbox |

### External Tool Integration
Every analysis module includes one-click links to:
- **IP checks:** VirusTotal, AbuseIPDB, IPVoid, Shodan
- **URL checks:** URLScan.io, URLVoid, VirusTotal, APIVoid
- **Sandbox:** ANY.RUN, Hybrid Analysis, Joe Sandbox
- **Email auth:** MXToolbox SPF, DKIM, DMARC

### Client Report
- Opens a formatted print-ready HTML report in a new window
- Auto-triggers the browser print dialog — select "Save as PDF"
- Covers: Email Details, IOCs, Authentication Results, Threat Scores, Impact Assessment, Recommendations, Verification Links
- Fully CSP-compliant — no external libraries, no blob URLs

---

## Getting Started

No installation. No dependencies. No build step.

1. Download `phishguard.html`
2. Open it in any modern browser (Chrome, Firefox, Edge, Safari)
3. Paste a phishing log and click **Analyze**

Or host it instantly for free:

```
Netlify Drop → drag phishguard.html to netlify.com/drop → get a live URL
GitHub Pages → upload as index.html → Settings → Pages → Enable
```

---

## Supported Log Formats

### Mimecast URL Protect
```json
{
  "source_ip": "209.85.222.172",
  "url": "https://sites.google.com/view/legalcounsel-notice",
  "is_blocked": "true",
  "source_json": {
    "action": "Block",
    "recipients": "user@company.ae",
    "subject": "Attention Requested",
    "senderEnvelope": "attacker@gmail.com",
    "urlCategory": "Phishing & Fraud"
  }
}
```

### Microsoft 365 Defender (TIMailData)
```json
{
  "AuthDetails": [
    {"Name": "SPF", "Value": "Soft fail"},
    {"Name": "DKIM", "Value": "Fail"},
    {"Name": "DMARC", "Value": "Fail"}
  ],
  "P1Sender": "attacker@gmail.com",
  "Recipients": ["user@company.ae"],
  "SenderIp": "91.220.42.227",
  "Subject": "Re: Inquiry Regarding Insurance Refund",
  "Verdict": "Phish"
}
```

---

## How the Parser Works

PhishGuard uses a multi-strategy parser that handles real-world log quality issues:

1. **Standard JSON.parse** — tries the raw input as-is
2. **repairJSON** — character-level scanner that detects and escapes unescaped inner double quotes inside string values (e.g. `"subject": "Notice: "Company Name" (ID: 123)"`)
3. **Smart quote normalization** — converts Unicode curly quotes (U+201C/U+201D) to escaped straight quotes before reparsing
4. **Strip and retry** — removes curly quotes entirely as a last resort
5. **Regex fallback** — extracts individual fields using pattern matching even if the full JSON cannot be parsed

The `extractSubject()` function has its own priority chain that always runs `repairJSON` first to guarantee the complete subject is returned, preventing the common truncation-at-inner-quote bug.

---

## Risk Scoring

Each module returns a score from 0–100. The overall risk is the weighted average across all modules.

| Score | Verdict |
|---|---|
| 65–100 | CONFIRMED PHISHING |
| 40–64 | HIGH RISK |
| 20–39 | SUSPICIOUS |
| 0–19 | LOW RISK |

---

## Privacy

- All processing happens entirely in the browser
- No data is sent to any external server
- No analytics, no tracking, no cookies
- External tool links (VirusTotal, AbuseIPDB, etc.) open in new tabs — you choose what to submit

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Vanilla HTML, CSS, JavaScript (ES2020+) |
| Fonts | IBM Plex Mono, Syne, DM Sans (Google Fonts) |
| PDF | Browser print dialog (`window.open` + `window.print`) |
| Parsing | Custom `repairJSON` + multi-strategy fallback chain |
| External APIs | None — all analysis is local heuristics |

---

## File Structure

```
phishguard.html     ← entire application (single file, ~82KB)
README.md           ← this file
```

---

## Contributing

Pull requests welcome. Key areas for contribution:

- Real VirusTotal / AbuseIPDB API integration (bring your own key)
- Additional log format support (Proofpoint, Cisco ESA, CrowdStrike)
- Hash file analysis module (SHA256 lookup)
- MITRE ATT&CK technique tagging
- Multi-log batch analysis

---

## Use Cases

- SOC Tier 1 analysts triaging phishing alerts
- Security engineers investigating email-based threats
- Consultants preparing client incident reports
- Students learning email threat analysis

---

## License

MIT License — free to use, modify, and distribute.

---

## Author

Built as a SOC analyst toolkit project. Designed to demonstrate practical threat analysis skills including log parsing, IOC extraction, threat intelligence integration, and incident reporting.
