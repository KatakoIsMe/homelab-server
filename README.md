# homelab-server
Self-hosted home server running Ubuntu Server and Docker microservices accessed through Tailscale.

---

## Hardware & Operating System Specifications

| Component | Specifications |
| :--- | :--- |
| **CPU** | Intel Core i5-6400 (4 Cores @ 2.70GHz) |
| **RAM** | 20GB DDR4 (Optimized via 8GB Swap Partition & `swappiness=10`) |
| **Boot Drive** | 232GB HDD (EXT4 & LVM Volume Management) |
| **Storage Pool** | 1TB HDD (EXT4, auto-mounted at `/mnt/storage` via `/etc/fstab`) |
| **Network** | Wired CAT7 Gigabit Ethernet on 100Mbps Fiber Line |
| **OS** | Headless Ubuntu Server 26.04 LTS (Resolute Raccoon) |

---

## Network & System Optimizations

* **Gigabit Network Tuning:** Configured Google BBR (`net.ipv4.tcp_congestion_control=bbr`), forced 1492 MTU, and disabled Ethernet EEE power-saving to maximize fiber throughput.
* **Power & Keep-Alive Rules:** Created custom `/etc/udev/rules.d/70-wifi-powersave.rules` and kernel parameters (`usbcore.autosuspend=-1`) to prevent interface sleep loops.
* **Hardened Access:** Configured OpenSSH server with `sudoers` administrative privileges, backed by `fail2ban` brute-force IP jailing.
* **Zero-Trust Remote VPN:** Integrated **Tailscale Mesh VPN** (`100.x.y.z` overlay) for encrypted remote access without opening dangerous router ports.

```mermaid
graph TD
    Internet([Internet]) --> Router[Router / Gateway]
    Router --> Firewall[UFW Firewall]
    Firewall --> Nginx[Nginx Reverse Proxy]
    Nginx --> Dashboard[Homepage / Dashboard]
    Nginx --> Pihole[Pi-hole DNS]
    Nginx --> Grafana[Grafana / Prometheus]

## Containerized Services (Docker Stack via Portainer)

### 1. FileBrowser (Family Cloud NAS)
* Web-based file management interface mapped to `/mnt/storage`.
* Configured 30-day persistent sessions (`--token-expiration 720`) and mobile PWA home-screen shortcuts for non-technical family users.

### 2. Samba (SMB Shares)
* Exposes `/mnt/storage` as `[HomeCloud]` across local LAN and Tailnet.
* Seamless network drive integration (`Z:\`) for Windows File Explorer and Linux Mint (`smb://`).

### 3. AdGuard Home (Network-Wide DNS Blocker)
* Intercepts DNS queries on Port 53; multi-upstream failover (Cloudflare, Google, Quad9) with disabled IPv6 resolution to eliminate DNS leaks.

---


