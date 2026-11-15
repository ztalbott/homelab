# Proxmox VE

**Category:** Virtualization

Proxmox VE 9 is the hypervisor for the whole lab. It hosts six LXC containers and three VMs, and handles scheduling for updates and backups. Containers are preferred because they share the host kernel and use a fraction of the memory a VM would.

## Storage

| Pool | Type | Use |
|---|---|---|
| SSD-Fast | lvmthin | Container root disks, firewall VM |
| HDD-VMs | lvmthin | Desktop VM disks, larger containers |
| local | dir | ISOs and container templates |
| HDD-Bulk | dir | Backups |
| hdd_storage | bind mount | Media library, mounted into Jellyfin |

Fast SSD storage holds operating systems and apps; bulk data such as media and backups stays on the HDD.

## Scheduled jobs

| Time (Sunday) | Job |
|---|---|
| 01:00 | `vzdump` backup of all guests |
| 02:00 | Update all containers |
| 02:30 | Update the host; reboot if a newer kernel is installed |

## Example: kernel-aware reboot check (representative)

```bash
# Reboot only if the newest installed kernel is not the one running
running=$(uname -r)
newest=$(ls /boot/vmlinuz-* | sed 's|/boot/vmlinuz-||' | sort -V | tail -n1)
if [ "$running" != "$newest" ]; then
    /sbin/shutdown -r +1 "Rebooting into kernel $newest"
fi
```

## Notes

- Container root disks must live on an lvmthin pool; a dir-type pool cannot hold a container rootfs.
- The reboot check above replaced a flag-file check, which did not reliably trigger after kernel updates.
- Backups run before updates so a bad patch can be rolled back from the previous night's snapshot.
