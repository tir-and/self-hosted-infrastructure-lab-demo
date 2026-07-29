# Session 2 — Linux Server, SSH & Basic Administration

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. This is the first session with an actual technical build — notice how the structure stays the same as Session 1, it's just got real content in it now.

## What I built

This week I set up the actual server that everything else in this lab runs on. So far it's just a plain Ubuntu Server VM sitting inside VirtualBox on my own machine — nothing fancy but it's the foundation for the rest of the semester. From here on, every service I deploy lives inside this VM.

> 💡 **Why this matters as an example:** Notice the README doesn't oversell a plain VM install. It says plainly what it is — a foundation, not a finished thing — which is exactly the case at this point in the programme.

## Why this one matters

This is not:
> "Installed an operating system."

This is actually about:
- moving from console access to SSH — how every real sysadmin actually works
- basic Linux navigation and package management
- understanding your VM's network identity — its IP, and why that matters for everything that connects to it later

## VM specs

- Name: `ubuntu-lab`
- OS: Ubuntu Server (no desktop, terminal only)
- RAM: 4096 MB
- Disk: 25 GB
- Network: Bridged Adapter (so the VM gets its own IP on my home network, not a NAT'd one)
- Username: `labuser`

## Getting the IP

Once the VM was up, I ran:

```
ip a
```

and looked for the `inet` line on the network adapter (not `127.0.0.1`). That gave me the VM's actual address on my network — something like `192.168.1.x`. I wrote it down because I'll need it every time I SSH in.

## Connecting via SSH

Instead of working inside the VirtualBox console window, I switched to SSH from my normal terminal:

```
ssh labuser@192.168.1.x
```

> 💡 **Why this matters as an example:** This is the bigger shift this week — not the VM itself, but moving from "clicking inside a window" to "working from a real terminal like a sysadmin would." It's a small change that matters more than it looks.

## First commands I ran

```
sudo apt update && sudo apt upgrade -y   # update packages
whoami                                    # who am I
hostname                                  # what's this machine called
df -h                                     # disk space
free -h                                   # memory usage
top                                       # running processes
sudo journalctl -n 20                     # last 20 log lines
```

Disk, memory, processes, logs — that's the diagnostic sequence I'll be running any time something behaves oddly for the rest of the programme.

## One problem I hit and how I fixed it

SSH connection was refused at first. Turned out I hadn't ticked the "Install OpenSSH Server" option during the Ubuntu installer, so there was no SSH daemon running on the VM to connect to. Fixed it by reinstalling and making sure that box was checked this time.

> 💡 **Why this matters as an example:** This is the section that matters most, even in a "simple" session like this one. A perfect install with no notes shows nothing. A specific mistake, explained clearly, shows you can actually troubleshoot.

## Screenshots

- VirtualBox showing the VM running
- SSH terminal session (`labuser@ubuntu-lab`)
- Output of `ip a`
- Output of `df -h` / `free -h`

## Next up

Session 3 — Docker and Docker Compose. Homework: come with the VM running and SSH access confirmed.
