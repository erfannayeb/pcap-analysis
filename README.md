# PCAP Traffic Analysis Exercises

A collection of network forensics exercises from [malware-traffic-analysis.net](https://malware-traffic-analysis.net),
analyzed using Wireshark. Each exercise simulates a real SOC analyst workflow: identifying an
infected host, extracting user/device information from network traffic, and writing a formal
incident report.

## Tools Used

- Wireshark
- Display filters: `nbns`, `kerberos.CNameString`, `ip.addr`, `(http.request or tls.handshake.type == 1) and !(ssdp)`

---

## Exercises

| # | Date | Title | Malware / Threat |
|---|------|--------|-----------------|
| 1 | 2026-02-28 | [Easy As 123](./2026-02-28-easy-as-123/) | NetSupport Manager RAT |
| 2 | 2026-01-31 | [Lumma in the Room-ah](./2026-01-31-lumma-stealer/) | Lumma Stealer (infostealer) |
| 3 | 2025-01-22 | [Download from Fake Software Site](./2025-01-22-fake-software-site/) | Malvertising → infostealer |

---

## Skills Demonstrated

- Identifying infected hosts via IP and MAC address analysis
- Extracting hostnames using NBNS protocol filters
- Recovering usernames via Kerberos (`kerberos.CNameString`)
- Identifying C2 and malicious domains using HTTP/TLS handshake filters (SNI extraction)
- Recognizing malvertising and typosquatting attack patterns
- Writing SOC-style incident reports with IOC tables and attack chain diagrams

---

## Repository Structure

```
pcap-analysis-exercises/
│
├── README.md
│
├── 2026-02-28-easy-as-123/
│   ├── README.md
│   ├── incident-report.md
│   └── screenshots/
│
├── 2026-01-31-lumma-stealer/
│   ├── README.md
│   ├── incident-report.md
│   └── screenshots/
│
└── 2025-01-22-fake-software-site/
    ├── README.md
    ├── incident-report.md
    └── screenshots/
```

> **Note:** PCAP files are not included in this repository as they contain live malware traffic.
> All exercises are sourced from [malware-traffic-analysis.net](https://malware-traffic-analysis.net).
