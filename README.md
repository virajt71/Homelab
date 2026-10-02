# Homelab & Dockhand Template Catalog 🚀

Self-hosted Docker Compose stack for home network infrastructure — DNS-level ad-blocking, TLS reverse proxy, container management, and a curated **Dockhand & Portainer Stack Template**.

---

## 📦 Dockhand & Portainer Template Catalog

This repository serves as a **Portainer v2 / Dockhand compatible App Template Catalog** for deploying the complete **Homelab Stack** (AdGuard Home + Nginx Proxy Manager + Dockhand) as a single unified stack.

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
5. Save changes and navigate to the **Templates** tab. You will see **1 single template card** for the entire Homelab stack!

---

### 🛠️ How to Add to Portainer

1. Log into your **Portainer** instance.
2. Go to **Settings** in the left sidebar.
3. Under **App Templates**, select **Use custom template URL** (or **External templates**).
4. Enter `https://raw.githubusercontent.com/virajt71/Homelab/develop/templates.json` into the **URL** field.
5. Click **Save settings**, then head to **App Templates** in the sidebar.

---

## 📚 Included Template

| Icon | Application | Category | Type | Services Included | Description |
| :---: | :--- | :--- | :---: | :---: | :--- |
| <img src="https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/png/dockhand.png" width="32"> | **Homelab** | Homelab, Networking, Management | Stack | `adguardhome`, `npm`, `dockhand` | Complete 3-in-1 stack: AdGuard Home (macvlan DNS), NPM (Proxy/TLS), and Dockhand (UI). |

---

## 🏛️ Local Homelab Stack Architecture

When running the stack using `stacks/homelab-full/compose.yaml`:

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