# Server File Permission Audit
**Client:** SmileCare Dental Clinic - Patient Database Server (Simulated)
**Prepared By:** Muhammad Ali Hussain, Cybersecurity Consultant
**Date:** October 2026

## 1. Executive Summary
A baseline security audit was conducted on the Linux-based patient database server to identify unauthorized access vectors and privilege escalation risks. The audit focused on core system files, world-writable directories, and SUID/SGID misconfigurations. 

## 2. Audit Methodology
The following commands were utilized to establish the security baseline, dropping standard errors (`2>/dev/null`) to isolate actionable misconfigurations:

*   **World-Writable Search:** `find / -type f -perm -0002`
*   **SUID/SGID Search:** `find / -type f -perm -4000 -o -perm -2000`
*   **Shadow File Verification:** `ls -l /etc/shadow`

## 3. Simulated Findings & Risk Assessment

| Target | Expected State | Actual State | Risk Level |
| :--- | :--- | :--- | :--- |
| `/etc/shadow` (Password Hashes) | `-rw-r-----` (Root read-only) | `-rw-r-----` (Root read-only) | **Pass** |
| `/opt/clinic_backup.sh` | `-rwxr-x---` (Admin only) | `-rwxrwxrwx` (World-writable) | **CRITICAL** |
| `/usr/bin/nmap` (Network Scanner) | `-rwxr-xr-x` (Standard execution) | `-rwsr-xr-x` (SUID bit set) | **HIGH** |

**Risk Breakdown:**
*   **The Backup Script:** Because `/opt/clinic_backup.sh` is world-writable (777 permissions), any compromised low-level user account can append malicious commands to it. When the root cron job runs the backup, the malware will execute with root privileges.
*   **The SUID Binary:** `nmap` has the SUID bit set. An attacker can use nmap's interactive mode to spawn a root shell, bypassing all standard user restrictions.

## 4. Remediation Instructions for IT Staff
Execute the following commands immediately to remediate the identified vulnerabilities:

1.  **Strip world-write permissions from the backup script:**
    `chmod 750 /opt/clinic_backup.sh`
    *(Grants read/write/execute to owner, read/execute to group, and completely denies access to others).*
2.  **Remove the SUID bit from the network scanner:**
    `chmod u-s /usr/bin/nmap`
    *(Strips the execution-as-owner privilege, restricting it to standard user execution).*
