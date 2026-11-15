# Docker + Portainer

**Category:** Containers

An LXC with 2 cores and 2 GB RAM runs Docker Engine, with Portainer as the web UI for deploying and managing containers and Compose stacks. It is the general-purpose host for apps that ship as Docker images.

## Usage

- Deploy and update apps as Compose stacks from Portainer
- View container logs, resource use and restart status in one place
- Portainer UI over HTTPS (port 9443), open to the LAN only

## Maintenance

Weekly `apt upgrade` for the OS, followed by `docker system prune` to clear unused images and layers.

## Notes

- Running Docker inside an LXC rather than a VM keeps memory use low while still isolating it from the host.
- Planned: Homarr dashboard as a single landing page with a tile for every service.
- Planned: Pterodactyl game server panel, also Docker-based, once VLANs are in place.
