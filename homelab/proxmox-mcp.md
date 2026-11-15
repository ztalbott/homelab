# Proxmox MCP server

**Category:** Automation

A custom [Model Context Protocol](https://modelcontextprotocol.io/) server, written in Python, that gives an AI assistant a set of tools for managing the lab: reading host and guest status, changing guest configuration, editing firewall rules and running commands. It runs in its own LXC (1 core, 512 MB RAM). Source: [proxmox-mcp-server](https://github.com/ztalbott/proxmox-mcp-server).

## How it is used

- An assistant skill loads the server's memory file at the start of each session, so it starts from the lab's current state instead of rediscovering it
- After any meaningful change, the assistant updates the memory file and the lab's documentation
- Shared conventions are encoded in the skill: default-deny firewall rules, `pct exec` instead of SSH for containers, and a consistent guest numbering scheme

## Setup

- systemd service with streamable-HTTP transport, authenticated, and reachable from the LAN only
- Starts on boot and runs independently of any desktop PC
- Included in the weekly update and backup jobs

## Notes

- The memory file had grown to about 187 KB because every command's output was logged. Logging is now limited to state changes, and the change log is capped at 40 entries with automatic pruning; the file is now around 100 lines.
- Health is checked with `systemctl status` and a TCP port check, since the endpoint expects an MCP handshake rather than a plain GET.
