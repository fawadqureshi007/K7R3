<p align="center">
  <img src="k7r3.jpeg" alt="K7R3 Logo" width="300">
</p>

<h1 align="center">K7R3</h1>

<p align="center">
  Practical Reconnaissance & Bug Bounty Field Guide
</p>


### Practical Reconnaissance & Bug Bounty Field Guide

> **Find the surface. Map the application. Follow the data. Verify the lead.**

K7R3 is a practical reconnaissance methodology for security researchers, bug bounty hunters, penetration testers, red teamers, and students working in authorized environments.

---

## ⚡ What K7R3 Covers

- Passive reconnaissance
- Subdomain enumeration
- DNS enumeration
- HTTP probing
- Technology fingerprinting
- WAF identification
- Port scanning
- Service enumeration
- TLS inspection
- Web crawling
- Historical URL discovery
- JavaScript analysis
- API discovery
- Parameter discovery
- Content discovery
- Public source-code inspection
- Automated security checks
- Manual HTTP inspection

---

## ⚠️ Scope

Use K7R3 only against:

- Assets explicitly included in a bug bounty program
- Systems you own
- Authorized penetration-testing engagements
- Red-team environments where you have permission
- CTFs and security labs

> A discovered domain, IP, endpoint, or service is **not automatically in scope**.

Always follow the target's rules, rate limits, and testing restrictions.

---

# 🚀 Quick Start

## Set Your Target

```bash
export TARGET=example.com
````

 ## Create Workspace

```
mkdir -p K7R3/{output,data/raw,data/screenshots,logs}
cd K7R3
```

 ## 1\. Find Subdomains

```
subfinder -d "$TARGET" -silent -o output/subfinder.txt
```

 **Purpose:** Passive subdomain discovery.

 ## 2\. Certificate Transparency

```
curl -s "https://crt.sh/?q=%25.$TARGET&output=json" \
| jq -r '.[].name_value' \
| tr '\r' '\n' \
| sort -u > output/crtsh.txt
```

 **Purpose:** Find hostnames that have appeared in TLS certificates.

 ## 3\. Combine Results

```
cat output/subfinder.txt output/crtsh.txt \
| sort -u > output/subdomains.txt
```

 **Purpose:** Create one clean subdomain list.

 ## 4\. Resolve Hosts

```
dnsx -l output/subdomains.txt -silent -a -resp -o output/dns.txt
```

 **Purpose:** Resolve discovered hostnames and identify IP addresses.

 ## 5\. Find Live Web Applications

```
httpx -l output/subdomains.txt \
-silent \
-status-code \
-title \
-tech-detect \
-web-server \
-follow-redirects \
-o output/live.txt
```

 **Purpose:** Identify reachable HTTP/HTTPS services.

 ## 6\. Extract Live URLs

```
awk '{print $1}' output/live.txt | sort -u > output/live-urls.txt
```

 ## 7\. Crawl Applications

```
katana -list output/live-urls.txt \
-silent \
-o output/endpoints.txt
```

 **Purpose:** Discover links, endpoints, forms, and JavaScript references.

 ## 8\. Historical URLs

```
gau --subs "$TARGET" | sort -u > output/gau.txt

waybackurls "$TARGET" | sort -u > output/wayback.txt
```

 **Purpose:** Find previously exposed URLs and functionality.

 ## 9\. Build Final URL List

```
cat output/endpoints.txt \
output/gau.txt \
output/wayback.txt \
| sort -u > output/urls.txt
```

---

 # 🛠️ Installation

 ## System Packages

```
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
ffuf \
gobuster \
whatweb \
wafw00f \
ca-certificates
```

 ## ProjectDiscovery

```
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest

go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest

go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest

go install -v github.com/projectdiscovery/naabu/v2/cmd/naabu@latest

go install -v github.com/projectdiscovery/katana/cmd/katana@latest

go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
```

 ## URL Collection

```
go install github.com/lc/gau/v2/cmd/gau@latest

go install github.com/tomnomnom/waybackurls@latest
```

 ## PATH

```
export PATH="$PATH:$HOME/go/bin"
```

 Permanent:

```
echo 'export PATH="$PATH:$HOME/go/bin"' >> ~/.bashrc
source ~/.bashrc
```

---

 # 🔎 1. Subdomain Discovery

 ## Subfinder

 ### Basic

```
subfinder -d example.com -silent
```

 ### Save Results

```
subfinder -d example.com \
-silent \
-o subdomains.txt
```

 ### Multiple Domains

```
subfinder -dL domains.txt \
-silent \
-o subdomains.txt
```

 ### All Sources

```
subfinder -d example.com \
-all \
-silent \
-o subdomains-all.txt
```

 ### JSON

```
subfinder -d example.com \
-json \
-o subdomains.json
```

---

 # 🌐 2. Certificate Transparency

```
curl -s 'https://crt.sh/?q=%25.example.com&output=json' \
| jq -r '.[].name_value' \
| tr '\r' '\n' \
| sort -u
```

 Save:

```
curl -s 'https://crt.sh/?q=%25.example.com&output=json' \
| jq -r '.[].name_value' \
| tr '\r' '\n' \
| sort -u > crtsh.txt
```

 **Use it for:** Certificate transparency hostname discovery.

---

 # 🗺️ 3. Amass

```
amass enum -passive -d example.com
```

 Save:

```
amass enum -passive \
-d example.com \
-o amass.txt
```

 Source information:

```
amass enum -passive \
-v \
-src \
-d example.com \
-o amass-sources.txt
```

---

 # 🧬 4. DNS Enumeration

 ## Basic

```
dnsx -l subdomains.txt -silent
```

 ## A Records

```
dnsx -l subdomains.txt \
-a \
-resp \
-o dns-a.txt
```

 ## CNAME

```
dnsx -l subdomains.txt \
-cname \
-resp \
-o dns-cname.txt
```

 ## MX

```
dnsx -l subdomains.txt \
-mx \
-resp \
-o dns-mx.txt
```

 ## TXT

```
dnsx -l subdomains.txt \
-txt \
-resp \
-o dns-txt.txt
```

 ## Common Records

```
dnsx -l subdomains.txt \
-a \
-aaaa \
-cname \
-mx \
-ns \
-txt \
-resp \
-o dns.txt
```

---

 # 🌍 5. Live Web Hosts

 ## HTTPx

```
httpx -l subdomains.txt -silent
```

 Status and title:

```
httpx -l subdomains.txt \
-silent \
-status-code \
-title
```

 Technology detection:

```
httpx -l subdomains.txt \
-silent \
-status-code \
-title \
-tech-detect \
-web-server \
-follow-redirects
```

 Save:

```
httpx -l subdomains.txt \
-silent \
-status-code \
-title \
-tech-detect \
-web-server \
-follow-redirects \
-o live.txt
```

 JSON:

```
httpx -l subdomains.txt \
-silent \
-json \
-o httpx.json
```

---

 # 🧰 6. Technology Fingerprinting

 ## WhatWeb

```
whatweb https://example.com
```

 Verbose:

```
whatweb -v https://example.com
```

 Aggressive:

```
whatweb -a 3 https://example.com
```

 Multiple targets:

```
whatweb -i live-urls.txt
```

---

 # 🛡️ 7. WAF Detection

```
wafw00f https://example.com
```

 Multiple targets:

```
wafw00f -i live-urls.txt
```

 > WAF detection is reconnaissance information. Do not attempt bypasses unless explicitly authorized.

---

 # 🔌 8. Port Discovery

 ## Naabu

 Top 100:

```
naabu -list ips.txt \
-top-ports 100 \
-silent \
-o ports.txt
```

 Top 1000:

```
naabu -list ips.txt \
-top-ports 1000 \
-silent \
-o top-ports.txt
```

 Web ports:

```
naabu -list ips.txt \
-p 80,443,8000,8080,8081,8443,8888 \
-silent \
-o web-ports.txt
```

 Full range:

```
naabu -list ips.txt \
-p 1-65535 \
-silent \
-o all-ports.txt
```

---

 # 🔬 9. Service Enumeration

 ## Nmap

 Version detection:

```
nmap -sV 203.0.113.10
```

 Default scripts:

```
nmap -sC -sV 203.0.113.10
```

 All ports:

```
nmap -p- -sV 203.0.113.10
```

 Web ports:

```
nmap -p 80,443,8080,8443 \
-sV \
203.0.113.10
```

 Save:

```
nmap -oA nmap-result 203.0.113.10
```

 HTTP:

```
nmap \
--script http-title,http-headers \
-p 80,443 \
203.0.113.10
```

 TLS:

```
nmap \
--script ssl-cert,ssl-enum-ciphers \
-p 443 \
203.0.113.10
```

---

 # 🔐 10. TLS Inspection

 ## SSLScan

```
sslscan example.com:443
```

 ## OpenSSL

```
openssl s_client \
-connect example.com:443 \
-servername example.com </dev/null
```

 Certificate information:

```
openssl s_client \
-connect example.com:443 \
-servername example.com </dev/null 2>/dev/null \
| openssl x509 \
-noout \
-subject \
-issuer \
-dates \
-ext subjectAltName
```

---

 # 🕷️ 11. Web Crawling

 ## Katana

 Basic:

```
katana \
-u https://example.com \
-silent \
-o katana.txt
```

 Multiple targets:

```
katana \
-list live-urls.txt \
-silent \
-o endpoints.txt
```

 Depth:

```
katana \
-u https://example.com \
-depth 3 \
-silent
```

 JavaScript:

```
katana \
-list live-urls.txt \
-jc \
-silent \
-o js-endpoints.txt
```

 Known files:

```
katana \
-u https://example.com \
-known-files all \
-silent
```

---

 # 🕰️ 12. Historical URLs

 ## GAU

```
gau example.com \
| sort -u \
> gau.txt
```

 Subdomains:

```
gau --subs example.com \
| sort -u \
> gau-subs.txt
```

 Multiple providers:

```
gau \
--providers wayback,commoncrawl,otx,urlscan \
example.com \
| sort -u \
> gau-all.txt
```

 ## Waybackurls

```
waybackurls example.com \
| sort -u \
> wayback.txt
```

 Multiple subdomains:

```
cat subdomains.txt \
| waybackurls \
| sort -u \
> historical.txt
```

---

 # 🧹 13. URL Filtering

 ## APIs

```
grep -Ei \
'/(api|api/v[0-9]+|graphql|swagger|openapi|admin|internal|debug|login|oauth|upload|download|export)' \
urls.txt \
| sort -u \
> interesting.txt
```

 ## Interesting Files

```
grep -Ei \
'\.(js|json|map|xml|txt|conf|config|bak|old|zip)([?#]|$)' \
urls.txt \
| sort -u \
> interesting-files.txt
```

 ## Parameters

```
grep -E '\?.+=' urls.txt \
| sort -u \
> parameterized.txt
```

---

 # 📜 14. JavaScript Recon

 Find JavaScript:

```
grep -Ei '\.js([?#]|$)' urls.txt \
| sort -u \
> javascript.txt
```

 Create directory:

```
mkdir -p data/raw/js
```

 Download JavaScript:

```
while read -r url; do
    name=$(printf '%s' "$url" \
    | sha256sum \
    | cut -d' ' -f1)

    curl -ksSL \
    --max-time 15 \
    "$url" \
    -o "data/raw/js/$name.js"
done < javascript.txt
```

 Search references:

```
grep -RniE \
'api|graphql|swagger|openapi|admin|internal|oauth|upload|download' \
data/raw/js/ \
| head -n 500
```

 Source maps:

```
grep -Rni \
'sourceMappingURL' \
data/raw/js/
```

 External hosts:

```
grep -RhoE \
'https?://[^" ]+' \
data/raw/js/ \
| sort -u \
> js-hosts.txt
```

---

 # 🗺️ 15. Source Maps

 Find maps:

```
grep -Ei \
'\.map([?#]|$)' \
urls.txt \
| sort -u \
> source-maps.txt
```

 Inspect references:

```
grep -Rni \
'sourceMappingURL' \
data/raw/js/
```

 Download:

```
curl -ksSL \
https://example.com/app.js.map \
-o app.js.map
```

 Source filenames:

```
jq '.sources' app.js.map
```

 Embedded source:

```
jq '.sourcesContent[]' \
app.js.map \
2>/dev/null
```

---

 # 🔗 16. API Discovery

 Check common documentation:

```
for p in \
swagger.json \
openapi.json \
api-docs \
swagger/v1/swagger.json \
api/swagger.json \
api/openapi.json
do
    curl -sk \
    -o /dev/null \
    -w "%{http_code} %{url_effective}\n" \
    "https://example.com/$p"
done
```

 OpenAPI:

```
curl -sk \
https://example.com/openapi.json \
| jq '.'
```

 Swagger:

```
curl -sk \
https://example.com/swagger.json \
| jq '.'
```

 GraphQL:

```
curl -sk \
-i \
https://example.com/graphql
```

 > Only perform deeper API testing or introspection when permitted by the target.

---

 # 🎛️ 17. Parameter Discovery

 Extract parameters:

```
grep '?' urls.txt \
| sed 's/^[^?]*?//' \
| tr '&' '\n' \
| cut -d= -f1 \
| sort -u \
> parameters.txt
```

 Arjun:

```
arjun \
-u https://example.com/search
```

 GET:

```
arjun \
-u https://example.com/search \
-m GET
```

 List:

```
arjun \
-i parameterized.txt \
-oT arjun.txt
```

---

 # 📁 18. Content Discovery

 ## FFUF

 Basic:

```
ffuf \
-u https://example.com/FUZZ \
-w /path/to/wordlist.txt \
-mc 200,204,301,302,307,401,403
```

 Extensions:

```
ffuf \
-u https://example.com/FUZZ \
-w /path/to/wordlist.txt \
-e .js,.json,.txt,.xml,.bak \
-mc 200,204,301,302,307,401,403
```

 Filter 404:

```
ffuf \
-u https://example.com/FUZZ \
-w /path/to/wordlist.txt \
-fc 404 \
-rate 50
```

 JSON:

```
ffuf \
-u https://example.com/FUZZ \
-w /path/to/wordlist.txt \
-o ffuf.json \
-of json
```

---

 ## Feroxbuster

```
feroxbuster \
-u https://example.com \
-w /path/to/wordlist.txt
```

 Extensions:

```
feroxbuster \
-u https://example.com \
-w /path/to/wordlist.txt \
-x js,json,txt,xml,bak
```

 Lower concurrency:

```
feroxbuster \
-u https://example.com \
-w /path/to/wordlist.txt \
-t 10
```

---

 ## Gobuster

 Directory:

```
gobuster dir \
-u https://example.com \
-w /path/to/wordlist.txt
```

 Extensions:

```
gobuster dir \
-u https://example.com \
-w /path/to/wordlist.txt \
-x js,json,txt,php
```

 DNS:

```
gobuster dns \
-d example.com \
-w /path/to/subdomains.txt
```

 Virtual hosts:

```
gobuster vhost \
-u https://example.com \
-w /path/to/vhosts.txt
```

---

 # 🧪 19. Automated Checks

 ## Nuclei

```
nuclei \
-l live-urls.txt \
-tags misconfig,exposure,tech \
-rate-limit 5 \
-concurrency 5 \
-o nuclei.txt
```

 Single target:

```
nuclei \
-u https://example.com \
-tags misconfig,exposure,tech \
-rate-limit 2
```

 JSONL:

```
nuclei \
-l live-urls.txt \
-jsonl \
-o nuclei.jsonl
```

 List templates:

```
nuclei -tl
```

 > Keep scans within the target's permitted rate and template restrictions.

---

 # 🧰 20. Manual HTTP Inspection

 ## Headers

```
curl -skI https://example.com/
```

 ## Redirects

```
curl -skIL https://example.com/
```

 ## Save Headers and Body

```
curl -sk \
-D headers.txt \
-o body.html \
https://example.com/
```

 ## OPTIONS

```
curl -sk \
-X OPTIONS \
-i \
https://example.com/
```

 ## robots.txt

```
curl -sk \
https://example.com/robots.txt
```

 ## sitemap.xml

```
curl -sk \
https://example.com/sitemap.xml
```

 ## security.txt

```
curl -sk \
https://example.com/.well-known/security.txt
```

---

 # 🍪 21. Cookie Inspection

 Inspect cookies:

```
curl -skI \
https://example.com/login \
| grep -i '^set-cookie:'
```

 Save cookies:

```
curl -sk \
-c cookies.txt \
https://example.com/login \
-o /dev/null
```

 Reuse permitted session:

```
curl -sk \
-b cookies.txt \
https://example.com/ \
-o /dev/null
```

 > Never attempt to obtain or reuse another person's session.

---

 # 🧑‍💻 22. Public Source-Code Recon

 Clone:

```
git clone https://github.com/ORG/REPO.git
```

 History:

```
git -C REPO log --all --oneline
```

 Historical files:

```
git -C REPO log \
--all \
--name-only \
--pretty=format: \
| sort -u
```

 Branches:

```
git -C REPO branch -a
```

 Remotes:

```
git -C REPO remote -v
```

 Search history:

```
git -C REPO grep \
-nEi \
'api[_-]?key|secret|token|authorization' \
$(git -C REPO rev-list --all) \
2>/dev/null \
| head -n 200
```

 > Only investigate intentionally public repositories and authorized research targets. Do not use discovered credentials or tokens unless explicitly authorized.

---

 # 🔬 23. Service-Specific Enumeration

 ## FTP

```
nmap \
-p21 \
-sV \
--script ftp-anon,ftp-syst \
203.0.113.10
```

 ## SSH

```
nmap \
-p22 \
-sV \
--script ssh2-enum-algos,ssh-hostkey \
203.0.113.10
```

 ## SMTP

```
nmap \
-p25,465,587 \
-sV \
--script smtp-commands \
203.0.113.10
```

 ## DNS

```
nmap \
-p53 \
-sV \
--script dns-recursion,dns-service-discovery \
203.0.113.10
```

 ## Redis

```
nmap \
-p6379 \
-sV \
203.0.113.10
```

 ## MongoDB

```
nmap \
-p27017 \
-sV \
203.0.113.10
```

 ## MySQL / PostgreSQL

```
nmap \
-p3306,5432 \
-sV \
203.0.113.10
```

---

 # 🎯 Targeted Hunting

 ## API

```
grep -Ei \
'api|graphql|swagger|openapi' \
urls.txt \
| sort -u \
> api.txt
```

 ## Admin

```
grep -Ei \
'admin|administrator|dashboard|manage|panel' \
urls.txt \
| sort -u \
> admin.txt
```

 ## Authentication

```
grep -Ei \
'login|signin|signup|register|oauth|sso|auth|session' \
urls.txt \
| sort -u \
> auth.txt
```

 ## Upload / Download

```
grep -Ei \
'upload|download|file|attachment|import|export' \
urls.txt \
| sort -u \
> files.txt
```

 ## Debug / Internal

```
grep -Ei \
'debug|internal|test|staging|dev|development' \
urls.txt \
| sort -u \
> internal.txt
```

 ## JavaScript

```
grep -Ei \
'\.js([?#]|$)' \
urls.txt \
| sort -u \
> javascript.txt
```

 ## Parameters

```
grep -E '\?.+=' \
urls.txt \
| sort -u \
> parameterized.txt
```

---

 # ⚡ Top Commands Cheat Sheet

 ## Subdomains

```
subfinder -d example.com -silent -o subdomains.txt
```

 ## Certificate Discovery

```
curl -s 'https://crt.sh/?q=%25.example.com&output=json' \
| jq -r '.[].name_value' \
| sort -u
```

 ## DNS

```
dnsx -l subdomains.txt -silent -a -resp
```

 ## Live Hosts

```
httpx -l subdomains.txt -silent -status-code -title -tech-detect
```

 ## Ports

```
naabu -list ips.txt -top-ports 100 -silent
```

 ## Service Detection

```
nmap -sC -sV 203.0.113.10
```

 ## Crawl

```
katana -list live-urls.txt -silent
```

 ## Historical URLs

```
gau --subs example.com | sort -u
```

 ## JavaScript

```
grep -Ei '\.js([?#]|$)' urls.txt | sort -u
```

 ## API Paths

```
grep -Ei 'api|graphql|swagger|openapi' urls.txt | sort -u
```

 ## Parameters

```
grep -E '\?.+=' urls.txt | sort -u
```

 ## Content Discovery

```
ffuf \
-u https://example.com/FUZZ \
-w /path/to/wordlist.txt \
-mc 200,204,301,302,307,401,403
```

 ## Automated Checks

```
nuclei \
-l live-urls.txt \
-tags misconfig,exposure,tech \
-rate-limit 5
```

---

 # 🧠 Practical Workflow

```
# 1. Discover
subfinder -d example.com -silent -o subdomains.txt

# 2. Resolve
dnsx -l subdomains.txt -silent -a -resp -o dns.txt

# 3. Find live applications
httpx -l subdomains.txt \
-silent \
-status-code \
-title \
-tech-detect \
-follow-redirects \
-o live.txt

# 4. Extract live URLs
awk '{print $1}' live.txt | sort -u > live-urls.txt

# 5. Crawl
katana -list live-urls.txt \
-silent \
-o endpoints.txt

# 6. Historical discovery
gau --subs example.com | sort -u > gau.txt
waybackurls example.com | sort -u > wayback.txt

# 7. Combine
cat endpoints.txt gau.txt wayback.txt \
| sort -u > urls.txt

# 8. Find interesting areas
grep -Ei \
'api|graphql|admin|internal|debug|login|oauth|upload|download' \
urls.txt \
| sort -u \
> interesting.txt

# 9. Find parameters
grep -E '\?.+=' urls.txt \
| sort -u \
> parameterized.txt

# 10. JavaScript
grep -Ei '\.js([?#]|$)' urls.txt \
| sort -u \
> javascript.txt
```

 > **The goal is not to run every tool against everything.**
>
>  **The goal is to reduce the attack surface into smaller, more useful datasets.**

---

 # 📂 Recommended Project Structure

```
K7R3/
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
│   └── osint/
│
├── data/
│   ├── raw/
│   ├── normalized/
│   ├── screenshots/
│   └── reports/
│
├── output/
│
└── logs/
```

---

 # ⚙️ Example Scope Configuration

```
target:
  root_domains:
    - example.com

scope:
  include:
    - "*.example.com"
    - "example.com"

  exclude:
    - "status.example.com"
    - "thirdparty.example.com"

ports:
  - 80
  - 443
  - 8080
  - 8443
```

---

 # 📊 Useful Output Files

```
output/
├── subdomains.txt
├── crtsh.txt
├── dns.txt
├── live.txt
├── live-urls.txt
├── ports.txt
├── endpoints.txt
├── gau.txt
├── wayback.txt
├── urls.txt
├── interesting.txt
├── interesting-files.txt
├── parameterized.txt
├── javascript.txt
├── js-hosts.txt
├── source-maps.txt
├── api.txt
├── admin.txt
├── auth.txt
└── nuclei.txt
```

---

 # 🧭 K7R3 Methodology

```
DISCOVER
    ↓
ENUMERATE
    ↓
RESOLVE
    ↓
PROBE
    ↓
FINGERPRINT
    ↓
SCAN
    ↓
CRAWL
    ↓
COLLECT
    ↓
FILTER
    ↓
INVESTIGATE
    ↓
VALIDATE
```

 ### Discover

 Find domains, subdomains, certificates, and related infrastructure.

 ### Enumerate

 Resolve discovered hosts and identify their network relationships.

 ### Probe

 Determine which hosts expose HTTP/HTTPS services.

 ### Fingerprint

 Identify technologies, servers, frameworks, and WAFs.

 ### Scan

 Identify exposed ports and services where permitted.

 ### Crawl

 Map application routes, forms, links, and JavaScript.

 ### Collect

 Pull historical URLs and public references.

 ### Filter

 Separate APIs, parameters, authentication pages, files, admin paths, and other useful targets.

 ### Investigate

 Manually understand the application and its behavior.

 ### Validate

 Verify security findings carefully and within the target's rules.

---

 # 🔥 Philosophy

 > **Good reconnaissance is not about collecting the most data. It's about finding the data that leads somewhere.**

```
Large Target
     ↓
Subdomains
     ↓
Live Assets
     ↓
Technologies
     ↓
Endpoints
     ↓
Parameters
     ↓
Interesting Functionality
     ↓
Manual Investigation
```

 Every stage should make the next stage smaller and more useful.

---

 # 🤝 Contributing

 Contributions are welcome.

 Useful contributions include:

 - New practical reconnaissance modules
- Better command examples
- Improved automation
- Bug fixes
- Scope-aware workflows
- Documentation improvements
- Useful wordlist recommendations
- Performance improvements

 Please keep contributions practical and focused.

---

 # 📜 License

 See `LICENSE` for the repository license.

---

 # ⭐ Support K7R3

 If this methodology helps your research:

 - ⭐ Star the repository
- 🐛 Report issues
- 💡 Suggest improvements
- 🔧 Contribute useful modules
- 📚 Share practical reconnaissance techniques

---

 # K7R3

 > **Recon smarter. Reduce the surface. Follow the data.**


