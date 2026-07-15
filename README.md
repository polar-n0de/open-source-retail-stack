# Open Source Retail Stack

Full open-source IT infrastructure for small retail businesses.
Deployed on a 12-year-old HP desktop with zero licensing cost.

## Stack

| Service | Purpose | Port |
|---|---|---|
| Proxmox VE | Hypervisor | 8006 |
| URBackup | Bare metal recovery | 55414 |
| Prometheus | Metrics collection | 9090 |
| Grafana | Dashboards | 3000 |
| Samba | File sharing | 445 |
| Tailscale | WireGuard VPN | - |
| Wazuh | SIEM + vulnerability detection | 443 |
| Ansible | Day-2 automation | - |
| Netdata | Lightweight endpoint monitoring | 19999 |

## Hardware

- HP EliteDesk 800 G1 TWR
- Intel Core i5-4570 / 24GB RAM
- SSD 256GB (Proxmox OS)
- SSD 1TB (LXC volumes — ZFS)
- HDD 3TB (URBackup repo + Samba — ZFS)

## Architecture

'''
[IPROXMOX HOST
├── LXC 100 · URBackup      192.168.1.x
├── LXC 101 · Prometheus    192.168.1.x
├── LXC 102 · Samba         192.168.1.x
├── LXC 103 · Tailscale     192.168.1.x
├── LXC 104 · Ansible       192.168.1.x
└── LXC 105 · Wazuh         192.168.1.x
EXTERNAL
├── TPV-1 · Netdata + URBackup client
└── TPV-2 · Netdata + URBackup client

'''

## Automation (Ansible)

Day-2 operations only. Initial deployment was manual and documented.

| Playbook | Purpose |
|---|---|
| update.yml | apt update + upgrade all LXCs |
| wazuh-agent.yml | Deploy Wazuh agents |
| kernel-hardening.yml | CVE mitigations (module blacklisting) |
| pve-exporter.yml | Prometheus PVE exporter |

## Security

- No service runs as root unless required
- SSH key authentication only
- Dedicated service users per LXC
- Wazuh agents on all nodes
- Kernel CVE mitigations via Ansible
- Remote access via Tailscale VPN only

## Cost

- Hardware: existing
- Licensing: zero
- All tools: open-source

## License

MIT
