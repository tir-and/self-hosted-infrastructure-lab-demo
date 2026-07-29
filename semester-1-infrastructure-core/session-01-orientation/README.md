# Session 1 — Orientation and Portfolio Setup

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. Session 1 has no technical build — so unlike later sessions, this README is mostly about setting expectations and getting the repo itself in order. That's still worth documenting properly.

## What I built

Nothing technical yet, this week was about understanding what I'm actually building over the next ten sessions, not installing anything. What I did produce: this GitHub repo, a first-pass architecture diagram of the finished lab, and a lab journal template I'll use going forward.

> 💡 **Why this matters as an example:** Notice the README just says plainly what this session actually was — orientation — and documents that honestly.

## Why this one matters

This is not:
> "Watched an intro video about self-hosting."

This is actually about:
- understanding the full picture before touching a terminal — what's being built, and why, layer by layer
- the "growing environment" mindset — every future session adds one piece to the same system, nothing gets built in isolation
- setting up the portfolio habit from day one, not bolting it on at the end
- a local-first, safety-first mindset — nothing gets exposed to the internet until Session 9–10, and only deliberately

## What I set up

- Created a public GitHub repo called `self-hosted-lab`
- Added the initial folder structure — an `architecture-diagrams/` folder at the root, a `troubleshooting-notes.md` file at the root (one running log for the whole programme), and a session folder per week (each with its own `README.md`, and later a `docker-compose.yml` and `screenshots/` once there's something to build)
- Drew a first-pass architecture diagram showing the full stack I'll build toward — VM → Docker → services → remote access
- Started a lab journal as `troubleshooting-notes.md` to track what I build and what breaks, session by session

> 💡 **Why this matters as an example:** The repo structure you set up this week is the one you'll be living in for the rest of the programme. Getting the folder names right now — the same names used every session — saves you from renaming things halfway through and losing history.

## The architecture I'm working toward

```
Ubuntu Server (VM)
└── Docker
      ├── Pi-hole              ← DNS filtering
      ├── File Browser         ← Private file storage
      ├── Immich                ← Private photo backup
      ├── Vaultwarden           ← Password manager
      ├── Uptime Kuma           ← Monitoring dashboard
      ├── Nginx Proxy Manager  ← HTTPS and clean URLs
      │
      └── Cloudflare Tunnel     ← Secure remote access
```

This is the destination, not this week's build. Every session from here adds one layer on top of the one before it.

## One problem I hit and how I fixed it

[Fill this in with your actual issue — e.g.]

Honestly, no technical problems this week since nothing was deployed. 

> 💡 **Why this matters as an example:** Even a quiet session is worth a note here. Not every week has a dramatic bug to write up — sometimes the honest entry is "nothing broke, but here's what I second-guessed." That's still useful documentation.

## Screenshots

- GitHub repo showing the initial folder structure
- First-pass architecture diagram

## Next up

Session 2 — Linux Server, SSH & Basic Administration. Homework: install VirtualBox, download the Ubuntu Server ISO, and make sure the `self-hosted-lab` repo exists and is public.
