# OPNsense

**Category:** Firewall / routing · **Status:** installed, cutover planned

OPNsense 26.7 is an open-source, FreeBSD-based firewall and router. It runs as a Proxmox VM (2 cores, 4 GB RAM, 20 GB SSD) with both ports of a dual-port Intel NIC passed through directly, one for WAN and one for LAN. It is installed and configured on a staging address, and is not yet the network gateway.

## Done

- Enabled IOMMU on the host (`intel_iommu=on iommu=pt`); each NIC port sits in its own IOMMU group and is bound to `vfio-pci`
- Built the VM on the q35 machine type with both ports passed through
- Completed first-run setup: Pi-hole set as the only DNS server, the built-in Unbound resolver disabled, private and bogon networks blocked on WAN, IPv6 off, DHCP left on Pi-hole

```conf
# /etc/pve/qemu-server/200.conf (excerpt, sanitized)
machine: q35
cores: 2
memory: 4096
hostpci0: 02:00.0
hostpci1: 02:00.1
```

## Planned

- Firewall rules, including a rule that forces all LAN DNS traffic to Pi-hole
- Cutover: OPNsense becomes the gateway, the current router moves to access-point mode, DHCP stays on Pi-hole
- VLANs, including an isolated segment for game servers
- Decide whether to keep it virtualized or move it to dedicated hardware, since the firewall currently depends on the host being up

## Notes

- OPNsense must run as a VM, not an LXC, because it is FreeBSD-based.
- A leftover virtio NIC on the same subnet as the passed-through ports caused intermittent "Destination Host Unreachable" errors. Removing it fixed connectivity.
- Passing through a physical NIC keeps WAN traffic off the Proxmox bridge entirely.
