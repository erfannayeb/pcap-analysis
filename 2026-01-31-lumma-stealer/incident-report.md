# Incident Report — Lumma Stealer Infection

**Report Date:** 2026-01-31  
**Prepared By:** Erfan Nayeb Aghaei  
**Severity:** Critical  
**Status:** Identified / Pending Remediation

---

## 1. Executive Summary

A SIEM signature for Lumma Stealer victim fingerprinting activity triggered on traffic from an 
internal host to `153.92.1[.]49` over TCP port 80, at 23:05 UTC on 2026-01-27. Lumma Stealer is 
an information-stealing malware capable of exfiltrating browser credentials, cookies, cryptocurrency 
wallets, and other sensitive data. PCAP analysis confirmed the infected host, the user account, and 
the C2 domain.

---

## 2. Affected Asset

| Field            | Value                        |
|------------------|------------------------------|
| IP Address       | `10.1.21.58`              |
| MAC Address      | `00:21:5d:c8:0e:f2`              |
| Hostname         | `DESKTOP-ES9F3ML`              |
| User Account     | `gwyatt`              |
| Full Name        | `Gabriel Wyatt`              |
| Domain           | `win11office.com`            |
| OS               | Windows 11 (AD-joined)       |

---

## 3. Timeline of Events

| Time (UTC)         | Event                                                          |
|--------------------|----------------------------------------------------------------|
| 2026-01-27 23:05   | SIEM alert: Lumma Stealer fingerprinting from `153.92.1[.]49` |
| 2026-01-27 23:05+  | HTTP POST activity and TLS C2 communication observed in PCAP  |

---

## 4. Indicators of Compromise (IOCs)

| Type       | Value                                 |
|------------|---------------------------------------|
| C2 IP      | `153.92.1.49`                       |
| C2 Port    | TCP 80                                |
| C2 Domains  | `holiday-forever.cc AND communicationfirewall-security.cc`|
| Malware    | Lumma Stealer                         |

---

## 5. Analysis Summary

PCAP was filtered to C2 IP traffic to identify the internal source. NBNS and Kerberos filters 
extracted the hostname and username. The C2 domain was uncovered using the filter 
`(http.request or tls.handshake.type == 1) and !(ssdp)`, which surfaces HTTP requests and TLS 
ClientHello SNI fields while removing SSDP noise — a standard technique for hunting suspicious 
domains in AD environment captures.

---

## 6. Threat Intelligence — Lumma Stealer

Lumma Stealer (also known as LummaC2) is a commodity infostealer sold as Malware-as-a-Service (MaaS). 
It targets:
- Browser credentials and cookies
- Cryptocurrency wallets
- 2FA authenticator apps
- FTP/VPN credentials

Data is exfiltrated to attacker-controlled domains over HTTP.

---

## 7. Recommended Actions

- **Isolate** the infected host immediately.
- **Assume credential compromise** — revoke all credentials stored in browsers on this host.
- **Notify affected users** to change passwords for all online accounts, especially banking and email.
- **Check for lateral movement** from this host to others on the `10.1.21.0/24` subnet.
- **Block** `153.92.1[.]49` and the identified C2 domain at the firewall/proxy.
- **Scan** for Lumma persistence (startup folder, scheduled tasks, registry).
- **Escalate** to data breach response team given infostealer capability.
