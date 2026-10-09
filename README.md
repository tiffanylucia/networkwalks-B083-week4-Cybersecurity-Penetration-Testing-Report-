# Penetration Testing & Vulnerability Assessment Report
**Client:** Mediroza General Hospital  
**Target Domain:** `https://medirozahospital.com`  
**Engagement Type:** Black-box Penetration Test (5-Day Duration)  
**Classification:** NETWORKWALKS CONFIDENTIAL — AUTHORISED PERSONNEL ONLY  

---

## 01. Executive Summary
During the 5-day black-box penetration testing engagement authorized by Mediroza General Hospital, our assessment team successfully evaluated the web application security posture and internal file controls. Multiple vulnerabilities were identified, leading to severe information disclosure. Specifically, weak PDF encryption controls, publicly accessible directory listings, and unencrypted database backups containing highly sensitive internal personnel records and shareholder data were successfully compromised. Immediate remediation is strongly advised to prevent malicious data exfiltration and protect patient and corporate privacy.

---

## 02. Scope and Methodology
* **Target:** `https://medirozahospital.com`
* **Scope:** Full black-box penetration test (Excluding Denial of Service and social engineering attacks in accordance with agreed rules).
* **Methodology:** 
  1. *Milestone 1:* Reconnaissance and unauthorized access discovery of confidential patient lab reports.
  2. *Milestone 2:* Cryptanalysis and password cracking against encrypted PDF reports.
  3. *Milestone 3:* Metadata extraction pointing to unindexed server directories, leading to database backup retrieval and internal record parsing.
  4. *Milestone 4:* Documentation and reporting of findings and remediation strategies.

---

## 03. Findings and Proof of Exploitation

### Finding 1: Weak PDF Document Encryption (Milestone 1 & 2)
* **Description:** Patient report files (including `patient_report_3.pdf`) were protected using weak user passwords. 
* **Proof of Exploitation:** Password cracking utilities successfully resolved the encryption key (`!@#$%^&`) using standard wordlists, granting unauthorized access to confidential health documents.
* **Impact:** Low complexity decryption exposes sensitive patient data to unauthorized readers.

### Finding 2: Directory Listing Enabled on Web Server (Milestone 3)
* **Description:** Metadata recovered from `patient_report_3.pdf` indicated author details (`j.malik`) and referenced a legacy directory path (`/old/`). Browsing to `https://medirozahospital.com/old/` revealed that directory indexing was enabled.
* **Proof of Exploitation:** The web server publicly listed its directory contents, displaying an exposed database backup file (`mediroza_db_backup_2019.sql`).

### Finding 3: Sensitive Database Backup Exposure (Milestone 3)
* **Description:** An unencrypted SQL backup file (`mediroza_db_backup_2019.sql`) was left accessible in the public-facing `/old/` directory.
* **Proof of Exploitation:** Downloading and analyzing the file contents revealed internal database schemas, staff credentials, monthly salary figures, and shareholder distributions.

---

## 04. Risk Rating

| Vulnerability | Severity | Impact | Likelihood |
| :--- | :--- | :--- | :--- |
| **Exposed SQL Backup File** | **Critical** | Complete exposure of PII, internal salaries, and shareholder records. | High |
| **Directory Indexing Enabled** | **Medium** | Unauthenticated enumeration of hidden folders and legacy files. | High |
| **Weak PDF Password Protection** | **Medium** | Bypass of confidentiality controls on sensitive medical reports. | Medium |

---

## 05. Recommendations and Remediation
1. **Remove Exposed Backups:** Immediately delete all database dumps, source archives, and legacy backup files (`.sql`, `.bak`, `.zip`) from public-facing web root directories.
2. **Disable Directory Listing:** Configure the web server (LiteSpeed/Apache/Nginx) to disable auto-indexing (`Options -Indexes`) to prevent file discovery via directory browsing.
3. **Enforce Strong Access Control:** Ensure all sensitive documents, reports, and legacy application paths require robust multi-factor authentication or are moved offline entirely.
4. **Data Encryption Standards:** Enforce strong enterprise password policies and secure encryption standards for all confidential client or patient deliverables.
