# Homelab

Docker Compose stack for the home network.

## Services

| Service       | Image                            | Role                          | Reach                       |
|---------------|----------------------------------|-------------------------------|-----------------------------|
| adguardhome   | adguard/adguardhome              | DNS + network ad-block        | 192.168.50.145 (macvlan)    |
| npm           | jc21/nginx-proxy-manager:2.15.1  | Reverse proxy / TLS           | host :80 :81 :443           |
| dockhand      | fnsys/dockhand:latest            | Docker management UI          | host :3000                  |

## Layout
- AdGuardHome sits on a **macvlan** network so it owns its own LAN IP
  (192.168.50.145) and can be the network DNS server.
- NPM and Dockhand publish on the host and sit behind the reverse proxy.

## Run
    docker compose up -d
    docker compose ps        # health status in STATUS column

## Notes
- AGH first-run setup listens on container :3000 (192.168.50.145:3000);
  after setup the admin UI moves to :80 / :443.
- `npm.lan` admin UI is on host :81; `dockhand.lan` on host :3000.
