
# K7R3

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
```

means the IP is automatically in scope.

The IP may belong to:
- A CDN
- Shared hosting
- A cloud provider
- A reverse proxy
- Another customer
- Third-party infrastructure

Scope comes first.

---

## Quick Start

If you already know the workflow and want to start immediately:

```bash
export TARGET="example.com"

mkdir -p reconforge/$TARGET/{raw,normalized,screenshots,reports}
cd reconforge/$TARGET
```

### 1. Subdomains

```bash
subfinder -d "$TARGET" -silent -o raw/subfinder.txt
amass enum -passive -d "$TARGET" -o raw/amass.txt

cat raw/subfinder.txt raw/amass.txt \
    | sed 's/\*\.//' \
    | sort -u \
    > normalized/subdomains.txt
```

### 2. Certificate Transparency

```bash
curl -s "https://crt.sh/?q=%25.$TARGET&output=json" \
    | jq -r '.[].name_value' \
    | tr '\r' '\n' \
    | sed 's/\*\.//' \
    | sort -u \
    >> normalized/subdomains.txt

sort -u normalized/subdomains.txt -o normalized/subdomains.txt
```

### 3. DNS

```bash
dnsx \
    -l normalized/subdomains.txt \
    -a \
    -aaaa \
    -cname \
    -resp \
    -silent \
    -o normalized/dns.txt
```

### 4. HTTP

```bash
httpx \
    -l normalized/subdomains.txt \
    -silent \
    -status-code \
    -title \
    -tech-detect \
    -web-server \
    -follow-redirects \
    -o normalized/live.txt
```

### 5. Crawl

```bash
awk '{print $1}' normalized/live.txt \
    | katana \
    -silent \
    -o normalized/crawled.txt
```

### 6. Historical URLs

```bash
cat normalized/subdomains.txt | gau > raw/gau.txt
cat normalized/subdomains.txt | waybackurls > raw/wayback.txt

cat raw/gau.txt raw/wayback.txt \
    | sort -u \
    > normalized/historical.txt
```

### 7. JavaScript

```bash
grep -Ei '\.js([?#]|$)' normalized/crawled.txt \
    | sort -u \
    > normalized/javascript.txt
```

### 8. Interesting endpoints

```bash
grep -Ei \
'/(api|graphql|admin|internal|debug|login|oauth|upload|download|export|swagger|openapi)(/|[?#]|$)' \
normalized/crawled.txt \
| sort -u \
> normalized/interesting-endpoints.txt
```

At this point, stop thinking:

> “I found 2,000 URLs.”

Start thinking:
- Which applications exist?
- Which APIs exist?
- Which hosts use different technologies?
- Which endpoints are interesting?
- Which assets belong to the same infrastructure?
- Which historical endpoints disappeared?
- Which JavaScript files expose functionality?
- Which assets deserve manual review?

---

## Recon Philosophy

Recon is not:

```text
Run tools
↓
Collect thousands of lines
↓
grep "admin"
↓
Hope
```

Recon is:

```text
DISCOVER
↓
NORMALIZE
↓
RESOLVE
↓
CLASSIFY
↓
CORRELATE
↓
PRIORITIZE
↓
MANUALLY INVESTIGATE
```

Every command should answer a question.

Before running a tool, ask:
- Input: What am I giving the tool?
- Output: What will it produce?
- Value: Why do I care?
- Next: What will I do with the result?

Example:

```text
api.example.com
    ↓
DNS
    ↓
CNAME → cloud provider
    ↓
HTTP
    ↓
200 / API Gateway
    ↓
Technology fingerprint
    ↓
JavaScript
    ↓
/api/v2/
 /graphql
    ↓
Historical URLs
    ↓
/api/v1/
    ↓
Manual investigation
```

The relationship between assets is often more important than the individual finding.

---

## Directory Structure

Use one directory per target:

```bash
mkdir -p reconforge/$TARGET/{raw,normalized,screenshots,reports}
```

Recommended layout:

```text
reconforge/
└── example.com/
    ├── raw/
    │   ├── subfinder.txt
    │   ├── amass.txt
    │   ├── crtsh.txt
    │   ├── gau.txt
    │   ├── wayback.txt
    │   └── nmap/
    │
    ├── normalized/
    │   ├── subdomains.txt
    │   ├── dns.txt
    │   ├── ips.txt
    │   ├── live.txt
    │   ├── urls.txt
    │   ├── historical.txt
    │   ├── javascript.txt
    │   ├── endpoints.txt
    │   ├── parameters.txt
    │   └── technologies.json
    │
    ├── screenshots/
    │
    └── reports/
```

Never mix raw evidence with processed data.

---

## Installation

### Debian / Ubuntu / Kali

```bash
sudo apt update
sudo apt install -y \
    git \
    curl \
    wget \
    jq \
    unzip \
    dnsutils \
    whois \
    nmap \
    masscan \
    python3 \
    python3-pip
```

### Go

Check:

```bash
go version
```

Add Go binaries:

```bash
export PATH="$PATH:$HOME/go/bin"
```

Persist:

```bash
echo 'export PATH="$PATH:$HOME/go/bin"' >> ~/.bashrc
source ~/.bashrc
```

### ProjectDiscovery toolchain

```bash
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest
go install -v github.com/projectdiscovery/naabu/v2/cmd/naabu@latest
go install -v github.com/projectdiscovery/katana/cmd/katana@latest
go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
```

Verify:

```bash
subfinder -version
dnsx -version
httpx -version
naabu -version
katana -version
nuclei -version
```

---

## Scope Management

Create a scope file:

```bash
mkdir -p config
nano config/scope.txt
```

Example:

```text
example.com
*.example.com
```

Add exclusions:

```bash
nano config/exclude.txt
```

Example:

```text
thirdparty.example.com
```

Apply filtering:

```bash
grep -vFf config/exclude.txt normalized/subdomains.txt \
    > normalized/in-scope-subdomains.txt
```

Do not blindly scan everything discovered.

Classify infrastructure first.

---

## Phase 0 — Target Profiling

Before automation, manually inspect:
- https://example.com
- https://example.com/robots.txt
- https://example.com/sitemap.xml

Fetch headers:

```bash
curl -I https://example.com
```

Fetch robots:

```bash
curl -s https://example.com/robots.txt
```

Fetch sitemap:

```bash
curl -s https://example.com/sitemap.xml
```

Check DNS:

```bash
dig example.com
```

Check technologies:

```bash
whatweb https://example.com
```

Record:
- Application
- Framework
- CMS
- CDN
- WAF
- Server
- Cloud
- Authentication
- API
- Mobile app
- Public repos
- Third-party services

Use technology detection to guide investigation, not as a final outcome.

---

## Phase 1 — Passive Reconnaissance

Useful passive sources:
- Certificate Transparency
- DNS / RDAP
- Search engines
- Internet archives
- Public repositories
- Public docs
- Security datasets
- Organization pages

Passive discovery often reveals:
- dev
- stage
- staging
- qa
- uat
- test
- beta
- admin
- portal
- api
- legacy
- old
- internal
- vpn

A hostname is a lead, not a vulnerability.

---

## Phase 2 — Subdomain Enumeration

### Subfinder

```bash
subfinder -d "$TARGET" -silent
```

Save results:

```bash
subfinder -d "$TARGET" -silent -o raw/subfinder.txt
```

Multiple targets:

```bash
subfinder -dL config/domains.txt -silent -o raw/subfinder.txt
```

Normalize:

```bash
cat raw/subfinder*.txt \
    | sed 's/\*\.//' \
    | sort -u \
    > normalized/subdomains.txt
```

### Amass

Passive:

```bash
amass enum -passive -d "$TARGET" -o raw/amass.txt
```

Merge:

```bash
cat raw/subfinder.txt raw/amass.txt \
    | sed 's/\*\.//' \
    | sort -u \
    > normalized/subdomains.txt
```

---

## Phase 3 — Certificate Transparency

```bash
curl -s "https://crt.sh/?q=%25.$TARGET&output=json" \
    -o raw/crtsh.json
```

Extract names:

```bash
jq -r '.[].name_value' raw/crtsh.json \
    | tr '\r' '\n' \
    | sed 's/\*\.//' \
    | sort -u \
    > raw/crtsh.txt
```

Merge:

```bash
cat normalized/subdomains.txt raw/crtsh.txt \
    | sort -u \
    > normalized/subdomains-final.txt

mv normalized/subdomains-final.txt normalized/subdomains.txt
```

Look for interesting patterns:

```bash
grep -Ei '(^|\.)(dev|development|stage|staging|test|qa|uat|beta|admin|portal|api|internal|legacy|old|vpn)(\.|$)' normalized/subdomains.txt
```

Interesting names are only leads.

---

## Phase 4 — DNS Enumeration

```bash
dnsx \
    -l normalized/subdomains.txt \
    -a \
    -aaaa \
    -cname \
    -mx \
    -ns \
    -txt \
    -resp \
    -silent \
    -o raw/dns.txt
```

Extract IPs:

```bash
awk '{print $2}' raw/a-records.txt | sort -u > normalized/ips.txt
```

Investigate:
- Host
- A record
- IP
- ASN
- Provider
- CDN / cloud

---

## Phase 5 — IP and ASN Mapping

For individual hosts:

```bash
dig example.com
dig example.com A
dig example.com AAAA
dig example.com CNAME
dig example.com MX
dig example.com NS
dig example.com TXT
whois example.com
whois 203.0.113.10
```

Check:
- Dedicated infrastructure?
- Shared infrastructure?
- CDN?
- Cloud-hosted?
- Multiple domains using the same IP?
- Ownership clear?

---

## Phase 6 — Live Host Discovery

```bash
httpx \
    -l normalized/subdomains.txt \
    -status-code \
    -title \
    -tech-detect \
    -web-server \
    -follow-redirects \
    -silent \
    -o normalized/live.txt
```

JSON output:

```bash
httpx \
    -l normalized/subdomains.txt \
    -status-code \
    -title \
    -tech-detect \
    -json \
    -o normalized/httpx.json
```

Extract URLs:

```bash
jq -r '.url' normalized/httpx.json | sort -u > normalized/live-urls.txt
```

Status triage:

```bash
grep '301\|302\|307\|308' normalized/live.txt
grep '401\|403' normalized/live.txt
grep '200' normalized/live.txt
```

A 403 is a clue, not proof of anything.

---

## Phase 7 — Technology Fingerprinting

```bash
whatweb https://example.com
```

Verbose:

```bash
whatweb -v https://example.com
```

Multiple targets:

```bash
whatweb --input-file=normalized/live-urls.txt --log-verbose=raw/whatweb.txt
```

Use technology to guide:
- Application structure
- Likely routes
- Client-side code
- API discovery

Technology detection is not proof of vulnerability.

---

## Phase 8 — WAF and CDN Detection

```bash
wafw00f https://example.com
```

Multiple targets:

```bash
wafw00f -i normalized/live-urls.txt
```

Record:
- WAF
- CDN
- Reverse proxy
- Gateway

Do not turn WAF detection into bypass guidance.

---

## Phase 9 — Port Discovery

### Naabu

Single host:

```bash
naabu -host 203.0.113.10
```

IP list:

```bash
naabu -list normalized/ips.txt -o raw/naabu.txt
```

Common web ports:

```bash
naabu -list normalized/ips.txt -p 80,443,8000,8080,8081,8443,8888 -o raw/web-ports.txt
```

Top ports:

```bash
naabu -list normalized/ips.txt -top-ports 100 -o raw/top-ports.txt
```

Only use broader scanning when explicitly authorized.

### Masscan

```bash
masscan 203.0.113.10 -p80,443 --rate 100
```

### RustScan

```bash
rustscan -a 203.0.113.10
```

Workflow:
```text
FAST DISCOVERY
   ↓
OPEN PORTS
   ↓
NMAP
   ↓
SERVICE DETECTION
   ↓
MANUAL REVIEW
```

---

## Phase 10 — Service Enumeration

```bash
nmap 203.0.113.10
nmap -sV 203.0.113.10
nmap -sC -sV 203.0.113.10
nmap -sC -sV -p80,443,8080,8443 203.0.113.10
nmap -p- -sV 203.0.113.10
```

Save results:

```bash
nmap -sC -sV -p80,443 -oN raw/nmap.txt 203.0.113.10
```

Think:
- Port
- Service
- Version
- Configuration
- Documentation
- Security relevance

---

## Phase 11 — TLS Reconnaissance

### SSLScan

```bash
sslscan example.com:443
sslscan example.com:443 > raw/sslscan.txt
```

Look for:
- Cert issuer
- SANs
- Expiration
- TLS versions
- Cipher suites
- Chain
- HSTS

### testssl.sh

```bash
./testssl.sh example.com
./testssl.sh --jsonfile raw/tls.json example.com
./testssl.sh --csvfile raw/tls.csv example.com
```

TLS findings need interpretation in context:
- Program policy
- Security posture
- Expected controls
- Actual exploitability

---

## Phase 12 — Web Crawling

```bash
katana -u https://example.com
katana -u https://example.com -silent -o raw/katana.txt
katana -u https://example.com -jsonl -o raw/katana.jsonl
```

Multiple targets:

```bash
katana -list normalized/live-urls.txt -silent -o normalized/crawled.txt
```

Search endpoints:

```bash
grep -Ei \
'/(api|graphql|admin|internal|debug|login|oauth|upload|download|export|swagger|openapi)(/|[?#]|$)' \
normalized/crawled.txt \
| sort -u \
> normalized/interesting-endpoints.txt
```

---

## Phase 13 — Historical URL Discovery

Historical data is one of the best ways to identify functionality no longer linked by the current app.

### GAU

```bash
gau "$TARGET" > raw/gau.txt
```

### Waybackurls

```bash
echo "$TARGET" | waybackurls > raw/wayback.txt
```

Merge:

```bash
cat raw/gau.txt raw/wayback.txt | sort -u > normalized/historical.txt
```

Search for old/admin routes:

```bash
grep -Ei \
'/(admin|administrator|manage|dashboard|internal)(/|[?#]|$)' \
normalized/historical.txt | sort -u
```

Historical presence does not prove current availability. Verify before testing.

---

## Phase 14 — Content Discovery

Use:
- ffuf
- feroxbuster
- gobuster
- dirsearch

Do not run huge wordlists across every host immediately.

First identify:
- Framework
- CMS
- Application type
- Existing routes
- Naming conventions

Then choose a targeted wordlist.

### ffuf

```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt
```

With extensions:

```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -e .html,.js,.json,.txt
```

Save JSON:

```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt -o raw/ffuf.json -of json
```

Always establish the normal 404 baseline first:

```bash
curl -i https://example.com/this-should-not-exist-123456
```

Then interpret discovered paths relative to that baseline.

---

## Phase 15 — JavaScript Reconnaissance

JavaScript often contains application structure not obvious from the HTML.

Extract JS:

```bash
grep -Ei '\.js([?#]|$)' normalized/crawled.txt | sort -u > normalized/javascript.txt
```

Fetch a file:

```bash
curl -s "https://example.com/app.js" -o raw/app.js
```

Search URLs:

```bash
grep -Eo 'https?://[^"'\'' ]+' raw/app.js | sort -u
```

Search API-looking paths:

```bash
grep -Eo '["'\'']/[^"'\'']{1,200}["'\'']' raw/app.js \
    | grep -Ei 'api|graphql|admin|auth|login|upload|download|export' \
    | sort -u
```

Search common API keywords:

```bash
grep -Ei 'api|graphql|swagger|openapi|authorization|bearer|endpoint|baseURL|baseUrl' raw/app.js
```

Look for:
- API base URLs
- API versions
- GraphQL endpoints
- Feature flags
- Route names
- Frontend configuration
- Third-party integrations
- Environment references

A string in JS is a lead, not a bug.

---

## Phase 16 — Source Map Analysis

Look for:

```bash
curl -s https://example.com/app.js | grep -E 'sourceMappingURL'
```

If a public source map is in scope:

```bash
curl -s https://example.com/app.js.map -o raw/app.js.map
jq '.sources' raw/app.js.map
```

Inspect for:
- Source paths
- Component names
- API clients
- Route definitions
- Development artifacts
- Debug info

Do not classify every exposed source map as a vulnerability. Determine:
- What is exposed?
- Is it intended?
- Does it contain sensitive info?
- What is the actual impact?

---

## Phase 17 — API Discovery

Start with crawled URLs:

```bash
grep -Ei \
'/(api|rest|graphql|swagger|openapi)(/|[?#]|$)' \
normalized/crawled.txt \
| sort -u \
> normalized/api-candidates.txt
```

Historical:

```bash
grep -Ei \
'/(api|rest|graphql|swagger|openapi)(/|[?#]|$)' \
normalized/historical.txt \
| sort -u \
>> normalized/api-candidates.txt
```

Deduplicate:

```bash
sort -u normalized/api-candidates.txt -o normalized/api-candidates.txt
```

Common docs:
- /swagger
- /swagger-ui
- /openapi.json
- /openapi.yaml
- /api-docs
- /docs
- /graphql
- /graphiql

Check only what is authorized:

```bash
for path in swagger swagger-ui openapi.json openapi.yaml api-docs docs; do
    echo "===== /$path ====="
    curl -sk -o /dev/null -w "%{http_code} %{url_effective}\n" "https://example.com/$path"
done
```

---

## Phase 18 — Parameter Discovery

Parameters may exist in:
- Query strings
- POST bodies
- JSON
- Forms
- Headers
- Cookies
- Path segments
- GraphQL variables

Extract query URLs:

```bash
grep '?' normalized/crawled.txt | sort -u > normalized/query-urls.txt
```

Extract parameter names:

```bash
sed 's/&/\n/g' normalized/query-urls.txt \
    | sed 's/.*?//' \
    | cut -d= -f1 \
    | sort -u \
    > normalized/parameters.txt
```

Historical parameters:

```bash
grep '?' normalized/historical.txt \
    | sed 's/&/\n/g' \
    | sed 's/.*?//' \
    | cut -d= -f1 \
    | sort -u \
    >> normalized/parameters.txt

sort -u normalized/parameters.txt -o normalized/parameters.txt
```

Prioritize by function:
- id
- user
- account
- file
- path
- url
- redirect
- return
- next
- callback
- search
- query
- page
- sort
- filter
- format
- export
- download

These are investigation candidates, not vulnerabilities.

### Arjun

For an authorized target:

```bash
arjun -u https://example.com/
```

Always verify current syntax:

```bash
arjun -h
```

Use selectively. Do not generate unnecessary high-volume traffic.

---

## Phase 19 — Cloud Reconnaissance

Identify cloud providers through:
- DNS
- CNAME
- TLS certificates
- HTTP headers
- Application assets
- Public docs
- Public code

Search DNS:

```bash
grep -Ei 'amazonaws|cloudfront|azure|google|gcp|cloudflare|fastly|akamai' raw/dns.txt
```

Search HTTP:

```bash
grep -Ei 'amazon|azure|google|cloudflare|fastly|akamai' normalized/httpx.json
```

Look for:
- Storage
- CDN
- Load balancers
- API gateways
- Serverless apps
- Container infrastructure
- CI/CD systems

Do not assume a cloud hostname belongs to the target. Verify ownership and scope.

---

## Phase 20 — GitHub and Public Code Reconnaissance

Look for:
- Organization repos
- Frontend code
- Mobile apps
- Infrastructure-as-code
- Documentation
- CI/CD config
- API definitions
- Historical repos

Search target domains:

```bash
grep -Rni -E 'example\.com|api\.example\.com' ./downloaded-repository/
```

Look for architecture:
- Frontend
- API
- Backend
- Storage
- Third-party services

Public code can expose relationships not visible from surface scanning.

Important:
- Do not use discovered credentials
- Do not test credentials you find
- Do not exfiltrate data
- Preserve minimal evidence
- Follow program disclosure rules

---

## Phase 21 — Organization OSINT

Collect:
- Legal name
- Brand names
- Domain names
- Subsidiaries
- Acquisitions
- Technology names
- Developer names
- Public repos
- Mobile apps
- Documentation domains
- Support domains
- Status pages

The goal is asset discovery.

Example:
```text
Company
  ↓
Brand
  ↓
Domain
  ↓
Subdomain
  ↓
Application
  ↓
API
  ↓
Cloud provider
```

OSINT should feed the attack surface map, not become a random data collection exercise.

---

## Phase 22 — Screenshots

Screenshot