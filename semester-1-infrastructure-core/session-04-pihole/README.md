# Session 4 — DNS and Ad Blocking with Pi-hole

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. This is the first service deployed on top of Docker — notice the structure is identical to Session 3, just applied to a new service.

## What I built

This week I deployed Pi-hole — a DNS server that filters ads and trackers for every device on my network, not just this VM. Instead of installing anything directly on the host, it runs as a Docker container, same as everything else in this lab from here on.

> 💡 **Why this matters as an example:** Notice this doesn't just say "installed an ad blocker." It's specific about *what Pi-hole actually is* — a DNS server — because that's the actual concept being taught this week, not the ad-blocking side effect.

## Why this one matters

This is not:
> "Installed an ad blocker."

This is actually about:
- what DNS does — how every device asks "what's the address for this name?" before it can connect to anything
- local DNS filtering — blocking known ad and tracker domains at the DNS level, before a connection is even made
- reading DNS query logs — seeing exactly what every device on the network is trying to reach, in real time
- blocklists — where they come from, and that they're just plain text files of domains, nothing magic

## Deployment

Deployed via Docker Compose. Config lives in `docker-compose-yml` added here.

> 💡 **Why this matters as an example:** Note port 53 is Pi-hole's actual DNS port — that's fixed by the app, not something you choose. Port 8081 for the web interface is the one you pick, mapped from Pi-hole's internal port 80. This is the same "inside the container vs outside the container" port distinction from Session 3, just applied here.

## What I tested

- Opened `http://192.168.1.200:8081/admin` and logged into the Pi-hole dashboard
- Pointed one device's DNS settings to `192.168.1.200` to actually use Pi-hole for lookups
- Visited a few ad-heavy sites and watched the **Query Log** — requests to known ad/tracker domains are getting blocked
- Checked the dashboard stats to see the percentage of queries blocked

> 💡 **Why this matters as an example:** Same principle as Session 3's "stop the container to prove monitoring works" — here, actually pointing a device at Pi-hole and watching queries get blocked live is what proves it works, rather than just showing the dashboard sitting there.

## One problem I hit and how I fixed it

[Fill this in with your actual issue — e.g.]

My first attempt had Pi-hole failing to bind to port 53, because `systemd-resolved` was already using it on the VM by default. Fixed it by disabling the stub listener in `systemd-resolved`'s config and restarting the service, which freed up port 53 for Pi-hole.

> 💡 **Why this matters as an example:** Port 53 conflicts are one of the most common Pi-hole setup issues — worth documenting clearly rather than skipping past it, since it's the kind of thing another student is very likely to hit too.

## Screenshots

- Pi-hole admin dashboard showing query stats
- Live query log with blocked requests highlighted
- Device network settings pointed at the Pi-hole IP

## Next up

Session 5 — Private File Services with File Browser.
