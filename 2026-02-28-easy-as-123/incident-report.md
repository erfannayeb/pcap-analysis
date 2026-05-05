# Incident Report — NetSupport Manager RAT Infection

**Report Date:** 2026-02-28  
**Prepared By:** Erfan Nayeb Aghaei 
**Severity:** High  
**Status:** Identified / Pending Remediation

---

## 1. Executive Summary

A SIEM alert triggered for NetSupport Manager RAT activity originating from internal LAN segment 
`10.2.28.0/24`. Traffic was directed to the known C2 address `45.131.214[.]85` over TCP port 443, 
beginning at 19:55 UTC. PCAP analysis confirmed the identity of the infected host and the associated user account.

---

## 2. Affected Asset

| Field            | Value                        |
|------------------|------------------------------|
| IP Address       | `10.2.28.88`              |
| MAC Address      | `00:19:d1:b2:4d:ad`              |
| Hostname         | `DESKTOP-TEYQ2NR`              |
| User Account     | `brolf`              |
| Full Name        | `Becka Rolf`              |
| Domain           | `easyas123.tech`             |
| OS               | Windows (AD-joined)          |

---

## 3. Timeline of Events

| Time (UTC)   | Event                                                       |
|--------------|-------------------------------------------------------------|
| 19:55        | First observed C2 communication to `45.131.214[.]85:443`   |
| ~19:55+      | Sustained RAT beaconing activity detected in PCAP           |

---

## 4. Indicators of Compromise (IOCs)

| Type       | Value                    |
|------------|--------------------------|
| C2 IP      | `45.131.214[.]85`        |
| C2 Port    | TCP 443                  |
| Malware    | NetSupport Manager RAT   |

---

## 5. Analysis Summary

PCAP was filtered to traffic involving the C2 IP (`ip.addr == 45.131.214.85`), isolating the internal 
source. NBNS broadcast traffic (`nbns` filter) confirmed the machine's hostname. Kerberos authentication 
packets (`kerberos.CNameString`) identified the logged-in user account. Additional DC traffic was reviewed 
to retrieve the user's full display name.

---

## 6. Recommended Actions

- **Isolate** the infected host from the network immediately.
- **Revoke** the compromised user's credentials and force a password reset.
- **Scan** the host for persistence mechanisms (scheduled tasks, registry run keys).
- **Review** other hosts in the subnet for lateral movement indicators.
- **Block** `45.131.214[.]85` at the perimeter firewall.
- **Preserve** the PCAP and host image for forensic review.
