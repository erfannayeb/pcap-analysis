# Exercise: Easy As 123 — NetSupport Manager RAT

**Date:** 2026-02-28  
**Source:** [malware-traffic-analysis.net](https://malware-traffic-analysis.net/2026/02/28/index.html)  
**Malware:** NetSupport Manager RAT  
**C2 IP:** 45.131.214[.]85 over TCP port 443

## Scenario
SIEM alerts fired for NetSupport Manager RAT activity from an external IP. As the SOC analyst, 
I retrieved the PCAP and identified the infected internal host.

## Environment
- LAN: `10.2.28.0/24`
- Domain: `easyas123.tech`
- AD Controller: `10.2.28.2 - EASYAS123-DC`

## Analysis Methodology

### 1. Identify Infected IP
Filter: `ip.addr == 45.131.214.85`  
Identified the internal source IP communicating with the C2 server.

### 2. Find Hostname (NBNS)
Filter: `nbns`  
NBNS broadcasts reveal the NetBIOS hostname of the infected machine.

### 3. Find Username (Kerberos)
Filter: `kerberos.CNameString`  
Kerberos authentication packets expose the AD username.

### 4. Find Full Name
Filter: `Rolf`  
Cross-referenced DC traffic to extract the full display name.

## Screenshots
See [Screenshots](./screenshots) for full screenshots

## Findings
See [incident-report.md](./incident-report.md) for full details.
