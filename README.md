# Self-Hosted Lab — Demo Repository

> 💡 **Note for students:** This is the instructor's demo repo for the *Practical Self-Hosted Infrastructure* programme. It exists so you can see what a real, well-documented build looks like — not a polished tutorial, but an actual working lab with real screenshots and real mistakes written down. Your own repo should follow the same structure, but everything in it should be your own work, in your own words.

## What this is

This repo documents one self-hosted infrastructure lab, built session by session over two semesters. Everything here runs on a single Ubuntu Server VM, with each week adding one more layer — a new service, a new concept, a new piece of the stack. Nothing is installed in isolation. By the end, it's one growing system, not ten disconnected experiments.

That's intentional, not incidental. The whole point of this programme is to leave you with something you actually built and can talk about — not a certificate, not a checklist, but a real deployed environment you can walk someone through in an interview.

## The programme

**Semester 1 — Infrastructure Lab Core**
For students focused on networking and infrastructure. Covers Linux, Docker, DNS filtering, file services, photo backup, password management, monitoring, reverse proxy, HTTPS, and remote access.

**Semester 2 — Security Lab Advanced**
For students continuing into cybersecurity. Builds directly on top of the same environment from Semester 1 — hardening, backups, access control, intrusion prevention, and SIEM monitoring.

You can stop after Semester 1 with a complete, portfolio-ready lab. Semester 2 isn't a new project — it's the same infrastructure, secured and monitored.

## Repo structure

```
self-hosted-lab-demo/
├── README.md                          ← you are here
├── architecture-diagrams/             ← evolving diagrams, one per session
├── troubleshooting-notes.md           ← one running log across the whole programme
├── semester-1-infrastructure-core/
│   ├── session-01-orientation/
│   ├── session-02-linux-server/
│   ├── session-03-docker/
│   ├── session-04-pihole/
│   ├── session-05-file-browser/
│   ├── session-06-immich/
│   ├── session-07-vaultwarden/
│   ├── session-08-uptime-kuma/
│   ├── session-09-nginx-proxy-manager/
│   └── session-10-remote-access-portfolio/
└── semester-2-security-advanced/
    ├── session-01-hardening-backups/
    ├── session-02-authelia/
    ├── session-03-crowdsec/
    ├── session-04-wazuh/
    ├── bonus-vulnerability-scanning-openvas/
    └── bonus-nextcloud/
```

Each session folder contains its own `README.md` — what was built, what was tested, one problem that came up and how it got fixed, and a list of the screenshots that go with it. Most sessions from Session 3 onward also include the actual `docker-compose.yml` used that week.

> 💡 **Why this structure matters:** Notice that troubleshooting notes live in one file at the root, not scattered across session folders. That's deliberate — it reads as one continuous story of things going wrong and getting fixed, which is closer to how real infrastructure work actually looks.

## How to use this if you're a student

1. Read through a session folder here *before* you build that week's session yourself — it'll show you the level of detail expected.
2. Don't copy the content. Copy the shape — the section headers, the honesty about what broke, the specific commands and outputs.
3. Your own repo should be called `self-hosted-lab`. This one, `self-hosted-lab-demo`, is the instructor's reference copy only.

## The concepts, not the tools

None of the tools in this stack are the actual point. Pi-hole teaches DNS and network filtering. Vaultwarden teaches secrets management and backup thinking. Uptime Kuma teaches operational monitoring. Every service here is a vehicle for a concept that transfers to any real job — the tool itself is almost incidental.

## Status

This repo is updated live as the programme runs. If a session folder looks thin or is missing, that session probably hasn't happened yet.
