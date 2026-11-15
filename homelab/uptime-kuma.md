# Uptime Kuma

**Category:** Monitoring

Uptime Kuma is a self-hosted uptime monitor. Version 2.5 runs in an LXC (1 core, 512 MB RAM) under PM2 as a dedicated, unprivileged service user, with a systemd unit so it starts on boot.

## Monitors

| Target | Check type |
|---|---|
| Proxmox host | Ping |
| Proxmox web UI | HTTP (port 8006) |
| Pi-hole admin | HTTP |
| Jellyfin | HTTP (port 8096) |
| Portainer | HTTP (port 9443) |
| Tailscale container | Reachability |
| MCP server | TCP port (8000) |

## Setup

Cloned from GitHub and pinned to a stable release tag, built with `npm run setup`, then started under PM2.

```bash
# representative example
git clone https://github.com/louislam/uptime-kuma.git /opt/uptime-kuma
cd /opt/uptime-kuma && git checkout 2.5.0 && npm run setup
pm2 start server/server.js --name uptime-kuma
pm2 save
```

## Notes

- The MCP server uses a TCP check because it expects an MCP handshake and does not answer a plain HTTP GET.
- The web UI (port 3001) is open to the LAN only.
- Included in the weekly patch cron and the weekly backup job.
