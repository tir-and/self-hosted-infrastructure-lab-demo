# Session 6 — Photo Backup with Immich

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. This is the first *multi-container* service in the lab — notice the Docker Compose file has more than one service in it this time, and the README explains why.

## What I built

This week I deployed Immich — a self-hosted, Google Photos-style backup platform. Unlike everything else so far, Immich isn't a single container; it's a small stack of services working together — the app itself, a Postgres database, and Redis for caching. This is the first time in the lab that Docker Compose is doing real orchestration work, not just running one thing.

> 💡 **Why this matters as an example:** Notice the README calls out that this is a multi-container app *before* diving into the compose file. Setting that expectation up front makes the config below make sense, instead of the student wondering why there are suddenly three services instead of one.

## Why this one matters

This is not:
> "Installed a photo backup app."

This is actually about:
- multi-container applications — how real-world apps are rarely just one container, and how Compose manages several services as one unit
- databases in containers — Postgres storing Immich's metadata, and why that data needs its own persistent volume, separate from the actual photo files
- media storage — where uploaded photos actually live on disk, and how that's kept separate from the database
- Docker Compose dependencies — using `depends_on` so the app container waits for the database to be ready before it starts

## Deployment

Deployed via Docker Compose. Config lives in `docker-compose.yml`added here.

> 💡 **Why this matters as an example:** Notice there are two separate volumes doing two different jobs — `immich-upload` holds the actual photo files, `immich-db` holds Postgres's data (albums, faces, metadata, users). Losing one doesn't mean losing the other. That distinction matters a lot more here than it did with a single-container service.

## What I tested

- Ran `docker compose up -d` and watched all three containers come up in order — `immich-postgres` and `immich-redis` first, `immich-server` after
- Opened `http://192.168.1.200:2283` and completed first-run setup (admin account, test credentials only)
- Uploaded a handful of test photos through the web interface
- Restarted the whole stack with `docker compose down` and `docker compose up -d`, then confirmed the uploaded photos and the admin account were both still there afterward
- Checked `docker logs immich-server` to confirm it actually connected to Postgres and Redis rather than silently failing

> 💡 **Why this matters as an example:** Checking the logs to confirm the app actually connected to its database is worth doing explicitly here — with a multi-container stack, it's possible for the "main" container to start and look fine on the surface while quietly failing to talk to a dependency.

## One problem I hit and how I fixed it

[Fill this in with your actual issue — e.g.]

First attempt, `immich-server` kept restarting in a loop. Checking its logs showed it couldn't connect to Postgres — turned out `immich-postgres` hadn't finished initializing yet when `immich-server` tried to connect, even with `depends_on` set. `depends_on` controls start *order*, not whether a service is actually ready. Fixed it by waiting a bit longer and restarting just the `immich-server` container once Postgres had fully come up.

> 💡 **Why this matters as an example:** This is a genuinely common gotcha with Docker Compose — `depends_on` isn't a readiness check, just a start-order hint. Worth understanding clearly here since it'll matter again in later multi-container services.

## Screenshots

- Immich web interface showing uploaded test photos
- `docker compose ps` showing all three containers running
- `docker logs immich-server` showing a successful database connection
- Terminal output confirming photos persisted after `docker compose down` / `up`

## Next up

Session 7 — Password Management with Vaultwarden.
