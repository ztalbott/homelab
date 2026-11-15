# Pi-hole

**Category:** DNS / DHCP

Pi-hole runs in a small LXC (1 core, 256 MB RAM, 2 GB disk) and is the only DNS and DHCP server on the network. The router's DHCP server is turned off.

## Usage

- Network-wide blocking of ads, trackers and telemetry for every device, including ones that can't run a blocker
- DHCP for client devices from a fixed pool; servers use static addresses outside that pool
- Local DNS records so services can be reached by name
- Admin web UI open to the LAN only

## Maintenance

Updated weekly by the host's cron job: `apt upgrade` for the OS, then `pihole -up` for Pi-hole itself.

## Notes

- Running DHCP on Pi-hole rather than the router means the planned firewall cutover does not change how clients get addresses.
- The firewall VM is configured to use Pi-hole as its only upstream DNS, so filtering stays in one place after the cutover.
