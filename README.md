# PureSecure – Software Assurance System

PureSecure is a three‑tier secure web application developed for the ELE8094 Software Assurance module. It integrates the MITRE CWE dataset with real‑time CVE data from the NIST National Vulnerability Database (NVD) to provide actionable insights into the software threat landscape. The system demonstrates secure design, secure implementation, and DevSecOps practices.

---

## 1. System Overview

PureSecure follows the User → System → CWE architecture required in the assignment brief. It is built as a three‑tier system:

### Tier 1: Client (Frontend)
- HTML/CSS/JavaScript Single Page Application (SPA)
- Pages: Login, Signup, Home, Search, Browse, CVE, Statistics
- JWT stored in localStorage and attached to every API request
- Centralised `fetchAPI()` wrapper handles:
  - JWT injection
  - 10‑second timeout
  - 401 handling (auto‑logout)
- Fail‑secure behaviour: no data loads unless a valid token is present

### Tier 2: Application (Backend)
- Python FastAPI backend
- Authentication routes: `/auth/login`, `/auth/signup`, `/auth/logout`
- CWE routes: serve normalised CWE data from MITRE XML dataset
- CVE routes: proxy real‑time CVE lookups from NVD API
- Pydantic validation for strict input checking

### Tier 3: Data
- MITRE CWE XML dataset (local)
- NVD REST API (external)
- Trust boundary enforced between frontend and backend

---

## 2. Core Functionality

### CWE Search
Search by CWE ID, name, or description (e.g., “CWE‑778”).

### CWE Browse
Filter by:
- Severity (Critical, High, Medium, Low)
- Category keywords  
Supports risk‑based triage.

### CVE Lookup
- Real‑time NVD integration
- Returns CVSS score, affected CPEs, references, and publication dates
- Includes service‑health check before querying

### Statistics Dashboard
Aggregates dataset‑wide metrics:
- 969 total CWEs
- 81 Critical
- 87 High
- 801 Medium
- 15 categories

### Insights Beyond CWE Website
1. Dataset‑level severity analytics  
2. CWE ↔ CVE correlation  
3. Risk‑prioritised browsing workflow  

---

## 3. Secure Design Justification

### JWT Stateless Authentication
- Backend issues signed JWTs on login
- Frontend attaches token to every request
- Backend verifies signature on each call
- No server‑side session state → reduced attack surface

### Fail‑Secure Defaults
- Missing token → redirect to login
- 401 response → token cleared and user logged out

### Separation of Concerns
- Authentication logic isolated in `auth-common.js` and `/auth/` routes
- Data logic isolated in `/cwe/` and `/cve/` routes
- Rendering logic isolated in `script.js`

### Trade‑offs
- JWT in localStorage is vulnerable to XSS
- Acceptable for localhost prototype
- Production would use HttpOnly, Secure cookies

---

## 4. Secure Implementation Justification

### Input Validation (CWE‑20)
Frontend regex validation for CVE IDs:
Backend Pydantic validation provides a second layer.

## 5. DevSecOps Process
Version Control
GitLab repository with structured commits
Feature‑branch workflow
Traceability aligned with NIST SSDF PO.3
Environment Separation
config.js auto‑selects backend URL based on protocol
Prevents accidental use of dev backend in production
Shift‑Left Security
Security added during development:
Authentication checks
Output encoding
Input validation
Error handling

CI/CD Pipeline (.gitlab-ci.yml)
Stage 1 – test
Pytest verifies 401 responses for protected endpoints

Stage 2 – security-scan
GitLab SAST (Python + JS)

Trivy dependency scan
Pipeline blocks on HIGH/CRITICAL findings

Stage 3 – deploy
Render webhook (placeholder)

Only runs on main
Branch protection requires merge request approval

## 6. Architecture Diagram

Client (SPA)
   |
   |  JWT Auth + API Requests
   v
FastAPI Backend
   |
   |  CWE XML + NVD API
   v
Data Sources

AI Assistance Declaration
AI assistance was used for documentation structuring and clarity.
All code, design decisions, and implementation work were authored by the student.



