# 🔐 Pi-hole + Cloudflared + Traefik Setup

This project sets up **Pi-hole** (a network-wide ad blocker) with **Cloudflared DNS over HTTPS (DoH)** and **Traefik reverse proxy** using Docker Compose. It includes internal networking, secure DNS resolution, and HTTPS access to the Pi-hole dashboard.

---

## 🧩 Services

### ✅ `cloudflared` (DNS over HTTPS)

Cloudflared acts as a local DNS proxy that encrypts DNS queries and forwards them to Cloudflare and other trusted DNS resolvers.

**Configuration highlights:**
- Forwards DNS queries to:
  - `https://1.1.1.1/dns-query`
  - `https://1.0.0.1/dns-query`
  - `https://9.9.9.9/dns-query`
  - `https://149.112.112.9/dns-query`
- Listens on:
  - IP: `0.0.0.0`
  - Port: `5053`
- Internal IP: `172.70.9.2`

---

### ✅ `pihole` (Ad Blocking DNS Server)

Pi-hole receives DNS queries and forwards them to cloudflared (DoH), optionally integrating with DHCP and the Traefik reverse proxy.

**Key features:**
- Web UI available at `http://pihole.yourdomain.uk` (with HTTPS via Traefik)
- Uses cloudflared on `172.70.9.2#5053` as upstream DNS
- Volumes persist Pi-hole config and blocklists
- `.env` file stores secrets like the web password

**Volumes:**
- `/home/sanju/docker/pihole/:/etc/pihole/`
- `/home/sanju/docker/pihole/etc-dnsmasq.d/:/etc/dnsmasq.d/`

---

## 🌐 Networking

Two Docker networks are used:

### `pihole_internal`
Custom internal network for Pi-hole and cloudflared:

```yaml
subnet: 172.70.9.0/29
