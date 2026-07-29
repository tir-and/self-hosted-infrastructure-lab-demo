# Architecture Diagrams

> 💡 **Note for students:** This folder is a visual changelog, not just a place to dump images. Every session that changes the architecture gets a new diagram added — old ones are never deleted or overwritten. Together they show the lab growing one layer at a time, which is the whole point of the programme.

## Naming convention

```
architecture-s{semester}-week{week}-{service}.png
```

Examples:
- `architecture-s1-week02-linux-server.png`
- `architecture-s1-week03-docker.png`
- `architecture-s1-week04-pihole.png`
- `architecture-s2-week01-hardening-backups.png`
- `architecture-s2-week02-authelia.png`

Semester and week are both included because each semester restarts its own week numbering (Semester 2, Week 1 is a different session than Semester 1, Week 1) — so the semester prefix keeps filenames unambiguous.

## Why diagrams are versioned, not replaced

Each diagram should reflect the *full* architecture as it stood at the end of that week — not just the new piece added. So `architecture-s1-week04-pihole.png` shows the VM, Docker, and Pi-hole together, not Pi-hole in isolation. That way, looking at the diagrams in order tells the story of the lab growing, session by session, without needing to read every README to understand how it evolved.

> 💡 **Why this matters as an example:** This is a small habit, but it's a good one to carry into real infrastructure work — documentation that shows *how a system got to its current state*, not just what the current state is, is far more useful when something breaks or when someone new joins the project.

## Current architecture

The most recent diagram — the one showing the lab as it stands right now — is always the one with the highest week number for the current semester. Check the filenames above to find it.
