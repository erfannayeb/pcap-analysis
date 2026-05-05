# Incident Report — Malvertising via Fake Google Authenticator Page

**Report Date:** 2025-01-22
**Prepared By:** Erfan Nayeb Aghaei
**Severity:** Critical
**Status:** Identified / Pending Remediation

---

## 1. Executive Summary

A user at an organization reported downloading a suspicious file after searching for
Google Authenticator online. Investigation confirmed the user was redirected via a malicious
advertisement to a spoofed Google Authenticator download page hosted at
`google-authenticator.burleson-appliance[.]net`. The downloaded file was malware that
established communication with multiple C2 servers. PCAP analysis confirmed the infected
host, the user account, and all associated indicators of compromise.

This incident follows the malvertising pattern documented by Unit 42 on 2025-01-22, in which
threat actors purchased search ads to redirect victims searching for legitimate software to
attacker-controlled lookalike domains.

---

## 2. Affected Asset

| Field | Value |
|---|---|
| IP Address | `10.1.17.215` |
| MAC Address | `00:d0:b7:26:4a:74` |
| Hostname | `DESKTOP-L8C5GSJ` |
| User Account | `shutchenson` |
| Full Name | Could not be determined from PCAP |
| Domain | `bluemoontuesday.com` |
| OS | Windows (AD-joined) |

---

## 3. Timeline of Events

| Time (UTC) | Event |
|---|---|
| Pre-infection | User searched for "Google Authenticator" online |
| Pre-infection | Malicious ad redirected user to fake download page |
| During infection | User downloaded and executed malicious installer from `google-authenticator.burleson-appliance[.]net` |
| Post-execution | Malware beaconed to C2 infrastructure across multiple IPs |

---

## 4. Indicators of Compromise (IOCs)

### Malicious Domains

| Domain | Role |
|---|---|
| `google-authenticator.burleson-appliance[.]net` | Fake Google Authenticator download page |
| `authenticatoor[.]org` | C2 / malware delivery (typosquat domain) |

### C2 IP Addresses

| IP Address | Port | Role |
|---|---|---|
| `5.252.153.241` | TCP 80 | C2 server |
| `45.125.66.32` | TCP 80 | C2 server |
| `45.125.66.252` | TCP 80 | C2 server |

### Host Artifacts

| Type | Value |
|---|---|
| Internal IP | `10.1.17.215` |
| MAC Address | `00:d0:b7:26:4a:74` |
| Hostname | `DESKTOP-L8C5GSJ` |
| Username | `shutchenson` |

---

## 5. Attack Chain

```
User searches for "Google Authenticator"
        |
        v
Malicious search ad (malvertising)
        |
        v
Fake download page: google-authenticator.burleson-appliance[.]net
        |
        v
User downloads and runs a malicious installer
        |
        v
Malware executes on DESKTOP-L8C5GSJ (10.1.17.215)
        |
        v
C2 beaconing to 5.252.153.241 / 45.125.66.32 / 45.125.66.252
```

---

## 6. Analysis Summary

PCAP was filtered using `ip.addr == 5.252.153.241` to confirm the internal source host. NBNS
broadcast traffic (`nbns` filter) identified the machine hostname as `DESKTOP-L8C5GSJ`. Kerberos
authentication packets (`kerberos.CNameString`) revealed the logged-in user as `shutchenson`.
The full display name could not be determined from available PCAP data.

The fake domain and additional C2 domains were identified using the filter:
`(http.request or tls.handshake.type == 1) and !(ssdp)`

This technique exposes both plaintext HTTP destinations and TLS Server Name Indication (SNI)
fields from ClientHello packets, allowing domain identification even within encrypted sessions.
The typosquat domain `authenticatoor[.]org` (note the double 'o') is a common social engineering
technique used to evade casual detection.

---

## 7. Threat Context — Malvertising

Malvertising (malicious advertising) is a technique where attackers purchase legitimate ad
placements on search engines to redirect users searching for real software to lookalike
malware delivery pages. Key characteristics of this attack:

- **Search poisoning via ads:** The victim was not compromised by a drive-by download or
  phishing email — they were actively searching for legitimate software.
- **Typosquatting:** `authenticatoor[.]org` uses a doubled letter to evade user scrutiny.
- **Lookalike domain:** `burleson-appliance[.]net` masquerades as a trusted subdomain path.
- **Multiple C2 nodes:** Three separate C2 IPs suggest redundancy or staged payload delivery.

---

## 8. Recommended Actions

- **Isolate** `DESKTOP-L8C5GSJ` (`10.1.17.215`) from the network immediately.
- **Revoke** credentials for `shutchenson` and force a password reset across all platforms.
- **Assume data theft** — treat all credentials and sensitive data on this machine as compromised.
- **Block** all identified C2 IPs and domains at the firewall and DNS level:
  - `5.252.153.241`, `45.125.66.32`, `45.125.66.252`
  - `google-authenticator.burleson-appliance[.]net`
  - `authenticatoor[.]org`
- **Scan** the host for persistence mechanisms (startup entries, scheduled tasks, registry run keys).
- **Alert users** organization-wide about malvertising risks when downloading software — always
  navigate directly to official vendor sites rather than clicking search ads.
- **Submit IOCs** to threat intel platforms (VirusTotal, AbuseIPDB) if not already present.
- **Review** DNS and proxy logs for other internal hosts that may have visited the fake domain.

---

## 9. References

- [Unit 42 LinkedIn Post](https://www.linkedin.com/posts/unit42_2025-01-22-wednesday-a-malicious-ad-led-activity-7288213662329192450-ky3V/)
- [Unit 42 X/Twitter Post](https://x.com/Unit42_Intel/status/1882448037030584611)
- [malware-traffic-analysis.net exercise](https://malware-traffic-analysis.net/2025/01/22/index.html)
