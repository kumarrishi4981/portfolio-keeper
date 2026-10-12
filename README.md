# ⚡ Portfolio Keeper — Automated Keep-Awake Sentinel

> A lightweight DevOps automation sentinel powered by **GitHub Actions** designed to eliminate cold starts and keep free-tier Render backend microservices warm, responsive, and ready for live recruiter and portfolio demonstrations.

[![Backend Sentinel](https://github.com/kumarrishi4981/portfolio-keeper/actions/workflows/keep-awake.yml/badge.svg)](https://github.com/kumarrishi4981/portfolio-keeper/actions/workflows/keep-awake.yml)
[![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-Automation-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)](https://github.com/features/actions)
[![Render](https://img.shields.io/badge/Render-Cloud_Hosting-46E3B7?style=for-the-badge&logo=render&logoColor=black)](https://render.com/)
[![Bash](https://img.shields.io/badge/Bash-Shell_Script-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white)](https://www.gnu.org/software/bash/)
[![cURL](https://img.shields.io/badge/cURL-HTTP_Client-073551?style=for-the-badge&logo=curl&logoColor=white)](https://curl.se/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)

---

## 📌 Problem & Motivation

Hosting modern full-stack portfolio applications across cloud platforms like **Render** provides robust containerized hosting. However, inactive free-tier web services automatically spin down after **15 minutes of zero inbound traffic**.

When a prospective employer, interviewer, or reviewer visits a live frontend on Vercel:
1. The frontend attempts to query a dormant Render backend.
2. The user faces a **50–60 second cold-start delay** or initial gateway timeout.
3. First impressions and perceived application speed suffer.

**Portfolio Keeper** solves this by maintaining a centralized GitHub Actions dispatcher that dispatches lightweight HTTP probes with custom timeout tolerances to spin up and health-check all deployed microservices.

---

## 🎯 Monitored Services & Endpoints

| Service | Architecture & Role | Production Endpoint | Health Path |
| :--- | :--- | :--- | :--- |
| **QuantTrade API** | Spring Boot Algorithmic Trading Engine | `https://quanttrade-api.onrender.com` | `/health` |
| **SoundForge API** | Node.js / Express / Socket.io Audio Hub | `https://soundforge-api.onrender.com` | `/health` |
| **MindFlow API** | Node.js / Gemini Vector Knowledge Graph | `https://mindflow-backend-tply.onrender.com` | `/health` |
| **CogniCare API** | Node.js / Express / GridFS Medical Companion | `https://cognicare-agpj.onrender.com` | `/api/health` |
| **FleetFlow Engine** | Spring Boot / Leaflet GIS Telematics Server | `https://fleetflow-fh0s.onrender.com` | `/health` |

---

## 🏗️ Architecture & Sentinel Flow

```text
┌─────────────────────────────────────────────────────────────────┐
│               GitHub Actions Workflow Trigger                   │
│   (Manual One-Click workflow_dispatch or Scheduled Cron Job)   │
└────────────────────────────────┬────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Ubuntu Runner Agent                         │
│   • Iterates through associative array of portfolio targets     │
│   • Dispatches cURL probes with 45s connection tolerance       │
│   • Reports HTTP status codes (200 OK / 301 / 404)              │
└────────┬──────────────┬──────────────┬──────────────┬───────────┘
         │              │              │              │
         ▼              ▼              ▼              ▼
   [QuantTrade]   [SoundForge]    [MindFlow]     [FleetFlow]   [CogniCare]
      Spring         Express        Express        Spring        Express
```

---

## ⚙️ Workflow Implementation

The sentinel script utilizes Bash associative arrays inside an ephemeral GitHub Actions runner:

```yaml
name: Wake Up All Backends (Manual)

on:
  workflow_dispatch:

jobs:
  ping-all-backends:
    runs-on: ubuntu-latest
    steps:
      - name: Ping All 5 Render Services
        run: |
          declare -A APPS=(
            ["QuantTrade"]="https://quanttrade-api.onrender.com/"
            ["SoundForge"]="https://soundforge-api.onrender.com/health"
            ["MindFlow"]="https://mindflow-backend-tply.onrender.com/health"
            ["CogniCare"]="https://cognicare-agpj.onrender.com/api/health"
            ["FleetFlow"]="https://fleetflow-fh0s.onrender.com/"
          )
          
          for name in "${!APPS[@]}"; do
            url="${APPS[$name]}"
            echo "Pinging $name -> $url"
            status=$(curl -s -o /dev/null -w "%{http_code}" --max-time 45 "$url")
            echo "  [$name] HTTP Status: $status"
          done
```

---

## 🚀 How to Trigger

### 1. Manual One-Click Dispatch via GitHub Web UI
1. Navigate to the **[Actions Tab](https://github.com/kumarrishi4981/portfolio-keeper/actions)**.
2. Select **Wake Up All Backends (Manual)** in the left sidebar.
3. Click **Run workflow** ➔ choose branch `main` ➔ confirm **Run workflow**.
4. In under 60 seconds, all 5 cloud instances will spin up and confirm `HTTP 200`.

### 2. Local Bash or Terminal Execution
Run the probe locally anytime before initiating a demonstration:
```bash
curl -I -s --max-time 45 https://quanttrade-api.onrender.com/
curl -I -s --max-time 45 https://soundforge-api.onrender.com/health
curl -I -s --max-time 45 https://mindflow-backend-tply.onrender.com/health
curl -I -s --max-time 45 https://cognicare-agpj.onrender.com/api/health
curl -I -s --max-time 45 https://fleetflow-fh0s.onrender.com/
```

---

## 👨‍💻 Author

**Rishi Kumar**
- **GitHub**: [@kumarrishi4981](https://github.com/kumarrishi4981)
- **Email**: [kumar.rishi4981@gmail.com](mailto:kumar.rishi4981@gmail.com)

---

## 📄 License

This repository is distributed under the MIT License.
