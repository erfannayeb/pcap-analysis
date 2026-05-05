# Exercise: Download from Fake Software Site — Malvertising + Infostealer

**Date:** 2025-01-22
**Source:** [malware-traffic-analysis.net](https://malware-traffic-analysis.net/2025/01/22/index.html)
**Malware:** Infostealer delivered via fake Google Authenticator page (malvertising)
**C2 IPs:** `5.252.153.241`, `45.125.66.32`, `45.125.66.252`

---

## Scenario

A coworker reported downloading a suspicious file after searching for Google Authenticator online.
As the SOC analyst, I retrieved the PCAP and confirmed an infection had occurred. The infection
vector was a malicious ad leading to a spoofed Google Authenticator download page.

This exercise was also covered by Unit 42:
- [LinkedIn post](https://www.linkedin.com/posts/unit42_2025-01-22-wednesday-a-malicious-ad-led-activity-7288213662329192450-ky3V/)
- [X/Twitter post](https://x.com/Unit42_Intel/status/1882448037030584611)

---

## Environment

| Field | Value |
|---|---|
| LAN Range | `10.1.17.0/24` |
| Domain | `bluemoontuesday.com` |
| AD Environment | `BLUEMOONTUESDAY` |
| Domain Controller | `10.1.17.2 - WIN-GSH54QLW48D` |
| Gateway | `10.1.17.1` |

---

## Analysis Methodology

### 1. Identify Infected IP
Filter: `ip.addr == 5.252.153.241`
Isolated the internal host communicating with the known C2 address.

### 2. Find Hostname (NBNS)
Filter: `nbns`
NBNS broadcast traffic revealed the NetBIOS hostname of the infected machine.

### 3. Find Username (Kerberos)
Filter: `kerberos.CNameString`
Kerberos authentication packets exposed the Active Directory user account name.

### 4. Find Fake Domain & C2 Domains
Filter: `(http.request or tls.handshake.type == 1) and !(ssdp)`
This filter surfaces all HTTP requests and TLS ClientHello packets, which carry the
Server Name Indication (SNI) field revealing the destination domain — even for encrypted
traffic. Removing SSDP reduces noise from Windows background traffic.

Two suspicious domains were identified:
- `google-authenticator.burleson-appliance[.]net` — fake Google Authenticator download page
- `authenticatoor[.]org` — C2 / malware delivery domain (typosquat)

---

## Screenshots

| File | What it shows |
|---|---|
| `01-c2-ip-filter.png` | Internal host communicating with C2 IP |
| `02-nbns-hostname.png` | NBNS filter revealing hostname |
| `03-kerberos-username.png` | `kerberos.CNameString` showing user account |
| `04-http-tls-filter.png` | HTTP/TLS filter showing fake domain and C2 domains |

---

## Findings

See [incident-report.md](./incident-report.md) for the full incident report.
