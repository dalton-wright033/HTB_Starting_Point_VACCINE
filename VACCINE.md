# Hack The Box — VACCINE
## Security Assessment Report

###  Metadata
- **Platform:** Hack The Box
- **Lab Type:** [Starting Point]
- **Operating System:** [Linux]
- **Difficulty:** [Very Easy]
- **Date Completed:** [08/20/2026]
- **Author:** [Teal (Dalton Wright)]

---

###  EXECUTIVE SUMMARY
**Risk Level:** [Critical]

**Overview:**
[Provide a 3-5 sentence summary of the engagement. Identify the primary entry point, the critical flaw that allowed escalation, and the final impact. Write this as if it were for a non-technical manager.]
The web application was compromised due to a combination of weak passwords, invalidated input fields, sudo misconfigurations. The critical flaw that lead to priviledge escalation was improper sudo configurations on the linux server hosting the web application. This led to root access via a text editor. With root access an attacker can arbitrarily excute commands to control the server with a potentially heavy impact on servie.

**Primary Root Cause:** [e.g., Lack of input validation / Missing patch for CVE-XXXX / Sudo misconfiguration]
ability to anonymously access FTP server to gather credentials
---

###  ATTACK PATH SUMMARY
1. **Reconnaissance:** Found open port 21 running FTP.
2. **Initial Access:** Exploited invalidated input field via SQLMap $\rightarrow$ Obtained os-shell/reverse shell.
3. **Privilege Escalation:** Identified sudo misconfiguration $\rightarrow$ Escalated to `root`.
4. **Objective:** Captured User and Root flags.

---

###  DETAILED WALKTHROUGH

#### 1. Enumeration & Reconnaissance
**Initial Scan:**
```bash
nmap  -sV 10.129.48.112
```

![VACCINE_NMAP](Screenshots/nmap.png)   

**Findings & Analysis:**
- 21: FTP - Potential enumeration surface.
- 80: HTTP - Web server.

**Deep Dive Enumeration:**
- FTP: Anonymous access $\rightarrow$ found backup.zip.

#### 2. Exploitation (Initial Access)
**Vulnerability Identified:** FTP anonymous access
**Methodology:**
1. Exploit anonymous FTP login: Enumerate FTP server for interesting files
2. Found password protected .zip file: Extracted .zip to attacking machine
3. Used zip2John: allows Extraction of zip password hash for cracking
4. Used John the Ripper: Crack hash of zip
5. Found index.php: Contained has of admin password for admin login page
6. Used John: Cracked admin password and gained access to admin dashboard
7. Used SQLmap: Found vulnerable input field to Postgress DB
7. Injected os commands into input field: Gained navigation of hosting machine

**Payload Used:**
![VACCINE_SQLmap](Screenshots/SQLmap_command.png)
**Result:** 
- Received reverse shell on port 4444 as postgres user

#### 3. Privilege Escalation
**Internal Enumeration:**
```bash
sudo -l
find / -perm -4000 2>/dev/null
```
**Escalation Vector:** [e.g., Sudo shell escape via Vim]
**Analysis:** [Explain *why* this worked. What was the specific misconfiguration?]

**Execution:**
```bash
# Command used to escalate
```
**Result:** [Root access achieved].

---

###  REMEDIATION MATRIX

| Vulnerability | Impact | Recommended Mitigation | Priority |
| :--- | :--- | :--- | :--- |
| [e.g., RCE via Upload] | Critical | Implement strict file-type validation and storage outside web-root | Critical |
| [e.g., Sudo Vim] | High | Remove NOPASSWD permissions for sensitive binaries in /etc/sudoers | High |
| [e.g., Outdated Apache] | Medium | Update Apache to version X.X to patch known CVEs | Medium |

---

###  LESSONS LEARNED
- **Technical Takeaway:** [What did you learn about the OS or tool?]
- **Strategic Takeaway:** [What did you learn about the attack pattern?]
- **Mistakes/Pivot Points:** [Document a path that *didn't* work and how you realized it.]

###  REFERENCES
- [Link to GTFOBins / HackTricks / CVE Database]
