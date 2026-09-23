# npm-nginx-proxy-manager

Self-hosted [Nginx Proxy Manager](https://nginxproxymanager.com/setup/) via Docker Compose (SQLite backend).

## Run

```bash
docker compose up -d
docker compose logs -f app   # first run generates the DB, JWT keys and admin login
```

Admin UI: `http://<server-ip>:81`

| Port | Purpose |
|------|---------|
| 80   | Public HTTP |
| 443  | Public HTTPS |
| 81   | Admin UI — bound to 127.0.0.1 only; reach it via `npm.<domain>` or `ssh -L 8181:localhost:81` |

## Proxying other apps

This stack creates a shared Docker network named `proxy`. Start it first, then
attach other apps to it — they need no public `ports:`:

```yaml
services:
  myapp:
    image: myapp:latest
    container_name: myapp
    restart: unless-stopped
    expose:
      - '3000'   # internal only
    networks:
      - proxy

networks:
  proxy:
    external: true
```

In the NPM proxy host: Forward Hostname = `myapp` (container name), Forward Port = `3000`.

## Data

- `./data` — database, proxy host configs, keys
- `./letsencrypt` — SSL certificates

Both are git-ignored; back them up separately.

## Upgrade

Bump the image tag in `docker-compose.yml`, then:

```bash
docker compose pull && docker compose up -d
```
