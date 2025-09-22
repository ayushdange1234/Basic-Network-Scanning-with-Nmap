# Compact Report — Nmap Scan (192.168.56.104)

**Student / Analyst:** Ayush Dange  
**Target:** 192.168.56.104 (VirtualBox host-only)  
**Scan date:** 2025-09-12  
**Tools:** Nmap 7.95, curl/wget (recommended), browser

## Objective
Perform a basic discovery and service identification scan to identify open ports, running services, and potential security issues on a lab VM.

## Key Findings (at-a-glance)
- **Host:** 192.168.56.104 — *up*, network distance 0 hops (local VM).  
- **Open ports:** **80/tcp (HTTP)** only. All other common ports tested (22, 443, 3306) were **closed**.  
- **Service:** Apache httpd **2.4.65** (Debian).  
- **Directory listing:** Enabled at web root — “Index of /” exposes files including *instagram.apk*, *securefile.exe*, *winAttack.exe*, *zenserk.exe* (suspicious executables).

## Evidence (representative commands & outputs)
- `nmap -sV -O 192.168.56.104` → `80/tcp open http Apache httpd 2.4.65`  
- `sudo nmap -A 192.168.56.104` → HTTP title: `Index of /` and file list (instagram.apk, securefile.exe, winAttack.exe, winattack.exe, zenserk.exe).  
- `nmap -p 22,80,443,3306 192.168.56.104` → `22 closed, 80 open, 443 closed, 3306 closed`.

(Full Nmap output is saved in `nmap_scan_results.txt` in repo.)

## Risk Assessment
- **Information disclosure:** Directory listing exposes file names and possibly sensitive artifacts.  
- **Malicious content risk:** Publicly exposed executables and APKs may be malware — if downloaded or executed on non-isolated hosts they could compromise systems.  
- **Attack surface:** Limited to HTTP at this time; nevertheless, a vulnerable web server or exposed malicious files pose a medium risk in a lab setting.

## Recommended Remediation (actionable)
1. **Disable directory listing**: remove `Options Indexes` or add `Options -Indexes` in the Apache virtual host config or `.htaccess`, then `sudo systemctl restart apache2`.  
2. **Remove or move suspicious files** from `/var/www/html/` to a non-public location; analyze any unknown files only in an isolated sandbox VM.  
3. **Patch & harden**: run OS and package updates (`sudo apt update && sudo apt upgrade -y`), enable UFW and restrict ports to required services.  
4. **Enable HTTPS** if the server must be public; use a valid certificate and redirect HTTP to HTTPS.  
5. **Audit logs**: review `/var/log/apache2/access.log` and `/var/log/apache2/error.log` for unauthorized uploads or access patterns.

## Next Steps for Analyst
- If you control the VM: implement remediation steps, then re-scan to verify.  
- If you do NOT control the VM: stop active testing and obtain permission from the owner.

---

**Prepared:** 2025-09-12 — Compact summary intended for submission with lab deliverables.
