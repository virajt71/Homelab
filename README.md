# Homelab & Dockhand Template Catalog 🚀

Self-hosted Docker Compose stack for home network infrastructure — DNS-level ad-blocking, TLS reverse proxy, container management, and a curated **Dockhand & Portainer App Template Catalog**.

---

## 📦 Dockhand & Portainer Template Catalog

This repository serves as a **Portainer v2 / Dockhand compatible App Template Catalog** specifically tailored for the **Homelab Core Stack** and its component services. Easily deploy self-hosted applications with pre-configured ports, environment variables, and persistent volumes directly from your **Dockhand** or **Portainer** dashboard.

### 🔗 Template Catalog Raw URL

Paste this URL into your Dockhand or Portainer settings:

```text
https://raw.githubusercontent.com/virajt71/Homelab/develop/templates.json
```

---

### 🛠️ How to Add to Dockhand

1. Open your **Dockhand** dashboard (e.g. `http://<your-server-ip>:3000`).
2. Navigate to **Settings** → **Sources** (or Templates settings).
3. Click **Add Source** or update the Template URL field.
4. Paste the URL:
   `https://raw.githubusercontent.com/virajt71/Homelab/develop/templates.json`
5. Save changes and navigate to the **Templates** tab to browse and deploy apps with a single click!

---

### 🛠️ How to Add to Portainer

1. Log into your **Portainer** instance.
2. Go to **Settings** in the left sidebar.
3. Under **App Templates**, select **Use custom template URL** (or **External templates**).
4. Enter `https://raw.githubusercontent.com/virajt71/Homelab/develop/templates.json` into the **URL** field.
5. Click **Save settings**, then head to **App Templates** in the sidebar to view all available applications.

---

## 📚 Included Templates

| Icon | Application | Category | Type | Default Ports | Description |
| :---: | :--- | :--- | :---: | :---: | :--- |
| <img src="https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/png/dockhand.png" width="32"> | **Homelab Core Stack** | Homelab, Networking | Stack | `53`, `80`, `81`, `443`, `3000` | Full 3-in-1 stack: AdGuard Home (macvlan DNS), NPM (Proxy/TLS), Dockhand (UI). |
| <img src="https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/png/adguard-home.png" width="32"> | **AdGuard Home** | Networking, Security | Container | `53`, `784`, `853`, `3000` | Network-wide DNS ad-blocker & tracker protection. |
| <img src="https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/png/nginx-proxy-manager.png" width="32"> | **Nginx Proxy Manager** | Networking, Proxy | Container | `80`, `81`, `443` | Reverse proxy with automated Let's Encrypt SSL management. |
| <img src="https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/png/dockhand.png" width="32"> | **Dockhand** | Management, Docker | Container | `3000` | Lightweight Docker management software with template catalog support. |

---

## 🏛️ Local Homelab Stack Architecture

When running the full stack using `stacks/homelab-full/compose.yaml`:

| Service | Image | Role | Network / Ports |
|---|---|---|---|
| **AdGuard Home** | `adguard/adguardhome` | DNS server + network-wide ad-block | macvlan → `192.168.50.145` (53/tcp+udp, 784/udp, 853/tcp) |
| **Nginx Proxy Manager** | `jc21/nginx-proxy-manager:2.15.1` | Reverse proxy + TLS termination | host `:80` `:81` `:443` |
| **Dockhand** | `fnsys/dockhand:latest` | Docker management UI | host `:3000` |

### Key Design Notes
- **AdGuard Home** runs on a dedicated **macvlan** network so it gets its own LAN IP (`192.168.50.145/32` off interface `eno1`, gateway `192.168.50.1`) and can act as the network's primary DNS server — avoiding host port conflicts.
- **NPM** and **Dockhand** run in bridge mode, publishing straight to host ports, and sit behind NPM's own reverse proxy for `*.lan` domains.
- All three services ship explicit `healthcheck` blocks so `docker compose ps` gives real up/down status.

---

## 🚀 Quick Start (Homelab Stack)

```bash
# 1. Clone repository
git clone https://github.com/virajt71/Homelab.git
cd Homelab

# 2. Create required external volume for Dockhand
docker volume create dockhand_data

# 3. Start the homelab full stack
docker compose -f stacks/homelab-full/compose.yaml up -d

# 4. Verify running health status
docker compose -f stacks/homelab-full/compose.yaml ps
```

---

## 📁 Repository Structure

```text
Homelab/
├── templates.json              # Portainer v2 / Dockhand App Template Catalog
├── stacks/                     # Full stack templates
│   └── homelab-full/
│       └── compose.yaml        # Main Docker Compose file for full Homelab stack
└── README.md
```

---

## 🤝 Contributing New Templates

Want to add a new app template to the catalog?

1. Add your application definition into [`templates.json`](templates.json).
2. If it's a multi-container stack, add its compose file under `stacks/<app-name>/compose.yaml`.
3. Submit a Pull Request!