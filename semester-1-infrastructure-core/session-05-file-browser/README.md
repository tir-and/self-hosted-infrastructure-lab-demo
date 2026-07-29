# Session 5 — Private File Services with File Browser

> 💡 **Note for students:** This is the instructor's demo README, kept at the level of detail you should aim for. This session introduces persistent storage properly for the first time — pay attention to the volumes section, since getting this wrong is an easy way to lose data later in the programme.

## What I built

This week I deployed File Browser — a simple web-based file manager that gives me private access to files on the VM through a browser, from any device on my network. Think of it as a lightweight, self-hosted version of Google Drive, without the cloud storage bill or the third party holding my files.

> 💡 **Why this matters as an example:** If you're wondering why the programme uses File Browser instead of something more feature-complete, that's answered below.

## Why File Browser and not Nextcloud

Nextcloud is more powerful, but it's heavier, slower to set up, and harder to troubleshoot when something goes wrong — not ideal this early in the programme. File Browser does the core job (upload, download, browse, organize files through a browser) with a much smaller footprint and far fewer moving parts. Nextcloud shows up later, in the Advanced track, once there's more Docker experience to fall back on.

## Why this one matters

This is not:
> "Installed a file manager."

This is actually about:
- persistent storage — making sure files survive a container restart, not just live inside it temporarily
- Docker volumes — how a folder on the host gets mounted into a container, so the container can be destroyed and recreated without losing data
- user management — setting up accounts and permissions rather than leaving everything wide open
- browser-based file access — reaching the same files from a phone, laptop, or any device on the network, without installing a client

## Deployment

Deployed via Docker Compose. Config lives in `docker-compose.yml` added here.

> 💡 **Why this matters as an example:** Notice there are two volumes here, not one — `/srv` is the actual files being managed, `/database` is File Browser's own settings and user accounts. Losing the database volume means losing users and config, not files; losing the `/srv` volume means losing the actual data. Worth understanding the difference, not just copying the block.

## What I tested

- Opened `http://192.168.1.200:8082` and logged in with the default admin account
- Changed the default admin password immediately (test credentials only, not a real password)
- Created a second, non-admin user account to test permissions
- Uploaded a few test files through the browser, then downloaded them back to confirm the round trip works
- Restarted the container and confirmed the files and user accounts were both still there afterward — proof the volumes are actually doing their job

> 💡 **Why this matters as an example:** That last bullet — restarting the container and checking nothing was lost — is the actual test of whether persistent storage works. A file sitting there before a restart doesn't prove anything on its own.

## One problem I hit and how I fixed it

[Fill this in with your actual issue — e.g.]

First attempt, uploads kept failing with a permissions error. Turned out the `PUID`/`PGID` I'd set didn't match the ownership of the folder I'd mounted on the host, so the container user couldn't write to it. Fixed it by checking the folder's actual owner with `ls -la` and matching the environment variables to that UID/GID.

> 💡 **Why this matters as an example:** Permission mismatches between the host and a container are one of the most common Docker volume issues you'll hit across this whole programme — worth understanding properly here, since it'll come up again.

## Screenshots

- File Browser login screen
- Dashboard showing uploaded test files
- User management screen showing the second account
- Terminal output confirming files/users persisted after a container restart

## Next up

Session 6 — Photo Backup with Immich.
