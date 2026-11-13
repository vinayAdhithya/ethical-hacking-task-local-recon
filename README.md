# Local Reconnaissance and Attack Surface Map

**Cybersecurity Engineering Lab & Assessment Project**  
**Program:** EdVyro Cybersecurity Internship  
**Author / Security Analyst:** Security Engineering Intern  
**Target Host:** `127.0.0.1:3000` (OWASP Juice Shop v20.4.0)  
**Assessment Date:** 2026-11-12  
**Status:** Completed & Verified  

---

## 1. Project Overview & Objective

This repository contains the complete, evidence-based deliverables for the **Local Reconnaissance and Attack Surface Map** cybersecurity laboratory assignment. The objective of this project is to perform authorized, low-impact reconnaissance on an isolated local cybersecurity target and convert the collected observations into a structured attack-surface inventory and architectural map without executing destructive testing, exploitation, or denial-of-service attacks.

---

## 2. Scope & Rules of Engagement

All reconnaissance and enumeration actions strictly adhered to ethical hacking standards and the formal **[Authorization Statement](authorization_statement.md)**:

- **Target IP / Hostname:** `127.0.0.1` (`localhost`)
- **Target Port:** `3000` (TCP)
- **Target Application:** OWASP Juice Shop v20.4.0 (Isolated Local Instance)
- **Interface:** IPv4 Loopback (`127.0.0.1`) only
- **Explicit Exclusions:** No public IP scanning, no testing of local network peers, no Denial-of-Service (DoS), no brute-forcing, and no malicious payload injections.
- **Evidence Integrity:** 100% of observations, port states, headers, routes, and risk ratings are derived from verified technical execution logs and genuine visual evidence.

---

## 3. Environment & Tools Used

### Host Environment
- **Operating System:** Microsoft Windows 11 (Build 10.0.26200)
- **Runtime Environment:** Node.js v22.23.1 / npm 10.9.8
- **Scripting & Automation:** Python 3.12.6 (`socket`, `urllib.request`, `json`)

### Reconnaissance & Documentation Toolset
- **Python Network Socket Suite:** High-precision, conservative TCP connect scanning (`-sT`) and banner grabbing (`-sV`).
- **cURL (`curl.exe`):** Raw HTTP/1.1 response header extraction and status verification.
- **Microsoft Edge Headless (`msedge.exe --headless`):** Automated, genuine visual evidence capture across single-page application routes.
- **Mermaid & Vector Graphics:** Attack-surface architecture diagram generation (`.mmd`, `.svg`, `.png`).
- **Python PDF Engine:** Generation of the formal 21-section cybersecurity assessment report (`attack_surface_report.pdf`).

---

## 4. Methodology & Execution Steps

1. **Pre-Reconnaissance Audit:** Inspected local host for existing web services and Docker availability; verified that port 3000 was initially unoccupied.
2. **Target Isolation & Deployment:** Deployed official OWASP Juice Shop release v20.4.0, patched server socket to bind strictly to `127.0.0.1:3000` (eliminating wildcard `0.0.0.0` exposure), and launched as an active daemon.
3. **Port & Service Enumeration:** Scanned 30+ standardized and web application ports on `127.0.0.1`; verified that port 3000 was open and fingerprinted the Node.js/Express service banner.
4. **HTTP Header Auditing:** Inspected HTTP response headers to evaluate defensive headers (CSP, HSTS, X-Frame-Options, CORS, X-Content-Type-Options).
5. **Route & Endpoint Discovery:** Cataloged 19 distinct routes across public views, Swagger API documentation, unauthenticated Prometheus telemetry, and exposed directory listings.
6. **Interactive Input Identification:** Profiled visible input fields across search, authentication, registration, customer feedback, and file upload forms.
7. **Risk Analysis:** Mapped verified observations to industry-standard risk hypotheses (RSK-001 through RSK-006) with actionable mitigations.
8. **Evidence Archival:** Captured 8 genuine visual screenshots and compiled sanitized execution transcripts.

---

## 5. Results & Findings Summary

### Port & Service Discovery
- **Port 3000/TCP (Open):** OWASP Juice Shop v20.4.0 (Node.js Express / SQLite3).
- **Ports 135 & 445 (Open):** Standard Windows RPC Endpoint Mapper and SMB services (out of target scope).
- **Port 27017 (Open):** Local MongoDB daemon listener (out of target scope).

### Key Security Observations
1. **RSK-001 (High): Missing Content-Security-Policy (CSP)** — Client-side scripts are not restricted by browser policy.
2. **RSK-002 (High): Public Directory Listing (`/ftp`)** — Browseable directory index exposing downloadable documents and potential path traversal risks.
3. **RSK-003 (High): Unrestricted File Upload Vectors** — Photo wall and complaint forms accept multipart file uploads.
4. **RSK-004 (Medium): Exposed Swagger API Documentation (`/api-docs/`)** — Unauthenticated access provides an architectural blueprint of all REST endpoints.
5. **RSK-005 (Medium): Unauthenticated Prometheus Telemetry (`/metrics`)** — Discloses memory usage and runtime statistics.
6. **RSK-006 (Medium): Permissive CORS Configuration (`*`)** — Wildcard access allows cross-origin reading of API responses.

---

## 6. Project Repository Structure

```text
Local_Reconnaissance_Attack_Surface_Map/
├── README.md                          # Project documentation and navigation
├── attack_surface_report.md           # Comprehensive Markdown technical assessment report
├── attack_surface_report.pdf          # Polished 21-section executive PDF report
├── attack_surface_diagram.mmd         # Mermaid source code for architecture diagram
├── attack_surface_diagram.svg         # High-resolution vector architecture diagram
├── attack_surface_diagram.png         # Rendered architecture diagram graphic
├── service_inventory.csv              # Structured 11-column attack surface inventory
├── risk_assessment.csv                # Detailed 8-column security risk register
├── command_log.txt                    # Sanitized step-by-step execution transcript
├── authorization_statement.md         # Formally defined scope and RoE statement
├── final_submission_checklist.md      # Requirement tracking and completion matrix
└── evidence/                          # Genuine visual evidence screenshots
    ├── 01_juice_shop_homepage.png
    ├── 02_juice_shop_login.png
    ├── 03_juice_shop_registration.png
    ├── 04_juice_shop_contact_feedback.png
    ├── 05_juice_shop_search_interface.png
    ├── 06_juice_shop_swagger_api_docs.png
    ├── 07_juice_shop_ftp_directory.png
    └── 08_juice_shop_metrics_endpoint.png
```

---

## 7. How to Reproduce Permitted Checks

To reproduce the non-intrusive reconnaissance against the running target:

1. **Verify Target Reachability:**
   ```powershell
   curl.exe -i http://127.0.0.1:3000/
   ```
2. **Execute TCP Connect Scan:**
   ```powershell
   python scratch/execute_recon.py
   ```
3. **Inspect Exposed Endpoints:**
   ```powershell
   curl.exe -s http://127.0.0.1:3000/api-docs/
   curl.exe -s http://127.0.0.1:3000/ftp
   curl.exe -s http://127.0.0.1:3000/metrics
   ```

---

## 8. Verification & Submission Link

- **Deliverables Status:** 100% Completed  
- **Requirement Verification Matrix:** See [final_submission_checklist.md](final_submission_checklist.md)  
- **Sanitized Log Verification:** See [command_log.txt](command_log.txt)  
- **Generated PDF Report:** See [attack_surface_report.pdf](attack_surface_report.pdf)  
