
# Architecture

## Overview

Single-node Proxmox VE hypervisor running six LXC containers.
All services are open-source. Zero licensing cost.

## Storage Layout

| Device | Pool | Usage |
|---|---|---|
| SSD 256GB (sda) | Proxmox OS | Host operating system |
| SSD 1TB (sdc) | DATOS (ZFS) | LXC volumes |
| HDD 3TB (sdb) | TOSHIBA3TB (ZFS) | URBackup repo + Samba data |

## LXC Containers

| ID | Hostname | IP | RAM | Disk | Privileged |
|---|---|---|---|---|---|
| 100 | urbackup | 192.168.1.x | 2GB | 20GB | Yes |
| 101 | prometheus | 192.168.1.x | 1GB | 20GB | No |
| 102 | samba | 192.168.1.x | 1GB | 10GB | Yes |
| 103 | tailscale | 192.168.1.x | 512MB | 8GB | No |
| 104 | ansible | 192.168.1.x | 512MB | 8GB | No |
| 105 | wazuh | 192.168.1.x | 8GB | 50GB | No |

## Notes on privileged containers

- URBackup (100): privileged required for bindmount to ZFS pool
- Samba (102): privileged required for correct file permissions on bindmount

## Network

Flat LAN. All services on 192.168.1.0/24.
External access exclusively via Tailscale VPN (WireGuard).
No ports exposed to the internet.

## Monitoring flow
```
Netdata (Proxmox host:19999) ──┐
Netdata (TPV-1:19999)        ──┤
Netdata (TPV-2:19999)        ──┼──> Prometheus (LXC 101:9090) ──> Grafana (LXC 101:3000)
PVE Exporter (LXC 101:9221)  ──┤
LXC metrics via PVE API      ──┘
(urbackup, prometheus, samba,
tailscale, ansible, wazuh)
```
## Security

- Wazuh agents on all LXCs (file integrity + vulnerability detection)
- SSH key authentication only
- No service runs as root unless required
- Dedicated service users per LXC
- Remote access via Tailscale only
- Kernel CVE mitigations applied via Ansible

