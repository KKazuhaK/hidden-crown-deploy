# Hidden Crown deployment templates

Public deployment files only; application source is maintained in a separate private repository. The public multi-platform image is `ghcr.io/kkazuhak/hidden-crown:1.0.0`.

The standalone `docker-compose.yml` uses a bind mount: `/opt/hidden-crown/data` when placed in `/opt/hidden-crown`. Container UID/GID is `1000:1000`. Administrator credentials have no defaults; `.env.example` is an unconfigured example.

## First installation

Download `install.sh`, inspect it, then run `sudo bash install.sh`. It requires an existing Docker Engine, Docker Compose plugin, wget and openssl. Enter the complete public HTTPS URL when prompted. The script creates `/opt/hidden-crown`, downloads this repository's templates, creates a restricted data directory, generates a 48-character administrator password, writes `.env`, pulls the image and starts the application. It refuses to replace any existing deployment configuration or SQLite database.

Configure host Nginx with HTTPS and WebSocket support, proxying to `http://127.0.0.1:8787`. The installer does not modify Nginx, DNS, certificates or firewall settings. See `DEPLOYMENT.md` and the examples under `deploy/`.

Updates preserve `data/`: edit the image version in `.env`, then run `docker compose pull && docker compose up -d` from `/opt/hidden-crown`. For backups, stop the service and copy the complete `data/` directory, including SQLite WAL/SHM files.
