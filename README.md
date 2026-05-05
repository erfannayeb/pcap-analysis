# PCAP Traffic Analysis Exercises

A collection of network forensics exercises from [malware-traffic-analysis.net](https://malware-traffic-analysis.net), 
analyzed using Wireshark. Each exercise simulates a real SOC analyst workflow: identifying an infected host, 
extracting user/device information from network traffic, and writing a formal incident report.

## Tools Used
- Wireshark
- Display filters: `nbns`, `kerberos.CNameString`, `http.request`, `tls.handshake.type == 1`

## Exercises

| # | Date | Title | Malware |
|---|------|--------|---------|
| 1 | 2026-02-28 | [Easy As 123](./2026-02-28-easy-as-123/) | NetSupport Manager RAT |
| 2 | 2026-01-31 | [Lumma in the Room-ah](./2026-01-31-lumma-stealer/) | Lumma Stealer |

## Skills Demonstrated
- Identifying infected hosts via IP/MAC address analysis
- Extracting hostnames using NBNS protocol filters
- Recovering usernames via Kerberos (`kerberos.CNameString`)
- Identifying C2 domains using HTTP/TLS handshake filters
- Writing SOC-style incident reports
