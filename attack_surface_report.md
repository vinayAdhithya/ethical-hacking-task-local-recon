# Cybersecurity Lab Assessment Report: Local Reconnaissance & Attack Surface Map

**Project Title:** Local Reconnaissance and Attack Surface Map  
**Organization / Course:** EdVyro Cybersecurity Internship  
**Target System:** OWASP Juice Shop (`http://127.0.0.1:3000`)  
**Lead Researcher / Evaluator:** Security Engineering Intern  
**Assessment Date:** 2026-11-12  
**Classification:** Confidential / Authorized Lab Assessment  

---

## 1. Executive Summary

This report documents the results of an authorized, low-impact cybersecurity reconnaissance and attack-surface mapping exercise conducted against an isolated local laboratory target (`127.0.0.1:3000`). The primary objective was to discover exposed network ports, profile running services, evaluate HTTP response headers, enumerate accessible routes and endpoints, identify visible user inputs, and convert all verified observations into a structured attack-surface inventory without executing active attacks, exploitation, or destructive tests.

The assessment successfully discovered an active **OWASP Juice Shop v20.4.0** application running on Node.js/Express bound exclusively to the loopback interface (`127.0.0.1`). Multiple high-value attack surface components were identified, including an exposed **Swagger OpenAPI UI documentation interface** (`/api-docs/`), an unauthenticated **Prometheus metrics telemetry endpoint** (`/metrics`), an exposed **directory listing** (`/ftp`), and several public input vectors across search, authentication, registration, feedback, and file upload workflows.

---

## 2. Scope & Rules of Engagement

All testing was strictly restricted to the authorized loopback target pursuant to the signed **Authorization Statement** and EdVyro internship guidelines:
- **Authorized Target:** `127.0.0.1:3000` (`localhost:3000`).
- **Network Interface:** Loopback adapter (`127.0.0.1`) only.
- **Prohibited Actions:** No external IP scanning, no testing of local network peers, no Denial-of-Service (DoS), no automated credential brute-forcing, and no malicious payload injections.
- **Integrity Guarantee:** Zero simulated or fabricated results. All findings presented in this report are grounded in genuine socket scans, HTTP response observations, and visual evidence.

---

## 3. Target Identification & Technical Environment

| Parameter | Observed Technical Detail |
| :--- | :--- |
| **Application Name** | OWASP Juice Shop |
| **Application Version** | v20.4.0 (Official Release) |
| **Target IP Address** | `127.0.0.1` (IPv4 Loopback) |
| **Target Port** | `3000` (TCP) |
| **Underlying Runtime** | Node.js v22.23.1 / Express Framework |
| **Database Backend** | SQLite3 / Sequelize ORM |
| **Frontend Framework** | Angular (Material Design Theme) |
| **Host Operating System** | Microsoft Windows 11 (Build 10.0.26200) |
| **Target Status** | **Online / Reachable (HTTP 200 OK)** |

---

## 4. Methodology & Tools Used

The assessment followed the standardized NIST SP 800-115 Technical Guide to Information Security Testing and Assessment, adapted for non-destructive reconnaissance:

1. **Target Verification:** Socket-level reachability verification using Python 3.12 network libraries and `curl.exe`.
2. **Port Enumeration:** Conservative TCP connect scanning across standardized ports (1-1024, 1337, 3000, 3306, 5000, 5432, 8000, 8080, 8443, 9000, 27017) strictly against `127.0.0.1`.
3. **Service Fingerprinting:** Non-intrusive banner grabbing and HTTP header inspections.
4. **Web Surface Mapping:** Route discovery, API documentation inspection, and security header audit.
5. **Interactive Element Profiling:** Documenting form fields, input types, and submission methods.
6. **Visual Evidence Gathering:** Automated headless browser capture using Microsoft Edge (`msedge.exe --headless`).

---

## 5. Port Enumeration & Service Profiling Results

A conservative TCP connect scan executed against `127.0.0.1` revealed the following open ports:

| Port | Protocol | State | Service Discovered | Version / Banner Summary |
| :--- | :--- | :--- | :--- | :--- |
| **3000** | TCP | **Open** | `http` (OWASP Juice Shop) | Node.js Express (HTTP/1.1 200 OK) |
| **135** | TCP | **Open** | `msrpc` | Microsoft Windows 11 RPC Endpoint Mapper |
| **445** | TCP | **Open** | `microsoft-ds` | Microsoft Windows SMB over TCP |
| **27017** | TCP | **Open** | `mongodb` | Local Development Daemon Listener |

*Note: Ports 135, 445, and 27017 belong to local Windows operating system and developer services present on the host loopback interface, confirming that only the application on port 3000 represents the target web attack surface.*

---

## 6. HTTP Response Headers & Security Header Audit

Direct HTTP requests to `http://127.0.0.1:3000/` returned the following raw headers:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
X-Recruiting: /#/jobs
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Content-Type: text/html; charset=UTF-8
Content-Length: 10017
Connection: keep-alive
Keep-Alive: timeout=5
```

### Security Header Assessment

| Security Header | Status | Observed Value / Setting | Security Significance |
| :--- | :--- | :--- | :--- |
| **Content-Security-Policy (CSP)** | ❌ **Missing** | *None* | **High Risk:** Without CSP, the browser will execute any injected inline scripts or external scripts, substantially increasing XSS susceptibility. |
| **Access-Control-Allow-Origin** | ⚠️ **Misconfigured** | `*` (Wildcard) | **Medium Risk:** Permissive CORS allows any third-party domain to read API responses if ambient credentials or tokens are exploited. |
| **X-Frame-Options** | ✅ **Present** | `SAMEORIGIN` | Protects application pages from being embedded in cross-origin `<iframe>` tags, mitigating clickjacking. |
| **X-Content-Type-Options** | ✅ **Present** | `nosniff` | Prevents browsers from MIME-sniffing responses away from the declared Content-Type. |
| **Strict-Transport-Security (HSTS)** | ❌ **Missing** | *None* | Cleartext HTTP connection allows man-in-the-middle interception (acceptable in local lab, required in production). |
| **Referrer-Policy** | ❌ **Missing** | *None* | May leak sensitive URL query parameters to external third-party referrers. |
| **Permissions-Policy** | ⚠️ **Outdated** | `Feature-Policy: payment 'self'` | Legacy `Feature-Policy` header used instead of modern standard `Permissions-Policy`. |
| **X-Recruiting** | ℹ️ **Information Disclosure** | `/#/jobs` | Discloses internal application routes directly to scanners. |

---

## 7. Application Routes & Endpoint Discovery

Reconnaissance identified 19 distinct functional routes and API endpoints:

| Route / Endpoint | Type | HTTP Method | Observed Status | Description & Attack Surface Relevance |
| :--- | :--- | :--- | :--- | :--- |
| `/` | Web Root | GET | **200 OK** | Serves baseline Angular SPA container. |
| `/#/login` | Auth | GET (SPA) | **200 OK** | User authentication portal. |
| `/#/register` | Registration | GET (SPA) | **200 OK** | Public account self-registration. |
| `/#/contact` | Feedback | GET (SPA) | **200 OK** | Customer review & rating form with CAPTCHA. |
| `/#/search` | Search | GET (SPA) | **200 OK** | Client-side search interface accepting query string. |
| `/#/score-board` | Gamification | GET (SPA) | **200 OK** | Challenge tracking interface. |
| `/#/photo-wall` | Gallery | GET (SPA) | **200 OK** | Community image wall with file upload feature. |
| `/api-docs/` | Documentation | GET | **200 OK** | **Exposed interactive Swagger UI documentation.** |
| `/api-docs/swagger.json` | API Schema | GET | **200 OK** | **Complete OpenAPI 3.0 schema file.** |
| `/ftp` | File Store | GET | **200 OK** | **Exposed browseable directory listing with downloadable files.** |
| `/metrics` | Telemetry | GET | **200 OK** | **Unauthenticated Prometheus runtime metrics.** |
| `/rest/products/search?q=` | REST API | GET | **200 OK** | Product search query API endpoint. |
| `/rest/captcha` | REST API | GET | **200 OK** | Mathematical CAPTCHA challenge generator. |
| `/api/Users` | REST API | GET | **401 Unauthorized** | Protected user account management endpoint. |
| `/api/Cards` | REST API | GET | **401 Unauthorized** | Protected payment method management endpoint. |
| `/rest/basket/1` | REST API | GET | **401 Unauthorized** | Protected user cart checkout endpoint. |

---

## 8. Visible Input Fields & Interaction Mapping

Every user-facing input field represents a potential entry point for untrusted data. The following inputs were inventoried:

| Input Component | Location / Route | Fields / Parameters | Data Type | Risk Hypothesis | Risk Rating |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Product Search Bar** | `/#/search` | `q` (Query Parameter) | Text | SQL Injection, Reflected XSS | **High** |
| **Login Credentials** | `/#/login` | `email`, `password` | Email / Password | SQLi Authentication Bypass, Brute Force | **High** |
| **User Registration** | `/#/register` | `email`, `password`, `repeatPassword`, `securityQuestion`, `securityAnswer` | Multi-field Form | Mass Assignment, Weak Security Questions | **Medium** |
| **Customer Feedback** | `/#/contact` | `UserId`, `comment`, `rating`, `captchaResult` | Textarea / Slider / Text | Stored XSS, CAPTCHA Bypass | **High** |
| **Photo Wall Upload** | `/#/photo-wall` | `file`, `caption` | Multipart/form-data | Arbitrary File Upload, Web Shell, Path Traversal | **High** |
| **Customer Complaint** | `/#/complain` | `message`, `file` | Textarea / File | XXE Injection (via XML/PDF), Malicious Upload | **High** |

---

## 9. Risk Identification & Potential Security Weaknesses

*Note: In accordance with ethical hacking principles, these findings represent potential security weaknesses identified through reconnaissance; they do not represent active exploit confirmations.*

### Finding RSK-001: Missing Content-Security-Policy (CSP)
- **Severity:** High
- **Observation:** No CSP header was found in any server response.
- **Potential Impact:** Without CSP, the browser cannot distinguish legitimate scripts from injected malicious payloads, greatly amplifying the damage of Cross-Site Scripting (XSS).
- **Remediation:** Implement a strict CSP header: `Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self';`.

### Finding RSK-002: Overly Permissive CORS Policy
- **Severity:** Medium
- **Observation:** `Access-Control-Allow-Origin: *` is returned globally.
- **Potential Impact:** Untrusted third-party sites can read sensitive JSON responses if ambient authentication mechanisms are exploited.
- **Remediation:** Restrict CORS headers to authorized internal origins or remove wildcard reflection on sensitive API routes.

### Finding RSK-003: Public Exposure of API Documentation (`/api-docs/`)
- **Severity:** Medium
- **Observation:** Full Swagger UI and raw OpenAPI schema are accessible to unauthenticated users.
- **Potential Impact:** Attackers gain a comprehensive blueprint of all administrative endpoints, request schemas, parameters, and hidden models without needing to reverse-engineer client code.
- **Remediation:** Disable `/api-docs/` in production or require administrative session authentication.

### Finding RSK-004: Exposed Browseable Directory Listing (`/ftp`)
- **Severity:** High
- **Observation:** Navigating to `/ftp` renders a file index listing containing downloadable legal, markdown, and coupon files.
- **Potential Impact:** Exposure of internal files and potential path traversal vulnerabilities allowing unauthorized download of sensitive server files.
- **Remediation:** Disable directory listing in Express (`serve-index`) and enforce strict whitelist validation on file downloads.

### Finding RSK-005: Unauthenticated Prometheus Metrics Telemetry (`/metrics`)
- **Severity:** Medium
- **Observation:** Real-time application telemetry is exposed publicly at `/metrics`.
- **Potential Impact:** Discloses internal application memory consumption, CPU performance, process uptime, and request counters useful for reconnaissance.
- **Remediation:** Protect `/metrics` with basic authentication or restrict access to internal monitoring IP ranges.

---

## 10. Summary of Security Recommendations

1. **Implement Defense-in-Depth Headers:** Configure comprehensive HTTP security headers including a strict Content-Security-Policy, Referrer-Policy, and Permissions-Policy.
2. **Restrict Administrative & Telemetry Routes:** Enforce access control on `/api-docs/`, `/metrics`, and `/ftp`.
3. **Harden Input Validation & Output Encoding:** Ensure all search and comment parameters are parameterized at the database layer and encoded contextually in the DOM.
4. **Enforce Rate Limiting & Account Protection:** Deploy IP-based request throttling on `/rest/user/login` and CAPTCHA validation across all public forms.
5. **Enforce Secure File Handling:** Validate uploaded images using binary magic bytes and store files outside the public web root.

---

## 11. Limitations of Testing

1. **Reconnaissance Only:** No functional exploit payloads, SQL injection tests, or fuzzing were performed.
2. **Local Scope:** Testing was confined exclusively to `127.0.0.1:3000` to prevent unintended network disruption.
3. **No Credential Testing:** Administrative features were only profiled via their unauthenticated endpoints.

---

## 12. Conclusion

The local reconnaissance exercise successfully cataloged the attack surface of the OWASP Juice Shop target application. The resulting inventory provides a clear, evidence-based architectural map of the application's exposed interfaces, security header posture, accessible routes, and interactive input fields. All observations have been corroborated with genuine screenshots and sanitized execution logs.
