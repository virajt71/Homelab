# Homelab

Self-hosted Docker Compose stack for home network infra - DNS-level ad-blocking, TLS reverse proxy, and container management, all reachable via LAN.

## Stack

| Service | Image | Role | Network / Ports |
|---|---|---|---|
| **AdGuard Home** | `adguard/adguardhome` | DNS server + network-wide ad-block | macvlan → `192.168.50.145` (53/tcp+udp, 784/udp, 853/tcp) |
| **Nginx Proxy Manager** | `jc21/nginx-proxy-manager:2.15.1` | Reverse proxy + TLS termination | host `:80` `:81` `:443` |
| **Dockhand** | `fnsys/dockhand:latest` | Docker management UI | host `:3000` |

## Architecture

- **AdGuard Home** runs on a dedicated **macvlan** network so it gets its own LAN IP (`192.168.50.145/32` off `eno1`, gateway `192.168.50.1`) and can act as the network's primary DNS server — no port juggling with the host.
- **NPM** and **Dockhand** run in bridge mode, publishing straight to host ports, and sit behind NPM's own reverse proxy for `*.lan` domains.
- All three services ship `healthcheck` blocks so `docker compose ps` gives real up/down status, not just "running."

## Quick start

```bash
git clone https://github.com/virajt71/Homelab.git
cd Homelab
docker compose up -d 
docker compose ps   # check STATUS column
```

## First-run notes

- **AdGuard Home**: initial setup wizard is on container port `:3000` (`http://192.168.50.145:3000`). After setup completes, the admin UI moves to `:80`/`:443`.
- **NPM**: admin UI → `http://<host>:81` (proxy hosts served on `:80`/`:443`).
- **Dockhand**: UI → `http://<host>:3000`, mounts `docker.sock` for container management. Requires the external volume `dockhand_data` to exist before first run:
  ```bash
  docker volume create dockhand_data
  ```

## Requirements

- Docker + Docker Compose v2
- A free static IP on your LAN subnet for AdGuard Home's macvlan address
- Host NIC name (`eno1`) matching your actual interface — check with `ip a` and update `compose.yaml` if different

## Volumes

- `agh_conf`, `agh_work` — AdGuard Home config/state
- `dockhand_data` (external) — Dockhand app data
- `./npm_data`, `./letsencrypt` — NPM config and certs (gitignored, host-mounted)

---

## Suggested GitHub repo description

> Self-hosted homelab stack: AdGuard Home (macvlan DNS), Nginx Proxy Manager, and Dockhand — Docker Compose, health-checked, LAN-ready.