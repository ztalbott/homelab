# Tailscale

**Category:** Remote access

Tailscale is a WireGuard-based mesh VPN. A dedicated LXC (1 core, 256 MB RAM) acts as a subnet router: it advertises the home LAN to the tailnet, so every service is reachable from a laptop or phone anywhere, without forwarding any ports on the router.

## Setup

```bash
# Representative example, run inside the container
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.d/99-tailscale.conf
sysctl -p /etc/sysctl.d/99-tailscale.conf
tailscale up --advertise-routes=<lan-subnet>/24
```

The advertised route is then approved in the Tailscale admin console.

## Notes

- One subnet router covers the whole LAN, so individual services don't need their own Tailscale client.
- The firewall blocks ping to this container from the LAN on purpose; failed pings are not an outage.
- Planned: Tailscale Funnel to expose the MCP server to a cloud-hosted AI client.
