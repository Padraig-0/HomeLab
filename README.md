# Homelab

Self-hosted infrastructure running on a dedicated Ubuntu Server 26.04 LTS box, managed via Docker Compose.

## Stack

### `docker/core/`
- **Portainer CE** — container management UI (`https://<host>:9443`)
- **Nginx Proxy Manager** — reverse proxy, internal `*.lan` routing, SSL termination (`http://<host>:81`)
- **Pi-hole** — local DNS resolution for `*.lan` hostnames (`http://<host>:8080/admin`)
- **Cloudflare DDNS** — keeps `mc.<mydomain>.com` pointed at the current public IP

### `docker/apps/`
- **Minecraft (Prominence II: Hasturian Era)** — modded Fabric server via `itzg/minecraft-server`, port `25565`

## Network

- Host static IP: reserved via router DHCP reservation
- Internal services resolved via Pi-hole (`*.lan`), configured manually per-device since the router doesn't support DHCP-pushed DNS
- Minecraft exposed externally via router port forward (`25565` TCP) + Cloudflare DDNS for a stable hostname

## Setup / Restore

1. Clone this repo to `/srv/docker` on a fresh host
2. Install Docker Engine + Compose plugin
3. For each service directory, copy `.env.example` → `.env` and fill in real values
4. `docker compose up -d` in each service directory (start with `core/portainer`, `core/npm`, `core/pihole` first, since other services depend on routing/DNS)

## Notes

- No secrets are committed as best as possible — see `.gitignore`. Real credentials live only in each service's `.env`, never tracked.
- Runtime/data directories (Pi-hole DB, NPM certs, Minecraft world data) are gitignored — only Compose configs are version-controlled.

- An early version of this repo tracked a Pi-hole admin password directly in docker-compose.yml. The credential was rotated, moved to a gitignored .env with a committed .env.example template, and the repository history was rebuilt to remove both the credential and several hundred megabytes of accidentally-tracked Minecraft server data.
