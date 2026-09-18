# ReconForge

Practical reconnaissance and attack surface mapping for security researchers.

> “Find assets. Resolve infrastructure. Identify services. Map applications. Extract endpoints. Correlate everything. Validate only what is authorized.”

ReconForge is a practical, command-driven reconnaissance methodology for:
- Bug bounty hunters
- Penetration testers
- Red-teamers
- Security researchers
- OSINT researchers
- CTF players
- Students learning real-world reconnaissance

This repository is methodology-first and terminal-first.

It is designed to be used while hunting.
It is not a dump of tools with no explanation.
It is not a theory-heavy document that delays action.

The workflow is simple:

SCOPE
  ↓
DISCOVERY
  ↓
RESOLUTION
  ↓
HTTP ENUMERATION
  ↓
INFRASTRUCTURE
  ↓
PORTS
  ↓
SERVICES
  ↓
WEB APPLICATIONS
  ↓
CRAWLING
  ↓
HISTORICAL DATA
  ↓
CONTENT
  ↓
JAVASCRIPT
  ↓
APIs
  ↓
PARAMETERS
  ↓
CLOUD
  ↓
PUBLIC CODE
  ↓
CORRELATION
  ↓
MANUAL REVIEW
  ↓
AUTHORIZED VALIDATION
  ↓
REPORT

---

## Authorization

Use ReconForge only against systems you are explicitly authorized to assess.

Examples:
- In-scope bug bounty assets
- Your own infrastructure
- Authorized penetration tests
- Internal security assessments
- CTF environments
- Security labs
- Training environments

Before scanning, establish:
- In scope
- Out of scope
- Rate limits
- Allowed methods
- Prohibited methods
- Third-party restrictions
- Automation restrictions
- Testing windows

Do not assume that:

```text
example.com
     ↓
203.0.113.10