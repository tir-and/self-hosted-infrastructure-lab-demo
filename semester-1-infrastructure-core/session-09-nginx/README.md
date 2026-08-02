# Session 9 — Reverse Proxy and HTTPS with Nginx Proxy Manager

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. This session changes how every other service in the lab gets *reached* — pay attention to how that shift is explained, not just the Nginx Proxy Manager setup itself.

## What I built

This week I deployed Nginx Proxy Manager — a reverse proxy that sits in front of every other service in the lab and gives each one a clean hostname and real HTTPS, instead of everyone remembering a different `ip:port` for every service. From this point on, services are reached by name over HTTPS, not by port number.

I'm using a shared domain, `infralabs.uk`, with each student getting their own subdomain under it — mine is `tir.infralabs.uk`. This means real, valid HTTPS certificates from Let's Encrypt, without every student needing to buy their own domain.

> 💡 **Why this matters as an example:** Notice the README explains *why* a shared domain exists, not just how to configure it. Domain costs add up fast across a group of students — one instructor-owned domain with per-student subdomains is one way for avoiding that, and it's worth understanding the reasoning, not just copying the setup.

## Why this one matters

This is not:
> "Installed a reverse proxy."

This is actually about:
- reverse proxies — one entry point routing requests to the right backend service, instead of exposing every service's port directly
- hostnames — reaching a service by a real subdomain (`pihole.tir.infralabs.uk`) instead of `192.168.1.200:8081`
- HTTPS and certificates — how Let's Encrypt's DNS challenge automates getting one without needing the server itself to be reachable from the internet
- Docker networking — using a shared network so containers can talk to each other by name, the same pattern used in real production reverse proxy setups

## Docker networking — the `proxy` network

Every service that gets a proxy host needs to be on the same Docker network as Nginx Proxy Manager, so NPM can route to it by container name instead of by IP and host port. Created a shared external network once:

```
docker network create proxy
```

Then added `networks: [proxy]` to Nginx Proxy Manager and to every other service's compose file — Pi-hole, File Browser, Immich, Vaultwarden, Uptime Kuma. This is the production-standard pattern (the same one Traefik and most self-hosted community setups use), not just proxying through the VM's IP and a host port.

> 💡 **Why this matters as an example:** This is a real architectural decision, not just a config detail. Once services join the `proxy` network, they no longer need to publish their ports to the host at all — NPM reaches them internally. That's a meaningfully different (and more correct) pattern than what earlier sessions used, and it's worth understanding why the shift happens here specifically.

## Cloudflare — domain and API token

- Domain: `infralabs.uk`, registered via Porkbun, managed through Cloudflare DNS
- My subdomain: `tir.infralabs.uk` — every service I expose lives under this, e.g. `pihole.tir.infralabs.uk`, `kuma.tir.infralabs.uk`
- Used a **scoped Cloudflare API token**, limited to editing DNS records only for my own subdomain — not a global API key, and not access to the whole `infralabs.uk` zone

> 💡 **Why this matters as an example:** The scoped token is the security-relevant detail here. A token that could edit DNS for the *entire* domain would mean one compromised student credential puts every other student's subdomain at risk. Scoping it down to just your own subdomain limits the blast radius if a token ever leaks — worth understanding as a real access-control decision, not just a setup step.

## Deployment

Deployed via Docker Compose alongside the rest of the stack. The config lives in [`docker-compose.yml`](./docker-compose.yml) in this folder.

## What I set up

- Opened `http://192.168.1.200:81` and logged into the Nginx Proxy Manager admin panel, then changed the default admin credentials immediately (test credentials only)
- Added a **DNS Challenge** certificate request using my Cloudflare API token, rather than the default HTTP challenge — since the DNS challenge doesn't require the service to be publicly reachable yet, which matters because remote access isn't set up until next session
- Added a **Proxy Host** for each service, pointing at its container name on the `proxy` network rather than an IP and host port:
  - `pihole.tir.infralabs.uk` → `pihole:80`
  - `files.tir.infralabs.uk` → `filebrowser:80`
  - `photos.tir.infralabs.uk` → `immich-server:2283`
  - `vault.tir.infralabs.uk` → `vaultwarden:80`
  - `kuma.tir.infralabs.uk` → `uptime-kuma:3001`
- Confirmed each one loads over real HTTPS with a valid Let's Encrypt certificate, no browser warnings

> 💡 **Why this matters as an example:** Using the DNS challenge instead of the HTTP challenge is the key decision this session — it's what makes it possible to get a real, valid certificate for a service that isn't exposed to the public internet yet. That's a useful pattern beyond this lab too.

## Checking everything is running

Since services no longer publish ports directly to the host, `docker compose ps` inside one service's folder only shows that one stack. To see everything running across the whole VM:

```
docker ps
docker compose ls
```

`docker ps` shows every running container regardless of which folder it was started from. `docker compose ls` shows every Compose project (stack) currently running. Both are needed now that services are spread across separate compose files instead of one big one.

> 💡 **Why this matters as an example:** This is worth calling out explicitly — `docker compose ps` scoped to one folder used to be enough when everything was simpler, but it gives a false sense of "nothing else is running" once the lab has several independent stacks. Get in the habit of checking globally.

## One problem I hit and how I fixed it

[Fill this in with your actual issue — e.g.]

Nginx Proxy Manager wouldn't start — it kept failing with a port binding error on 80 and 443. Took a minute to figure out why, since I hadn't touched those ports in NPM's own config. Turned out Vaultwarden's standard Docker image ships with a bundled Caddy web server that also binds to ports 80 and 443 by default — something I'd missed back in Session 7 since Vaultwarden was running fine on its own at the time, with nothing else competing for those ports yet. The two containers were fighting over the same host ports the moment NPM tried to claim them.

Fixed it by adjusting Vaultwarden's port mapping so its bundled Caddy no longer touches 80/443 on the host, freeing those ports for NPM — which is the one service in this stack that actually needs them.

> 💡 **Why this matters as an example:** This is a genuinely sneaky one, because Vaultwarden worked completely fine for two whole sessions before this conflict showed up — the problem was dormant until a second service tried to claim the same ports. That's a good lesson on its own: a working setup today doesn't guarantee no conflicts later, especially with ports 80/443, which a lot of self-hosted services assume they can have to themselves.

## Screenshots

- Nginx Proxy Manager dashboard showing all five proxy hosts, each with a valid certificate
- Browser showing a service reached via its `*.tir.infralabs.uk` subdomain over HTTPS, no warnings
- Cloudflare DNS records showing the subdomains pointing correctly
- `docker network inspect proxy` showing all the connected containers
- Output of `docker ps` and `docker compose ls` showing the full picture across all stacks

## Next up

Session 10 — Remote Access and Portfolio Packaging, with Cloudflare Tunnel.
