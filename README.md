# Wallarm Solutions Engineer Technical Evaluation For Luis Guzman

## 🎯 Objectives

AT the end of this evaluation, I was able to:

✅ Deploy a Wallarm filtering node using a Docker  
✅ Configure a backend origin to receive test traffic. (httpbin.org) 
✅ Use the **GoTestWAF** attack simulation tool to generate traffic (GoLang)
✅ Document the deployment and troubleshooting process.  
✅ Demonstrate proficiency in using **Wallarm's official documentation**.  

---

## 🚀 Task Breakdown

# Wallarm Filtering Node + GoTestWAF Evaluation (MacBook Pro)

This project demonstrates how to run a local Wallarm filtering node on macOS using Docker and evaluate it with GoTestWAF against a backend origin (httpbin.org).

## 🔧 Requirements

- Docker (tested with `Docker version 28.3.0`)
- Go (tested with `go version go1.24.5 darwin/arm64`)
- Homebrew (used to install CLI tools)

---

## Setup Instructions 

### 1. Install Required Tools
```bash
xcode-select --install          # Install command line tools
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install go                 # Install Go
brew install docker             # If Docker not installed
```

### 2. Clone the GoTestWAF Repository
```bash
git clone https://github.com/wallarm/gotestwaf.git
cd gotestwaf
go build                        # Compile the CLI binary
```

---

## Deploy Wallarm Filtering Node (Locally via Docker)

> Followed Wallarm docs: https://docs.wallarm.com/installation/inline/compute-instances/docker/nginx-based/

1. **Obtain your Wallarm API token**
   - Log into [Wallarm Console](https://us1.my.wallarm.com/)
   - Go to *Settings → API Tokens* and generate one with `Administrator` permissions

2. **Run Wallarm Node Docker container**
```bash
docker run -d \
  -e WALLARM_API_TOKEN='PASTE_YOUR_TOKEN' \
  -e WALLARM_LABELS='group=macbook-local' \
  -e NGINX_BACKEND='http://host.docker.internal:8081' \
  -e WALLARM_API_HOST='us1.api.wallarm.com' \
  -p 8080:80 \
  wallarm/node:latest
```

### 3. Deploy Backend Origin (httpbin.org)
```bash
docker run -d -p 8081:80 kennethreitz/httpbin
```

Ensure Wallarm is proxying requests to httpbin.org:
```bash
curl http://localhost:8080/get
```
---

## Run GoTestWAF Evaluation
```bash
./gotestwaf \
  --url http://localhost:8080 \
  --noEmailReport \
  --reportFormat html \
  --includePayloads
```

> HTML Report will be saved to: `./reports/waf-evaluation-report-<timestamp>.html`

---

## Results Summary (2025-07-12)

**True-Positive Score:** 662/666 blocked — `99.40%`

**True-Negative Score:** 46/47 passed — `97.87%`

**Overall Protection Accuracy:** `99.32%`

![Wallarm Local Setup](./docs/wallarm-local-diagram.png) <!-- Replace with actual PNG if available -->

---

## 🔍 Notes & Observations
- GraphQL and gRPC test categories showed 0% coverage — expected as httpbin does not support these protocols.
- Wallarm successfully intercepted and blocked all common OWASP injection categories (XSS, SQLi, LFI, RCE, etc.)
- Node was deployed locally and identified as a generic WAF

---

## 📂 Repo Structure
```bash
.
├── cmd
├── docs
├── internal
├── misc
├── pkg
├── reports         # HTML report saved here
├── testcases
├── tests
├── go.mod / go.sum
├── Dockerfile / Makefile / config.yaml
└── gotestwaf        # CLI binary
```

---

## 🧠 Author
Luis Guzmán — Solutions Engineer Candidate  
Test date: July 12, 2025

---

## ✅ Evaluation Criteria

Your submission will be evaluated based on:

📌 **Completeness**: Were all required tasks completed?  
📌 **Clarity**: Is the documentation clear and well-structured?  
📌 **Troubleshooting**: How well did you document and resolve any issues?  
📌 **Understanding of the Product**: Did you correctly set up and use the Wallarm filtering node?  
📌 **Use of Official Documentation**: Did you successfully leverage Wallarm's official resources?  




