# 🖥️ Home Lab — Self-Hosted Infrastructure

![Status](https://img.shields.io/badge/status-active-brightgreen)
![OS](https://img.shields.io/badge/OS-Ubuntu_24.04_LTS-orange)
![Runtime](https://img.shields.io/badge/runtime-Docker_Compose-blue)
![Programme](https://img.shields.io/badge/programme-Semester_1_In_Progress-yellow)

<!-- 💡 Update the badges above as you go — change "In Progress" to "Complete" when you finish a semester, and update the status badge if a service goes down. -->

A personal self-hosted infrastructure lab built as part of the **Practical Self-Hosted Infrastructure Programme**. The goal is to build, document, and progressively secure a real working environment using Linux, Docker, and open-source services — the kind you'd find in a small business or home office.

Every service added to this lab is documented here with architecture context, configuration notes, troubleshooting lessons, and screenshots.

> 💡 **What this lab demonstrates:** Real infrastructure skills — Linux administration, containerisation, DNS, reverse proxying, HTTPS, monitoring, and remote access — all running on a single Ubuntu Server VM.

---

## Architecture Overview

All services run as Docker containers on a single Ubuntu Server VM. The host machine connects via SSH. External access is handled through Cloudflare Tunnel — no ports are exposed directly to the internet.

```
Host Machine (Laptop)
  └── VirtualBox VM — Ubuntu Server 24.04 LTS (4GB RAM)
        └── Docker Compose
              ├── Pi-hole          (DNS + ad blocking)
              ├── File Browser     (private file access)
              ├── Immich           (photo backup)
              ├── Vaultwarden      (password management)
              ├── Uptime Kuma      (monitoring)
              └── Nginx Proxy Mgr  (reverse proxy + HTTPS)
                    └── Cloudflare Tunnel (remote access)
```

<!-- 💡 This diagram will only match your build once you've actually deployed each service. Delete the ones you haven't gotten to yet, or keep the full picture and just mark which ones are live in the table below. -->

Full architecture diagram: `architecture-diagrams/architecture-v1.png`

---

## Services Deployed

<!-- 💡 Update this table every session. Status options: ✅ Running · ⬜ Not started · 🔜 Planned (Semester 2) -->

| Service | Purpose | Status | Port | Notes |
|---|---|---|---|---|
| **Pi-hole** | DNS + ad blocking | ⬜ Not started | 53 / 80 | |
| **File Browser** | Private file access | ⬜ Not started | 8082 | |
| **Immich** | Photo backup | ⬜ Not started | 2283 | Multi-container (Postgres + Redis) |
| **Vaultwarden** | Password management | ⬜ Not started | 8083 | Local-only, not exposed externally |
| **Uptime Kuma** | Service monitoring | ⬜ Not started | 3001 | |
| **Nginx Proxy Manager** | Reverse proxy + HTTPS | ⬜ Not started | 81 (admin) | |
| **Wazuh** | SIEM / log monitoring | 🔜 Semester 2 | — | Planned |
| **CrowdSec** | Intrusion prevention | 🔜 Semester 2 | — | Planned |

---

## Repository Structure

```
self-hosted-lab/
├── configs/                  # Docker Compose files and .env templates
│   ├── pihole/
│   ├── immich/
│   └── nginx-proxy-manager/
├── diagrams/                 # Architecture diagrams (PNG)
├── docs/                     # Session logs and troubleshooting notes
│   ├── session-log-01.md
│   ├── session-log-04-pihole.md
│   └── troubleshooting.md
├── screenshots/              # Working service screenshots
└── README.md
```

---

## Session Logs

Each session has a corresponding doc in `/docs` covering what was built and what was learned.

<!-- 💡 Link them here as you go, e.g.:
- [Session 2 — Linux Server, SSH & Basic Administration](docs/session-log-02.md)
- [Session 3 — Docker and Docker Compose](docs/session-log-03.md)
-->

---

> 🔒 **Credential safety:** All `.env` files containing real passwords are listed in `.gitignore` and never committed. Only template files with placeholder values are in this repository.

---

## Environment

| Component | Detail |
|---|---|
| Host OS | *(Linux / Windows 11 / macOS — pick yours)* |
| Hypervisor | VirtualBox 7.0 |
| Guest OS | Ubuntu Server 24.04 LTS |
| VM RAM | 4 GB |
| VM Storage | 25 GB (dynamically allocated) |
| Container runtime | Docker 26 + Docker Compose v2 |
| Remote access | Cloudflare Tunnel (free tier) |

---

## What I Can Demonstrate

<!-- 💡 This section is what you'll actually say in an interview. Keep it honest — only list what you've actually done, and update it as you build more. -->

- SSH into the Ubuntu Server VM and navigate the file system
- Bring services up and down with `docker compose up -d` and `docker compose down`
- *(add more as you complete each session)*

---

*Built as part of the Practical Self-Hosted Infrastructure Programme · Semester 1: Infrastructure Lab Core*
