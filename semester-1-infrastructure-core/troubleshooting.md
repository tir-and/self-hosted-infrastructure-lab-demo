# Troubleshooting Notes

Cumulative log of real problems hit while building and teaching this lab — instructor rehearsals and live sessions both. Single running file, not split by week.

## How to use this log

Each entry follows the same structure:

- **Symptom** — what I observed (error message, unexpected behaviour)
- **Service** — which container or system component was involved
- **Diagnosis** — how I figured out what was wrong
- **Fix** — what actually resolved it
- **Lesson** — what I'd check first next time

---

## Entries

### [Week 2] SSH unreachable — "No route to host"

**Service:** Ubuntu Server VM / networking
**Session:** 02

**Symptom:**
`ssh user@192.168.x.x` from the host returned `ssh: connect to host 192.168.x.x port 22: No route to host`. Ping also failed.

**Diagnosis:**
Root cause turned out to be a double-NAT/bridge setup on the host side — the host PC was connected via its own network bridge (`br0`) to a router that was itself a client to the ISP router. VirtualBox's Bridged Adapter couldn't route cleanly through that chain. Checked interface state inside the VM with `ip a` and SSH status with `sudo systemctl status ssh` first — both were fine, so the problem was upstream of the VM.

**Fix:**
Switched the VM's network adapter from Bridged to **NAT with port forwarding** (Host Port 2222 → Guest Port 22), then connected with `ssh -p 2222 user@127.0.0.1`. Simpler and complication-free for a teaching demo — bridged is preferred when the host network allows it, but NAT + port forwarding is the reliable fallback.

**Lesson:** If SSH is refused or unreachable and the VM's own interface and SSH service both look healthy, the problem is the network path, not the VM. Bridged Adapter assumes a simple flat network — unusual host setups (bridges, double NAT, VPNs) can break it. NAT + port forwarding is the fallback that always works.

---

### [Week 2] SSH socket-activated but "inactive (dead)"

**Service:** Ubuntu Server VM / SSH
**Session:** 02

**Symptom:**
`systemctl status ssh` showed `loaded but inactive (dead), triggered by ssh.socket`.

**Diagnosis:**
SSH was installed but not actively running — it was waiting on socket activation rather than running as a persistent daemon.

**Fix:**
```bash
sudo systemctl start ssh
sudo systemctl enable ssh
```

**Lesson:** "Inactive" doesn't always mean broken — check whether a service is socket-activated before assuming installation failed. `enable` matters as much as `start`, otherwise it won't survive a reboot.

---

### [Week 2] IP address changes after reboot

**Service:** Ubuntu Server VM / networking
**Session:** 02–03

**Symptom:**
Server was reachable one session, unreachable the next — SSH times out at the old IP.

**Diagnosis:**
DHCP reassigned a new address on reboot. Confirmed by running `ip a` inside the VM console.

**Fix:**
Run `ip a`, note the new `inet` line, SSH to the new address. Longer-term fix (introduced Session 3): set a static IP via Netplan, or reserve the VM's MAC address in the router's DHCP settings.

**Lesson:** This is the important one — always run `ip a` first if a service that was working suddenly isn't reachable. Deliberately left unfixed at the start so students hit it as a real troubleshooting experience, then solved properly with a static IP once Docker enters the picture.

---

### [Week 3] Docker permission denied on `docker run hello-world`

**Service:** Docker
**Session:** 03

**Symptom:**
`docker run hello-world` fails with a permission error immediately after install.

**Diagnosis:**
User's shell session doesn't yet have the refreshed group membership for the `docker` group added during install.

**Fix:**
```bash
newgrp docker
```
then retry.

**Lesson:** Group membership changes don't apply retroactively to an already-open shell. A fresh `newgrp` or a full re-login clears it.

---

### [Week 4] Pi-hole fails to start — port 53 already in use

**Service:** Pi-hole
**Session:** 04

**Symptom:**
`docker compose up -d` ran without error, but Pi-hole's admin dashboard was unreachable. `docker logs pihole` showed:
```
Error starting userland proxy: listen tcp4 0.0.0.0:53: bind: address already in use
```

**Diagnosis:**
Ubuntu's `systemd-resolved` runs a local DNS stub listener bound to port 53 by default, competing with Pi-hole for the same port.
```bash
sudo ss -tulpn | grep :53
# LISTEN 127.0.0.53:53  users:(("systemd-resolve",...))
```

**Fix:**
```bash
sudo nano /etc/systemd/resolved.conf
# Set: DNSStubListener=no
#      DNS=127.0.0.1
sudo systemctl restart systemd-resolved
docker compose down && docker compose up -d
```
**Lesson:** Before assuming a port conflict is between two containers, check what the OS itself is using. `sudo ss -tulpn | grep :<port>` is the first diagnostic step for any "address already in use" error. This is expected on every Ubuntu install — not a student mistake.

---

### [Week 4] Pi-hole running but query log stays empty

**Service:** Pi-hole
**Session:** 04

**Symptom:**
Admin dashboard loads fine, no container errors, but the query log shows zero traffic despite browsing the web.

**Diagnosis:**
`/etc/resolv.conf` still pointed at the old `127.0.0.53` systemd-resolved stub, so DNS queries were never actually being routed through Pi-hole.

**Fix:**
```bash
sudo nano /etc/resolv.conf
# nameserver 127.0.0.1
```
Queries appeared in the Pi-hole log immediately.

**Lesson:** Deploying a service and actually routing traffic through it are two separate steps. Always verify the system is using the thing you just deployed, not just that it's running.

---

### [Week 4] Pi-hole web UI unreachable via https://

**Service:** Pi-hole
**Session:** 04

**Symptom:**
`https://<ip>/admin` fails with "Unable to connect."

**Diagnosis:**
Pi-hole's web UI serves plain HTTP by default; no cert is configured at this stage of the course (HTTPS comes later, in Session 9).

**Fix:**
Use `http://<ip>/admin`, not `https://`.

**Lesson:** Simple but very common early mistake — worth flagging explicitly in materials before students hit it. Also a good moment to double check `docker ps` shows the container actually running if the plain-http URL still fails.

---

### [Week 5] Uploaded files disappear after container restart

**Service:** File Browser
**Session:** 05

**Symptom:**
Uploaded a test file through the File Browser UI, restarted the container with `docker compose down && docker compose up -d`, and the file was gone.

**Diagnosis:**
The Compose file was missing a volume definition. Anything written inside a container's own filesystem is not persisted — it only exists for the container's lifetime.

**Fix:**
Add a bind-mount volume for the storage directory in `docker-compose.yml` so uploaded data lives on the host, not inside the container.

**Lesson:** This is the core lesson of the whole session — deploying a service and persisting its data are two different problems. Always check for a volume mount before trusting that uploaded/created data will survive a restart.

---

### [Week 6] Immich UI not loading right after `docker compose up -d`

**Service:** Immich
**Session:** 06

**Symptom:**
`http://<ip>:2283` doesn't load immediately after bringing the stack up.

**Diagnosis:**
`docker compose logs -f immich-server` shows "waiting for database" — the Postgres container needs time to initialize before the server container can connect.

**Fix:**
Wait roughly 60 seconds and refresh. If it still fails after that, check that `DB_PASSWORD` is actually set in `.env` — a missing value is the most common real cause of a stuck database container.

**Lesson:** Multi-container stacks with a database dependency have a startup order problem, not necessarily a config problem. Give it a minute before troubleshooting further, but go straight to `.env` if it's still down after that.

---

### [Week 6] Immich machine-learning container slow to start

**Service:** Immich (ML container)
**Session:** 06

**Symptom:**
Main UI is reachable and usable, but the ML container is still pulling/initializing for several minutes.

**Diagnosis:**
Normal cold-start behaviour — the ML image and model weights are large on first run.

**Fix:**
No fix needed. Reassure and move on; the main UI and upload functionality work before ML finishes initializing.

**Lesson:** Not every "still loading" is a bug. Worth pre-empting in materials so it doesn't get treated as a break.

---

### [Week 6] Immich Compose file leaks the DB password if pushed to GitHub as-is

**Service:** Immich / portfolio documentation
**Session:** 06

**Symptom:**
N/A — a documentation/security hygiene issue, not a runtime failure.

**Diagnosis:**
The Compose file for Immich contains `DB_PASSWORD` as a literal value pulled from `.env`. Pushing it straight to a public GitHub repo exposes that value.

**Fix:**
Sanitise before pushing — keep the variable name in the file, strip or replace the actual password value.

**Lesson:** Anything routed through `.env` needs a manual check before it goes into a public portfolio repo. Applies to any future service with credentials in Compose (Vaultwarden, NPM DB if used, etc).

---

### [Week 7] Vaultwarden's bundled Caddy collides with Nginx Proxy Manager (discovered in Week 9 rehearsal)

**Service:** Vaultwarden / Nginx Proxy Manager
**Session:** Surfaced in Session 9 rehearsal, root cause traces to Session 7

**Symptom:**
When bringing up NPM, `docker compose up -d` fails or NPM can't bind ports 80/443 — already in use.

**Diagnosis:**
Standard Vaultwarden setup guides bundle a Caddy container to provide HTTPS for browser-extension autofill. Caddy binds 80/443 on the host, which is exactly what NPM needs once it's deployed in Session 9. Since most students follow a typical Vaultwarden guide, this collision is expected for effectively everyone, not an individual misconfiguration.

**Fix:**
```bash
cd ~/vaultwarden
docker compose stop caddy
docker compose rm -f caddy
```
Then add Vaultwarden as a Proxy Host in NPM instead, using the shared `proxy` network:
- Domain: `vault.infralabs.uk`
- Forward Hostname/IP: `vaultwarden` (container name)
- Forward Port: `80` (internal container port)
- Websockets Support: on

**Lesson:** A service's default self-hosted setup guide can quietly plant a future conflict several sessions later. Worth flagging explicitly in Session 9's homework/prep check, and worth adding a forward-looking note in Session 7's own materials so students aren't blindsided when Caddy has to come out.

---

### [Week 9] `docker compose up -d` doesn't re-apply port mappings to an existing container

**Service:** Docker / general
**Session:** 09

**Symptom:**
Changed a port mapping (or other config) in `docker-compose.yml`, ran `docker compose up -d`, and the change didn't take effect — old ports/config still active.

**Diagnosis:**
If the container already exists, `up -d` will not recreate it just because the Compose file changed, in some cases — the stale container keeps its original settings.

**Fix:**
```bash
docker compose down
docker compose up -d
```

**Lesson:** When a config change in a Compose file doesn't seem to apply, don't assume the file is wrong — try a full `down && up -d` cycle before debugging further.

---

### [Week 9] NPM Proxy Host uses the wrong port — confusing host-mapped port with internal container port

**Service:** Nginx Proxy Manager
**Session:** 09

**Symptom:**
Proxy Host configured for a service (e.g. Pi-hole) returns a bad gateway or doesn't connect, even though the service works fine directly via its host-mapped port.

**Diagnosis:**
NPM talks to other containers over the shared `proxy` Docker network, container-to-container — so the **Forward Port** field must be the container's internal port, not the host-mapped port. Example: Pi-hole is mapped to host port 8081, but its internal container port is still 80, and 80 is what NPM needs in the Forward Port field. Confirmed the internal port by reading the number after the `->` arrow in `docker ps` output.

**Fix:**
Always set Forward Port to the container's internal port (right-hand side of `->` in `docker ps`), not whatever host port it happens to be published on.

**Lesson:** Host-mapped ports and internal container ports serve different purposes — host ports are for direct human/browser access, internal ports are what other containers on the same Docker network use to reach a service. Worth a port reference table per service (Pi-hole 80, File Browser 80, Vaultwarden 80, Uptime Kuma 3001, Immich 2283) so this isn't re-derived from scratch each time.

---

## Recurring patterns across sessions

A few things show up more than once and are worth remembering as first diagnostic steps generally:

- **"Address already in use" on any port** → check what the OS itself has bound first: `sudo ss -tulpn | grep :<port>`, before assuming it's a container conflict.
- **Service unreachable after a reboot** → run `ip a` first. IP changes are the most common single cause of "it worked yesterday."
- **Config change doesn't seem to apply** → try `docker compose down && docker compose up -d` before assuming the file itself is wrong.
- **Data lost after container restart** → check for a missing volume mount before anything else.
- **A service that plugs into the shared `proxy` network** → use the container's internal port, not its host-mapped port, when configuring NPM.
