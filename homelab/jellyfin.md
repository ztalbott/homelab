# Jellyfin

**Category:** Media

Jellyfin is an open-source media server that streams the movie and TV library to TVs, phones and browsers on the LAN. It runs in an LXC with 1 core and 512 MB RAM.

## Setup

- The container's root disk on SSD holds only the application and its metadata database
- The media library lives on the HDD and is bind-mounted into the container, so media never uses SSD space
- Web UI on the default port (8096), open to the LAN only

```conf
# /etc/pve/lxc/102.conf (excerpt, sanitized)
mp0: /mnt/hdd_storage/,mp=/media
```

## Notes

- LAN-only by design: the container has no default gateway, so it cannot reach the internet. Online metadata and artwork lookups are traded away for isolation.
- Planned: hardware transcoding through the CPU's integrated GPU (Intel Quick Sync), which needs no extra hardware.
