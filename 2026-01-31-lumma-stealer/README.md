# Exercise: Lumma in the Room-ah — Lumma Stealer

**Date:** 2026-01-31  
**Source:** [malware-traffic-analysis.net](https://malware-traffic-analysis.net/2026/01/31/index.html)  
**Malware:** Lumma Stealer  
**C2 IP:** 153.92.1[.]49 over TCP port 80

## Scenario
A SIEM alert fired for "ET MALWARE Lumma Stealer Victim Fingerprinting Activity" from an internal host. 
I retrieved the PCAP and identified both the infected machine and the C2 domain used by the stealer.

## Environment
- LAN: `10.1.21.0/24`
- Domain: `win11office.com`
- AD Controller: `10.1.21.2 - WIN-LU4L24X3UB7`

## Analysis Methodology

### 1. Identify Infected IP
Filter: `ip.addr == 153.92.1.49`  
Isolated the internal host communicating with the C2 IP.

### 2. Find Hostname (NBNS)
Filter: `nbns`

### 3. Find Username (Kerberos)
Filter: `kerberos.CNameString`

### 4. Find Suspicious C2 Domain
Filter: `(http.request or tls.handshake.type == 1) and !(ssdp)`  
This surfaces all HTTP requests and TLS ClientHello packets (which contain the SNI — Server Name 
Indication), excluding noisy SSDP traffic. The Lumma Stealer C2 domain was identified in these results.

## Screenshots
See [Screenshots](./screenshots) for full screenshots.

## Findings
See [incident-report.md](./incident-report.md) for full details.
