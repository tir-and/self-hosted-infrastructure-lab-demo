# Session 3 — Docker and Docker Compose

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. This session has more moving parts than Session 2 — notice the README still follows the same shape, it's just got more sections because more actually happened this week.

## What I built

This week I installed Docker on the VM and ran my first container — a plain nginx web server, just to prove the whole pipeline works end to end: pull an image, run a container, reach it in a browser. I also set a static IP on the VM before starting, since I got tired of the address changing every time it rebooted.

> 💡 **Why this matters as an example:** Notice the static IP work gets its own short section even though it's not the "main" topic of the week. Small setup decisions like this are worth documenting too — they're exactly the kind of thing a student six months from now will forget they did and wonder why their IP suddenly stopped changing.

## Why this one matters

This is not:
> "Installed Docker."

This is actually about:
- the difference between containers and full virtual machines — and why that difference matters for running a dozen services on one modest VM
- the image → container → running service model, since every future service in this lab follows this exact pattern
- ports — how a container's internal port maps to a port on the host, and why that mapping is what makes multiple services coexist without conflicts

## Setting a static IP

Before installing Docker, I locked in a static IP for the VM so it doesn't drift every time it reboots — every service from here on will be reached via this same address.

Used Netplan, directly on the VM:

```
sudo nano /etc/netplan/00-installer-config.yaml
```

Changed `dhcp4: true` to:

```yaml
dhcp4: no
addresses: [192.168.1.200/24]
gateway4: 192.168.1.1
nameservers:
  addresses: [8.8.8.8, 1.1.1.1]
```

Saved and applied:

```
sudo netplan apply
```

Picked `192.168.1.200` specifically because it's outside my router's DHCP range, so there's no risk of a conflict later.

## Installing Docker

Used Docker's official convenience script rather than manually adding the repo and GPG key — quicker for a single VM, and it's still Docker's own installer, not a third-party one:

```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

Then restarted the VM so the new group membership actually took effect.

> 💡 **Why this matters as an example:** Notice this explains *why* the convenience script was used instead of the manual repo method — not just what command was run. There's more than one valid way to install something, and a good README says which path you took and gives a quick reason, rather than assuming it's the only way.

## Verifying the install

```
docker --version
sudo systemctl status docker
docker run hello-world
```

`docker run hello-world` is the one that actually matters — it pulls an image from Docker Hub, creates a container from it, runs it, and lets it exit. That exact sequence — pull, create, run — is what happens with every service deployed from here on.
```

## My first real container — nginx

```
docker pull nginx
docker run -d --name nginx-test -p 8080:80 nginx
```

Opened `http://192.168.1.200:8080` in the browser and got the default nginx welcome page.

Then inspected and cleaned up:

```
docker ps
docker logs nginx-test
docker stop nginx-test
docker rm nginx-test
```

> 💡 **Why this matters as an example:** This is the full lifecycle, not just "it worked." Pull, run, open in browser, inspect, stop, remove — every service in this lab will get run through some version of this same sequence, so it's worth actually doing all of it once here, not just the "run" step.

## One problem I hit and how I fixed it

First time I ran `docker run hello-world`, it failed with a permission error — turns out my user wasn't part of the `docker` group yet, so it couldn't talk to the Docker daemon without `sudo`. Fixed it by running `newgrp docker`, which picks up the new group membership without needing to log out and back in.

> 💡 **Why this matters as an example:** This is a genuinely common first-timer issue with Docker, so it's a good one to have documented plainly rather than glossed over.

## Screenshots

- Terminal output of `docker run hello-world`
- Browser showing the nginx welcome page at `http://192.168.1.200:8080`
- `docker ps` showing the running container
- Output of `ip a` confirming the static IP took effect

## Next up

Session 4 — DNS and Ad Blocking with Pi-hole.
