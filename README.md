#ReconForge

Reconnaissance & Attack Surface Mapping for Security Researchers

«Find assets. Understand infrastructure. Map applications. Correlate everything. Validate only what is authorized.»

ReconForge is a practical reconnaissance methodology for:

- Bug bounty hunters
- Penetration testers
- Red teamers
- Security researchers
- Security students
- CTF/lab practitioners

The goal is not to run 50 tools and collect thousands of useless results.

The goal is to answer:

What belongs to the target?
        ↓
What assets exist?
        ↓
Which assets are alive?
        ↓
Where are they hosted?
        ↓
What services are exposed?
        ↓
What technologies are running?
        ↓
What applications exist?
        ↓
What endpoints and parameters exist?
        ↓
What existed historically?
        ↓
How are the assets connected?
        ↓
What deserves manual investigation?
        ↓
What can be safely validated within scope?

ReconForge is methodology-first.

Tools change.

The methodology stays.

---

Table of Contents

- "1. Authorization First" (#1-authorization-first)
- "2. Recon Philosophy" (#2-recon-philosophy)
- "3. Recon Mindset" (#3-recon-mindset)
- "4. Complete Recon Workflow" (#4-complete-recon-workflow)
- "5. Repository Structure" (#5-repository-structure)
- "6. Installation" (#6-installation)
- "7. ProjectDiscovery Installation" (#7-projectdiscovery-installation)
- "8. Amass Installation" (#8-amass-installation)
- "9. Scope Management" (#9-scope-management)
- "10. Phase 0 — Target Profiling" (#10-phase-0--target-profiling)
- "11. Phase 1 — Passive Reconnaissance" (#11-phase-1--passive-reconnaissance)
- "12. Phase 2 — Subdomain Enumeration" (#12-phase-2--subdomain-enumeration)
- "13. Phase 3 — Certificate Transparency" (#13-phase-3--certificate-transparency)
- "14. Phase 4 — DNS Enumeration" (#14-phase-4--dns-enumeration)
- "15. Phase 5 — IP & ASN Mapping" (#15-phase-5--ip--asn-mapping)
- "16. Phase 6 — HTTP Discovery" (#16-phase-6--http-discovery)
- "17. Phase 7 — Technology Fingerprinting" (#17-phase-7--technology-fingerprinting)
- "18. Phase 8 — WAF/CDN Detection" (#18-phase-8--wafcdn-detection)
- "19. Phase 9 — Port Discovery" (#19-phase-9--port-discovery)
- "20. Phase 10 — Service Enumeration" (#20-phase-10--service-enumeration)
- "21. Phase 11 — TLS Enumeration" (#21-phase-11--tls-enumeration)
- "22. Phase 12 — Web Crawling" (#22-phase-12--web-crawling)
- "23. Phase 13 — Historical URLs" (#23-phase-13--historical-urls)
- "24. Phase 14 — Content Discovery" (#24-phase-14--content-discovery)
- "25. Phase 15 — JavaScript Recon" (#25-phase-15--javascript-recon)
- "26. Phase 16 — Source Maps" (#26-phase-16--source-maps)
- "27. Phase 17 — API Discovery" (#27-phase-17--api-discovery)
- "28. Phase 18 — Parameter Discovery" (#28-phase-18--parameter-discovery)
- "29. Phase 19 — Authentication Surface Mapping" (#29-phase-19--authentication-surface-mapping)
- "30. Phase 20 — Cloud & Infrastructure Recon" (#30-phase-20--cloud--infrastructure-recon)
- "31. Phase 21 — Repository & Public-Code Recon" (#31-phase-21--repository--public-code-recon)
- "32. Phase 22 — OSINT & Search Recon" (#32-phase-22--osint--search-recon)
- "33. Phase 23 — Screenshots & Visual Mapping" (#33-phase-23--screenshots--visual-mapping)
- "34. Phase 24 — Security Headers" (#34-phase-24--security-headers)
- "35. Phase 25 — Vulnerability Intelligence" (#35-phase-25--vulnerability-intelligence)
- "36. Phase 26 — Controlled Validation" (#36-phase-26--controlled-validation)
- "37. Phase 27 — Asset Correlation" (#37-phase-27--asset-correlation)
- "38. Phase 28 — Prioritization" (#38-phase-28--prioritization)
- "39. Phase 29 — Manual Investigation" (#39-phase-29--manual-investigation)
- "40. Phase 30 — Evidence Collection" (#40-phase-30--evidence-collection)
- "41. Phase 31 — Reporting" (#41-phase-31--reporting)
- "42. Phase 32 — Continuous Recon" (#42-phase-32--continuous-recon)
- "43. 15-Minute Recon Workflow" (#43-15-minute-recon-workflow)
- "44. Deep Recon Workflow" (#44-deep-recon-workflow)
- "45. Useful Command Recipes" (#45-useful-command-recipes)
- "46. Common Mistakes" (#46-common-mistakes)
- "47. Troubleshooting" (#47-troubleshooting)
- "48. Final Checklist" (#48-final-checklist)

---

1. Authorization First

ReconForge is intended for authorized security testing only.

Examples:

- Bug bounty targets explicitly listed as in-scope
- Systems you own
- Authorized penetration tests
- Internal security assessments
- CTFs
- Security labs
- Research environments where testing is permitted

Before scanning anything:

Read the program policy.
Read the scope.
Read exclusions.
Read rate limits.
Read prohibited techniques.
Read testing windows.
Read third-party restrictions.

Do not assume:

Discovered asset = automatically in scope

For example:

target.example.com
        ↓
CNAME
        ↓
third-party.provider.example
        ↓
shared infrastructure

Finding the infrastructure does not automatically authorize testing the entire provider.

Always verify scope.

Never perform

- Credential attacks
- Password spraying
- Persistence
- Unauthorized access
- Destructive testing
- Data destruction
- Denial-of-service activity
- Mass exploitation
- Testing third-party systems outside scope
- Using discovered credentials/tokens to access systems unless explicitly authorized

ReconForge focuses on:

Discovery
Enumeration
Mapping
Correlation
Fingerprinting
Controlled validation
Evidence collection

---

2. Recon Philosophy

Bad reconnaissance:

Run tools
    ↓
Collect 100,000 results
    ↓
grep "interesting"
    ↓
Open random URLs
    ↓
Hope for a vulnerability

Better reconnaissance:

Scope
 ↓
Passive discovery
 ↓
Subdomains
 ↓
DNS
 ↓
IP / ASN
 ↓
HTTP
 ↓
Technology
 ↓
Ports
 ↓
Services
 ↓
Crawling
 ↓
Historical URLs
 ↓
JavaScript
 ↓
APIs
 ↓
Parameters
 ↓
Cloud / OSINT
 ↓
Correlation
 ↓
Manual investigation
 ↓
Controlled validation
 ↓
Evidence
 ↓
Report

The objective is not to collect the largest dataset.

The objective is to reduce uncertainty.

---

3. Recon Mindset

Think in relationships.

Example:

dev.example.com
        |
        +---- DNS
        |
        +---- 203.0.113.10
                    |
                    +---- Cloud provider
                    |
                    +---- nginx
                    |
                    +---- Node.js
                    |
                    +---- React
                    |
                    +---- /api/v1
                    |
                    +---- /graphql
                    |
                    +---- JavaScript bundle
                                |
                                +---- internal API reference
                                |
                                +---- additional endpoint

A single hostname can reveal:

Hostname
    ↓
DNS
    ↓
IP
    ↓
ASN
    ↓
Provider
    ↓
Ports
    ↓
Services
    ↓
Web server
    ↓
Framework
    ↓
Application
    ↓
Endpoints
    ↓
Parameters

That relationship is more valuable than a raw subdomain count.

---

4. Complete Recon Workflow

                    TARGET
                       |
             +---------+---------+
             |                   |
         PASSIVE              ACTIVE
             |                   |
       CT / OSINT             DNS
       Search                 HTTP
       Repos                  Ports
       Archives              Crawling
             |                   |
             +---------+---------+
                       |
                  CORRELATION
                       |
        +--------------+--------------+
        |              |              |
       DNS            HTTP           IP
        |              |              |
        +--------------+--------------+
                       |
                  TECHNOLOGY
                       |
             +---------+---------+
             |                   |
            Web                 API
             |                   |
          JS / URLs          Parameters
             |                   |
             +---------+---------+
                       |
                  PRIORITIZE
                       |
                MANUAL REVIEW
                       |
             AUTHORIZED VALIDATION
                       |
                    REPORT

---

5. Repository Structure

Recommended structure:

reconforge/
├── README.md
├── LICENSE
├── install.sh
├── recon.sh
│
├── config/
│   ├── scope.yaml
│   ├── resolvers.txt
│   └── wordlists.yaml
│
├── modules/
│   ├── passive/
│   ├── dns/
│   ├── network/
│   ├── web/
│   ├── crawling/
│   ├── javascript/
│   ├── api/
│   ├── cloud/
│   ├── osint/
│   └── vulnerability/
│
├── data/
│   ├── raw/
│   ├── normalized/
│   ├── screenshots/
│   └── reports/
│
└── output/
    ├── subdomains.txt
    ├── dns.txt
    ├── ips.txt
    ├── live.txt
    ├── ports.txt
    ├── services.txt
    ├── urls.txt
    ├── historical.txt
    ├── javascript.txt
    ├── endpoints.txt
    ├── parameters.txt
    └── technologies.json

Keep raw data.

Never overwrite the original discovery data unnecessarily.

---

6. Installation

Recommended systems:

- Kali Linux
- Ubuntu
- Debian
- Parrot OS

Install base packages:

sudo apt update

sudo apt install -y \
  git \
  curl \
  wget \
  jq \
  unzip \
  dnsutils \
  whois \
  python3 \
  python3-pip

Install Go:

sudo apt install -y golang

Check:

go version

Create Go binary directory:

mkdir -p "$HOME/go/bin"

Add it to PATH:

echo 'export PATH="$PATH:$HOME/go/bin"' >> "$HOME/.bashrc"
source "$HOME/.bashrc"

Verify:

echo "$PATH"

---

7. ProjectDiscovery Installation

Install:

go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
go install -v github.com/projectdiscovery/naabu/v2/cmd/naabu@latest
go install -v github.com/projectdiscovery/katana/cmd/katana@latest
go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest

Verify:

subfinder -h
dnsx -h
httpx -h
naabu -h
katana -h
nuclei -h

Update Nuclei templates:

nuclei -update-templates

---

8. Amass Installation

Install:

go install -v github.com/owasp-amass/amass/v4/...@master

Verify:

amass -h

Useful modes:

amass enum
amass intel
amass viz
amass track
amass db

---

9. Scope Management

Create:

mkdir -p config output data/raw data/normalized

Example "config/scope.yaml":

scope:
  domains:
    - example.com
    - "*.example.com"

  ips:
    - "203.0.113.10"

  urls:
    - "https://example.com"

  exclusions:
    - "thirdparty.example.net"

rules:
  respect_rate_limits: true
  no_destructive_testing: true
  no_credential_attacks: true
  no_unauthorized_access: true

Set your target:

export TARGET="example.com"

Create workspace:

mkdir -p "data/$TARGET"
mkdir -p "data/$TARGET"/{raw,normalized,screenshots,reports}
mkdir -p "output/$TARGET"

Use one directory per target.

---

10. Phase 0 — Target Profiling

Before running scanners, understand the organization.

Record:

Root domain
Subdomains
Brand names
Known applications
Known APIs
Technology
CMS
CDN
WAF
Cloud provider
Hosting provider
Authentication provider
Mobile applications
Public repositories
Third-party integrations

Basic:

whois "$TARGET"

DNS:

dig "$TARGET" A
dig "$TARGET" AAAA
dig "$TARGET" MX
dig "$TARGET" NS
dig "$TARGET" TXT
dig "$TARGET" CNAME

HTTP:

curl -skI "https://$TARGET"

Questions:

Who owns the domain?

Where is DNS hosted?

Is there a CDN?

Is there a WAF?

Which cloud provider appears to be involved?

Which applications are public?

Which authentication systems are used?

Are APIs exposed?

Are development environments visible?

Are there mobile applications?

Are there public repositories?

Do not start by scanning everything.

Build the target model first.

---

11. Phase 1 — Passive Reconnaissance

Passive sources can reveal:

- Certificate names
- DNS records
- Historical infrastructure
- Public repositories
- Search engine results
- Public documentation
- Archived URLs
- Technology information
- Public IP information
- Previously exposed applications

Useful sources/tools:

Certificate Transparency
Search engines
DNS/RDAP/WHOIS
Git repositories
Web archives
Internet indexes
Threat-intelligence platforms
Public datasets
Vendor documentation

Basic search:

site:example.com

Subdomain-focused search:

site:example.com -www

Look for naming patterns:

dev
development
stage
staging
test
qa
uat
beta
demo
admin
portal
api
internal
vpn
mail
legacy
old

Important:

Hostname discovery != vulnerability

Treat every finding as a lead.

---

12. Phase 2 — Subdomain Enumeration

Start with Subfinder:

subfinder -d "$TARGET" -silent

Save:

subfinder -d "$TARGET" -silent -o subfinder.txt

Use multiple domains:

subfinder -dL domains.txt -silent -o subdomains.txt

Broader source collection:

subfinder -d "$TARGET" -all -silent -o subfinder-all.txt

JSON:

subfinder -d "$TARGET" -json -o subfinder.json

Amass:

amass enum -passive -d "$TARGET" -o amass.txt

Verbose source information:

amass enum -passive -d "$TARGET" -v -src -o amass-source.txt

Merge:

cat subfinder.txt amass.txt | sort -u > all-subdomains.txt

Clean:

sed '/^$/d' all-subdomains.txt | sort -u > subdomains-clean.txt

Count:

wc -l subdomains-clean.txt

Do not optimize for the count.

Optimize for useful assets.

---

13. Phase 3 — Certificate Transparency

Certificate logs are excellent for historical and current hostname discovery.

Query:

curl -s \
  "https://crt.sh/?q=%25.$TARGET&output=json" |
jq -r '.[].name_value' |
tr '\r' '\n' |
sort -u

Save:

curl -s \
  "https://crt.sh/?q=%25.$TARGET&output=json" |
jq -r '.[].name_value' |
tr '\r' '\n' |
sort -u > crtsh.txt

Merge:

cat subdomains-clean.txt crtsh.txt |
sort -u > all-subdomains.txt

Find interesting labels:

grep -Ei \
'(^|\.)(dev|development|stage|staging|test|qa|uat|beta|demo|admin|portal|api|internal|vpn|legacy|old)\.' \
all-subdomains.txt

Again:

Interesting hostname != vulnerability

It simply deserves investigation.

---

14. Phase 4 — DNS Enumeration

Resolve discovered hosts:

dnsx -l all-subdomains.txt -silent

Save:

dnsx \
  -l all-subdomains.txt \
  -silent \
  -o resolved.txt

Collect A records:

dnsx \
  -l all-subdomains.txt \
  -a \
  -resp \
  -silent \
  -o dns-a.txt

Collect AAAA:

dnsx \
  -l all-subdomains.txt \
  -aaaa \
  -resp \
  -silent \
  -o dns-aaaa.txt

CNAME:

dnsx \
  -l all-subdomains.txt \
  -cname \
  -resp \
  -silent \
  -o dns-cname.txt

MX:

dnsx \
  -l all-subdomains.txt \
  -mx \
  -resp \
  -silent \
  -o dns-mx.txt

NS:

dnsx \
  -l all-subdomains.txt \
  -ns \
  -resp \
  -silent \
  -o dns-ns.txt

TXT:

dnsx \
  -l all-subdomains.txt \
  -txt \
  -resp \
  -silent \
  -o dns-txt.txt

Combined:

dnsx \
  -l all-subdomains.txt \
  -a -aaaa -cname -mx -ns -txt \
  -resp \
  -silent \
  -o dns.txt

Questions:

Which hosts resolve?

Which IPs are shared?

Which domains point to cloud infrastructure?

Which hosts use CDN infrastructure?

Which hosts have unusual CNAMEs?

Are development systems separated?

Are mail systems external?

Are third-party SaaS providers involved?

---

15. Phase 5 — IP & ASN Mapping

For a specific host:

dig "$TARGET" A +short

IPv6:

dig "$TARGET" AAAA +short

CNAME:

dig "$TARGET" CNAME +short

MX:

dig "$TARGET" MX +short

NS:

dig "$TARGET" NS +short

TXT:

dig "$TARGET" TXT +short

WHOIS:

whois "$TARGET"

For each discovered IP, record:

Hostname
IP
ASN
Provider
Cloud
Region
Reverse DNS
CDN
Load balancer
Shared infrastructure

Conceptually:

domain
  ↓
DNS
  ↓
IP
  ↓
ASN
  ↓
provider
  ↓
infrastructure

Do not assume every IP associated with a hostname belongs exclusively to the target.

Cloud and CDN infrastructure are frequently shared.

---

16. Phase 6 — HTTP Discovery

Probe HTTP services:

httpx \
  -l all-subdomains.txt \
  -silent

Save:

httpx \
  -l all-subdomains.txt \
  -silent \
  -o live.txt

Status code:

httpx \
  -l all-subdomains.txt \
  -status-code \
  -silent

Title:

httpx \
  -l all-subdomains.txt \
  -title \
  -silent

Technology:

httpx \
  -l all-subdomains.txt \
  -tech-detect \
  -silent

Web server:

httpx \
  -l all-subdomains.txt \
  -web-server \
  -silent

Follow redirects:

httpx \
  -l all-subdomains.txt \
  -follow-redirects \
  -silent

Combined:

httpx \
  -l all-subdomains.txt \
  -status-code \
  -title \
  -tech-detect \
  -web-server \
  -follow-redirects \
  -silent \
  -o live-detailed.txt

JSON:

httpx \
  -l all-subdomains.txt \
  -status-code \
  -title \
  -tech-detect \
  -web-server \
  -follow-redirects \
  -json \
  -o httpx.json

Extract URLs:

jq -r '.url // empty' httpx.json |
sort -u > live.txt

---

17. Phase 7 — Technology Fingerprinting

WhatWeb:

whatweb "https://$TARGET"

Verbose:

whatweb -v "https://$TARGET"

Higher aggression:

whatweb -a 1 "https://$TARGET"

Fingerprint:

Web server
Framework
CMS
JavaScript libraries
Programming language
Analytics
CDN
Reverse proxy
Security products

Technology fingerprints are leads.

Do not treat fingerprints as proof.

Confirm important technologies through:

HTTP headers
HTML
JavaScript
Error pages
Asset names
Response behavior
Public documentation
Version information

---

18. Phase 8 — WAF/CDN Detection

Run:

wafw00f "https://$TARGET"

Multiple URLs:

wafw00f -i live.txt

Record:

WAF
CDN
Reverse proxy
Gateway
Load balancer

WAF detection is for infrastructure understanding.

Do not treat it as an invitation to bypass security controls.

---

19. Phase 9 — Port Discovery

Naabu:

naabu -host "$TARGET"

Save:

naabu \
  -host "$TARGET" \
  -o ports.txt

Specific ports:

naabu \
  -host "$TARGET" \
  -p 80,443,8080,8443 \
  -o web-ports.txt

Multiple hosts:

naabu \
  -list all-subdomains.txt \
  -o ports.txt

JSON:

naabu \
  -list all-subdomains.txt \
  -json \
  -o ports.json

A practical flow:

Subdomains
    ↓
Resolve
    ↓
HTTP discovery
    ↓
Port discovery
    ↓
Interesting ports
    ↓
Detailed service enumeration

Avoid scanning networks that are not explicitly authorized.

---

20. Phase 10 — Service Enumeration

Nmap basic:

nmap "$TARGET"

Service detection:

nmap -sV "$TARGET"

Default scripts:

nmap -sC -sV "$TARGET"

Specific ports:

nmap -sC -sV -p 80,443,8080,8443 "$TARGET"

Save:

nmap \
  -sC -sV \
  -p 80,443,8080,8443 \
  -oN nmap.txt \
  "$TARGET"

XML:

nmap \
  -sC -sV \
  -p 80,443,8080,8443 \
  -oX nmap.xml \
  "$TARGET"

All TCP ports:

nmap -p- "$TARGET"

Use full port scans only where scope and program rules permit them.

Recommended workflow:

Naabu
  ↓
Interesting ports
  ↓
Nmap
  ↓
Service/version
  ↓
Manual research

---

21. Phase 11 — TLS Enumeration

SSLScan:

sslscan "$TARGET:443"

Save:

sslscan "$TARGET:443" > sslscan.txt

Inspect:

Certificate
Issuer
Subject
SANs
Expiration
Protocols
Ciphers
Certificate chain
HSTS
STARTTLS

For TLS-related research, also inspect:

openssl s_client \
  -connect "$TARGET:443" \
  -servername "$TARGET"

Certificate:

openssl s_client \
  -connect "$TARGET:443" \
  -servername "$TARGET" \
  </dev/null 2>/dev/null |
openssl x509 -noout -subject -issuer -dates -ext subjectAltName

Certificate SANs can reveal additional hostnames.

---

22. Phase 12 — Web Crawling

Katana:

katana \
  -u "https://$TARGET" \
  -silent

Save:

katana \
  -u "https://$TARGET" \
  -silent \
  -o crawl.txt

JSONL:

katana \
  -u "https://$TARGET" \
  -jsonl \
  -o crawl.jsonl

Multiple targets:

katana \
  -list live.txt \
  -silent \
  -o endpoints.txt

Look for:

/api/
/api/v1/
/api/v2/
/graphql
/login
/logout
/oauth
/admin
/upload
/download
/search
/export
/internal
/debug
/health
/status
/docs
/swagger

Extract unique URLs:

cat crawl.txt |
sort -u > urls.txt

The crawl is a map.

It is not automatically a vulnera