# Session 7 — Password Management with Vaultwarden

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. This is the first genuinely sensitive service in the lab — pay attention to how the README handles that, not just the deployment steps.

## What I built

This week I added Vaultwarden — a self-hosted password manager, essentially a lightweight open-source implementation of the Bitwarden server. It runs as its own container alongside everything else deployed so far, and for now it's local-only, not exposed to the internet.

> 💡 **Why this matters as an example:** Notice the README states plainly that this stays local-only *for now*, rather than leaving that ambiguous. With a service this sensitive, being explicit about what's exposed and what isn't matters more than usual.

## Why this one matters

This is not:
> "Installed a password manager."

This is actually about:
- secrets management — how sensitive data gets stored and protected, not just where it lives
- why HTTPS actually matters here specifically, not just as a general best practice
- backup thinking — if this breaks, passwords shouldn't disappear along with the container
- the real risk of exposing something like this publicly without doing it properly first

## Deployment

Deployed via Docker Compose alongside the rest of the stack. Config lives in `docker-compose.yml` added here.

> 💡 **Why this matters as an example:** One volume here, but it's an important one — `/data` holds the entire encrypted vault. Losing it means losing everything stored inside, so this is a service where the volume backup actually matters, not just as a Docker best practice but practically.

## What I tested

- Opened `http://192.168.1.200:8083` and got the Vaultwarden login screen
- Created a test account using **test credentials only** — this is a training environment, not my actual vault
- Added a test vault entry to confirm it saves and persists
- Restarted the container and confirmed the entry was still there afterward — the volume doing its job# Session 7 — Password Management with Vaultwarden

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. This is the first genuinely sensitive service in the lab — pay attention to how the README handles that, not just the deployment steps.

## What I built

This week I added Vaultwarden — a self-hosted password manager, essentially a lightweight open-source implementation of the Bitwarden server. It runs as its own container alongside everything else deployed so far, and for now it's local-only, not exposed to the internet.

> 💡 **Why this matters as an example:** Notice the README states plainly that this stays local-only *for now*, rather than leaving that ambiguous. With a service this sensitive, being explicit about what's exposed and what isn't matters more than usual.

## Why this one matters

This is not:
> "Installed a password manager."

This is actually about:
- secrets management — how sensitive data gets stored and protected, not just where it lives
- why HTTPS actually matters here specifically, not just as a general best practice
- backup thinking — if this breaks, passwords shouldn't disappear along with the container
- the real risk of exposing something like this publicly without doing it properly first

## Deployment

Deployed via Docker Compose alongside the rest of the stack. Config lives in `docker-compose-files/vaultwarden/`.

```yaml
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    ports:
      - "8083:80"
    volumes:
      - ./vw-data:/data
```

> 💡 **Why this matters as an example:** One volume here, but it's an important one — `/data` holds the entire encrypted vault. Losing it means losing everything stored inside, so this is a service where the volume backup actually matters, not just as a Docker best practice but practically.

## What I tested

- Opened `http://192.168.1.200:8083` and got the Vaultwarden login screen
- Created a test account using **test credentials only** — this is a training environment, not my actual vault
- Added a test vault entry to confirm it saves and persists
- Restarted the container and confirmed the entry was still there afterward — the volume doing its job

> 💡 **Why this matters as an example:** That last bullet matters more here than in earlier sessions, as passwords and secrets are of critical importance we absolutely want to make sure they can survive a crash.

## One problem I hit and how I fixed it

[Fill this in with your actual issue — e.g.]

First attempt, the container kept restarting. Checking the logs showed it couldn't write to its data directory — the volume path didn't exist yet on the host, so Vaultwarden had nothing to write to. Created the folder manually before bringing the stack back up, and it started cleanly after that.

> 💡 **Why this matters as an example:** This is the section students often skip writing as unimportant, and yet it's the one that matters most. A perfect setup with no notes shows nothing. A mistake explained clearly shows you can actually troubleshoot and solve problems — and that's what the job is most often about.

## Screenshots

- Vaultwarden login screen
- Test vault with an entry saved
- `docker ps` showing the container running
- Volume folder on the host showing persisted data

## Notes for later

This one stays local-only for now. Exposing it publicly is a Session 9–10 decision, once reverse proxy and HTTPS are actually in place — not before.

## Next up

Session 8 — Monitoring with Uptime Kuma.
