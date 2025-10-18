# Executive Summary — VulnNet-dotjar (Tomcat compromise)

**Date:** October 18, 2025  
**Host:** VulnNet-dotjar (Ubuntu 18.04)  
**Summary:** An Apache Tomcat instance was found to be compromised. Attackers leveraged exposed Tomcat management functionality and AJP access to deploy a malicious WAR, achieved remote code execution (RCE), harvested a local password hash, cracked it, and used a sudo misconfiguration allowing `java -jar` to be run as root to gain root shell.

---

## Key Evidence (high level)

- Tomcat Manager API used to upload `shell.war` via HTTP PUT (`/manager/text/deploy`) with valid credentials (`webdev` account).
    
- AJP (port 8009) was used to read internal app files (e.g., `WEB-INF/web.xml`).
    
- Malicious WAR executed a reverse shell (msfvenom Java payload).
    
- A password hash (`$6$...`) discovered on filesystem and cracked with `hashcat`, yielding `jdk-admin` credentials.
    
- `jdk-admin` could run `/usr/bin/java -jar *.jar` via sudo, which allowed privilege escalation to root.
    

---

## Business Impact

- **Data confidentiality:** High — internal configuration files, credentials, and possibly sensitive application data were exposed.
    
- **Integrity:** High — attackers could modify web applications by deploying WARs or altering files.
    
- **Availability:** Medium — attackers could disrupt or replace applications.
    
- **Operational risk & compliance:** High — unauthorized root access and credential exposure risk regulatory and reputational impact; potential lateral movement to other assets.
    

Overall risk: **Critical** — full server compromise with root access.

---

# 🛠️ Technical Action Checklist: VulnNet-dotjar (Tomcat Compromise)

## Immediate (Containment) Recommendations — Do these now

1. **Revoke the compromised Tomcat credentials** (`webdev`) and any other accounts discovered.
    
2. **Stop Tomcat and remove malicious artifacts**:
    
    - `sudo systemctl stop tomcat`
        
    - Preserve forensic copies, then remove `shell.war` and any unknown WARs from `/var/lib/tomcat*/webapps/`.
        
3. **Isolate the host from the network** (or block outbound connections) to prevent further exfiltration/pivoting.
    
4. **Remove/disable AJP connector** or block port 8009 at the firewall if AJP is not required.
    
5. **Disable or fix sudo rule** that permits `java -jar` to be run as root (remove the dangerous sudoers entry).
    

---

## Short-term (Remediation & Recovery)

1. **Rotate all credentials** discovered or used on the host (local accounts, application credentials, SSH keys, API keys).
    
2. **Patch/upgrade** Tomcat and Java to supported, patched versions.
    
3. **Audit for persistence** (cronjobs, unexpected systemd services, scripts in `/tmp`, `/dev/shm`, `/var/www`, user authorized_keys). If persistence is found, consider rebuilding from a known-good image.
    
4. **Review and harden sudoers** — apply principle of least privilege; never allow arbitrary execution of interpreters (e.g., `java`) as root.
    
5. **Conduct full host forensic investigation** before returning to production (collect logs, file-system images, SHA256 of suspicious files).
    

---

## Long-term (Preventive Controls)

- Disable Tomcat Manager (or restrict to a management network and MFA).
    
- Disable or secure AJP (bind to localhost / use secret / firewall).
    
- Enforce secure credential management — no secrets in webapp files; use vaults.
    
- CI/CD gating: block building/deploying artifacts containing secrets or unapproved dependencies; require signed artifacts.
    
- Continuous monitoring: alert on WAR uploads, manager API calls, `java -jar` sudo usage, outbound connections from Tomcat processes.
    
- Deploy host-based IDS/endpoint protection and periodic SCA/SBOM scanning for vulnerable libraries.
    

---

## Recommended Next Steps for Leadership

1. Authorize immediate containment and credential rotation (High priority).
    
2. Approve forensic preservation and a full incident investigation (Medium–High).
    
3. Approve patching and configuration hardening work (Medium).
    
4. Plan post-incident review and long-term controls: restrict management interfaces, enforce secret management, and add monitoring/alerting (Medium).

## 🔴 Immediate Containment (High Priority)

✅ **Stop Tomcat (prevent further WAR execution):**