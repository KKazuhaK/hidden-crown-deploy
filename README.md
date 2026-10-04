# Hidden Crown deployment templates

Public deployment files only; application source is maintained in a separate private repository. The public multi-platform image is `ghcr.io/kkazuhak/hidden-crown:2.0.0` (Linux AMD64 and ARM64).

The standalone `docker-compose.yml` defaults to SQLite and uses a bind mount: `/opt/hidden-crown/data` when placed in `/opt/hidden-crown`. Container UID/GID is `1000:1000`. Administrator credentials have no defaults; `.env.example` is an unconfigured example. Leave `DATABASE_URL` empty for quick testing without a database service.

Use the complete `docker-compose.postgres.yml` instead to connect to your existing PostgreSQL 14+ with `DATABASE_URL`. This template starts only the application; it adds neither a database container nor a SQLite volume. Use a dedicated database and application role. See [DEPLOYMENT.md](DEPLOYMENT.md) for SQL setup, Docker-to-host connectivity and all settings. Redis and MySQL are not required.

## First installation

Download `install.sh`, inspect it, then run `sudo bash install.sh`. It requires an existing Docker Engine, Docker Compose plugin, wget and openssl. Enter the complete public HTTPS URL when prompted. The script creates `/opt/hidden-crown`, downloads this repository's templates, creates a restricted data directory, generates a 48-character administrator password, writes `.env`, pulls the image and starts the application. It refuses to replace any existing deployment configuration or SQLite database.

Configure host Nginx with HTTPS and WebSocket support, proxying to `http://127.0.0.1:8787`. The installer does not modify Nginx, DNS, certificates or firewall settings. See `DEPLOYMENT.md` and the examples under `deploy/`.

Version 2.0 starts with a fresh database and does not import 1.x games. The default SQLite filename is `hidden-crown-v2.sqlite`; the old file remains untouched. Export games you need before replacing a 1.x deployment, and retain the old files for rollback. Existing room links do not recover into a fresh store.

Later updates preserve `data/` or the configured PostgreSQL database: edit the image version in `.env`, then run `docker compose pull && docker compose up -d` from `/opt/hidden-crown`. For SQLite backups, stop the service and copy the complete `data/` directory, including WAL/SHM files; for PostgreSQL use `pg_dump`. Run one application instance per database: cross-instance routing and broadcast are future extension points.
