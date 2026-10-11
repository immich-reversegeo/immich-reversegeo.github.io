# Installation

## Docker

If you already run immich with Docker Compose, the simplest setup is to add one service to that existing compose file.

> [!WARNING]
>
> **Database backup strongly recommended**
>
> This service updates immich metadata in the database.
>
> Before first use, and especially before using any bulk-clear action from the Data page, create a proper immich database backup.
>
> Official guide: [Backup and Restore | Immich](<https://docs.immich.app/administration/backup-and-restore/>)

## Choose your Compose setup

Add the service and volume declarations below to your existing Immich Compose file. Reuse its `.env` file and default network. Keep `/config` and `/data` persisted separately.

docker-compose.yml

```
# docker-compose.yml
# Add this service to your existing Immich docker-compose.yml.
services:
  immich-reversegeo:
    image: ghcr.io/immich-reversegeo/immich-reversegeo:latest
    container_name: immich-reversegeo
    pull_policy: always
    ports:
      - "8080:8080"          # change the left side if 8080 is already in use; do NOT expose publicly
    volumes:
      - reversegeo-config:/config      # settings (no database secrets in settings.json)
      - reversegeo-data:/data          # Overture caches, skipped.db, runtime data
    env_file:
      - .env                 # reuse Immich's own .env (DB_USERNAME, DB_PASSWORD, etc.)
    environment:
      - ASPNETCORE_URLS=http://+:8080
    mem_limit: 2g
    restart: unless-stopped

volumes:
  reversegeo-config:
  reversegeo-data:
```

Start only Immich ReverseGeo:

```
docker compose up -d immich-reversegeo
```

Save the same service snippet in a separate directory and provide an `.env` file with your Immich database connection values. Join the existing Immich network by adding this section to that Compose file:

```
networks:
  default:
    external: true
    name: immich_default
```

Replace `immich_default` with your actual Immich network name. The database hostname must be reachable on that network. You can inspect available networks with `docker network ls`.

```
docker compose up -d immich-reversegeo
```

Reference file: [docker-compose.yml](<https://github.com/immich-reversegeo/immich-reversegeo/blob/master/docker-compose.yml>).

Both setups need about 2 GB of memory, as set by `mem_limit` in the service snippet.

> [!NOTE]
>
> **Why the memory limit is there**
>
> The bundled country boundary data is held in memory while processing runs and uses several hundred megabytes. `mem_limit` sets a total container memory budget that the .NET runtime can take into account. Large country caches and high parallelism can increase peak memory use.
>
> Do not lower it much below `2g`. A limit under the working set makes the container fail immediately instead of running. Raising it is fine.

Both setups need the database connection values from Immich:

```
DB_HOSTNAME=database
DB_PORT=5432
DB_USERNAME=postgres
DB_PASSWORD=...
DB_DATABASE_NAME=immich
```

Use `DB_HOSTNAME` from Immich's `.env` file, including when connecting to an external database. A nonblank `DB_HOST` takes precedence over `DB_HOSTNAME` for existing installations. If `DB_HOST` is empty or contains only spaces, the app uses `DB_HOSTNAME`. If neither has a nonblank value, the host defaults to `database`.

The example publishes the UI on host port `8080`. Change the left side of the port mapping if it is already in use. Do not expose the UI publicly; see the private-access options below.

## Image versions

`latest` normally follows GitHub's designated latest stable release after its image is successfully published. Maintainers can explicitly promote another validated build using the manual publishing option. Use a published version tag to control upgrades, for example `ghcr.io/immich-reversegeo/immich-reversegeo:1.2.3`. This is an example; choose an available version from [GitHub Releases](<https://github.com/immich-reversegeo/immich-reversegeo/releases>).

- Releases tagged `v1.2.3` publish both `1.2.3` and `v1.2.3` image tags.
- Other stable releases retain their version tags without changing `latest`. Rerunning an older release does not automatically move `latest` backwards.
- Prereleases publish their full version tags, such as `1.2.3-rc.1`, without updating `latest`.
- `edge` follows development builds from `master` after the build, tests, and Docker build checks pass.
- `sha-...` tags identify builds by commit.

After changing the image version in your Compose file, run `docker compose pull immich-reversegeo` and `docker compose up -d immich-reversegeo`. Keep your existing `/config` and `/data` volumes.

## VPS and firewall notes

Do not expose the web UI publicly. Docker-published ports can bypass host firewall rules such as UFW, so do not rely on the host firewall alone to hide a broadly published `8080:8080` mapping on an internet-facing server.

If you run Immich ReverseGeo on a VPS and only need local or SSH-forwarded access, bind the published port to localhost:

```
ports:
  - "127.0.0.1:8080:8080"
  # - "[::1]:8080:8080"
```

Then use a private path such as SSH local port forwarding, a VPN, or a trusted reverse proxy with authentication:

```
ssh -N -L 8080:localhost:8080 user@host
```

Then open:

```
http://localhost:8080
```

## Runtime notes

- Built-in data covers country matching and airport matching.
- More detailed country data is downloaded only when needed.
- The app needs internet access the first time it downloads data for a new country.
- Large countries can use hundreds of megabytes each under `/data`.

## After install

- Use [Configuration](<https://immich-reversegeo.github.io/configuration/index.md>) for database and processing settings.
- Use [Using the App](<https://immich-reversegeo.github.io/using-the-app/index.md>) for manual runs, coordinate testing, and reset tools.
