# Authorised Rules of Engagement & Target Authorization Statement

**Project Title:** Local Reconnaissance and Attack Surface Map  
**Organization / Institution:** EdVyro Cybersecurity Internship  
**Lab Type:** Isolated Local Security Lab  
**Testing Date:** 2026-11-12  

---

## 1. Scope of Authorization

This authorization statement establishes the strict boundaries, rules of engagement, and permitted reconnaissance actions for this lab assignment.

- **Authorized Target:** `127.0.0.1:3000` (`localhost:3000`)
- **Authorized Hostname:** `localhost`
- **Application:** OWASP Juice Shop (or designated local lab web application)
- **Environment:** Strictly isolated local machine loopback interface.

### Permitted Actions:
- Port discovery and state enumeration (TCP connect / low-impact scans) strictly on `127.0.0.1`.
- Service identification and version banner enumeration.
- Non-intrusive HTTP/HTTPS header analysis and status verification.
- Web route, endpoint, and asset identification via passive observation and standard requests.
- Visible form and input field enumeration (forms, query parameters, search inputs).
- Architecture and attack-surface mapping.

### Explicitly Prohibited Actions:
- Scanning or probing any external IP address, domain, or cloud infrastructure.
- Scanning other hosts on the local network or subnet.
- Denial of Service (DoS) or Distributed Denial of Service (DDoS) testing.
- Automated brute-force attacks, password spraying, or credential stuffing.
- Active exploitation, injection of malicious payloads, or weaponized exploits.
- Modification, corruption, or deletion of local system files.
- Fabrication of scan data or simulated evidence.

---

## 2. Target System Details

| Parameter | Specification |
| :--- | :--- |
| **Target Name** | OWASP Juice Shop Local Lab |
| **Target IP Address** | `127.0.0.1` (IPv4 Loopback) |
| **Target Port** | `3000` (TCP) |
| **Operating System** | Windows 11 Home / Pro (Build 10.0.26200) |
| **Local Interface** | Loopback Adapter (`127.0.0.1`) |
| **Authorization Level** | Low-Impact Non-Destructive Reconnaissance Only |

---

## 3. Acknowledgement & Signature

By reviewing and approving this document, the researcher / evaluator confirms that:
1. The target is completely isolated to the local host interface (`127.0.0.1:3000`).
2. No external network resources or third-party devices will be probed or impacted.
3. All operations adhere strictly to ethical hacking standards and EdVyro internship guidelines.

**Student / Lead Researcher:** `[Pending User Confirmation / Signature]`  
**Role:** Cybersecurity Engineering Intern  
**Date:** `2026-11-12`  
**Status:** `AWAITING USER APPROVAL`  
