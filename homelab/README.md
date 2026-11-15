# Homelab

A single Proxmox VE server that runs the home network's core services. Each service gets its own lightweight, unprivileged LXC container so it can be updated, backed up and restored independently. VMs are used only where a full operating system is needed: a Linux desktop and a FreeBSD-based firewall.

## Hardware

| Component | Detail |
|---|---|
| CPU | Intel Core i7-10700K, 8 cores / 16 threads |
| RAM | 16 GB |
| Storage | ~94 GB SSD for the OS and container disks; 1.4 TB HDD for VM disks, backups and media |
| Network | Onboard NIC for the host; dual-port Intel NIC passed through to the firewall VM |

## Guests

| ID | Name | Type | Resources | Role |
|---|---|---|---|---|
| 101 | pihole | LXC | 1 core, 256 MB | DNS + DHCP |
| 102 | jellyfin | LXC | 1 core, 512 MB | Media server |
| 105 | docker | LXC | 2 cores, 2 GB | Docker + Portainer |
| 106 | tailscale | LXC | 1 core, 256 MB | VPN subnet router |
| 107 | proxmox-mcp | LXC | 1 core, 512 MB | MCP server |
| 108 | kuma | LXC | 1 core, 512 MB | Uptime monitoring |
| 103 | downloader | VM | 1 core, 3 GB | Linux Mint desktop, started on demand |
| 104 | mint-template | VM template | 1 core, 2 GB | Base image for desktop VMs |
| 200 | opnsense | VM | 2 cores, 4 GB | Firewall, staged (cutover planned) |

All service containers start on boot. The desktop VM is cloned from the template and only runs when needed.

## Operations

- **Updates:** a weekly cron job on the host. All containers are patched first (`apt upgrade`, plus `pihole -up` on Pi-hole and an image prune on Docker), then the host runs `apt full-upgrade`. Each job writes its own log file. The host script reboots only when the running kernel is older than the newest installed kernel.
- **Backups:** a weekly `vzdump` job covers every guest, in snapshot mode with zstd compression, to the HDD backup pool. It runs an hour before the update window so there is always a fresh restore point before patching. Retention is 7 daily, 4 weekly and 3 monthly copies.
- **Access:** key-only SSH to the host, with password authentication disabled. Containers have SSH disabled and are managed from the host with `pct exec`.
- **Firewall:** the Proxmox firewall is enabled per guest with default-deny inbound rules. Each service opens only the ports it needs, and only to the LAN.
- **Monitoring:** [Uptime Kuma](uptime-kuma.md) checks every service.

## Services

| Service | Category |
|---|---|
| [Proxmox VE](proxmox.md) | Virtualization |
| [Pi-hole](pihole.md) | DNS / DHCP |
| [Jellyfin](jellyfin.md) | Media |
| [Docker + Portainer](docker.md) | Containers |
| [Tailscale](tailscale.md) | Remote access |
| [Uptime Kuma](uptime-kuma.md) | Monitoring |
| [Proxmox MCP server](proxmox-mcp.md) | Automation |
| [OPNsense](opnsense.md) | Firewall / routing (planned) |

## Planned

- Finish the OPNsense cutover, then split the network into VLANs
- Homarr dashboard on the Docker host as a single landing page
- Game server panel (Pterodactyl) on an isolated VLAN
- Jellyfin hardware transcoding with Intel Quick Sync
- Grafana + Prometheus, Gitea, and a local LLM container (Ollama)
