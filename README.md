# Nmap Basic Scan — 192.168.56.104

## Objective
Perform a basic Nmap scan on a lab VM (`192.168.56.104`) to identify open ports, services, and possible security issues.

## Environment
- Scanner: Kali Linux
- Nmap version: 7.95
- Target: 192.168.56.104 (VirtualBox host-only network)
- Date of scan: 2025-09-12

## Commands run
```
nmap -sn 192.168.56.104/24
nmap 192.168.56.104
nmap -p- 192.168.56.104
nmap -sV 192.168.56.104
sudo nmap -A 192.168.56.104
sudo nmap -sS -p 1-65535 -T4 192.168.56.104
nmap -p 22,80,443,3306 192.168.56.104
nmap -sV -O 192.168.56.104 -oN nmap_scan_results.txt
```

## Key findings (summary)
| Port | Service | Version | Notes |
|------|---------|---------|-------|
| 80   | http    | Apache httpd 2.4.65 | Directory listing enabled — several files visible (including `.exe` and `.apk`) |

## Directory listing (observed files)
- index
- index.nginx-debian.html
- instagram.apk
- securefile.exe
- winAttack.exe
- winattack.exe
- zenserk.exe

## Interpretation & significance
- **Port 80 (HTTP)**: Apache 2.4.65 is serving content and directory listing is enabled (information leakage).  
- **Exposed files** (`*.exe`, `*.apk`) appear in the webroot — treat as suspicious until known; do not execute them on an unisolated machine.  
- **Other common services** (SSH 22, HTTPS 443, MySQL 3306) were **closed**, reducing exposure for those services.

## Recommendations / Remediation
1. **Disable directory listing** in Apache: remove `Options Indexes` or add `Options -Indexes` in the site config or `.htaccess`. Restart Apache.  
2. **Remove or relocate suspicious files** from public webroot. If they must be stored, place them in a protected directory requiring authentication or store outside the webroot.  
3. **Analyze suspicious files only in an isolated sandbox VM** (no network) or use controlled analysis tools. Do not run them on your main host.  
4. **Harden the server**: keep OS and Apache updated (`sudo apt update && sudo apt upgrade -y`), enable a firewall (UFW) and allow only required ports. Prefer HTTPS for public-facing services.  
5. **Audit logs**: check `/var/log/apache2/access.log` and `/var/log/apache2/error.log` for suspicious uploads/accesses.

## How to reproduce
1. Ensure Nmap is installed (`nmap --version`).  
2. Run the commands in **Commands run** section (only on systems you own/are authorized to test).  
3. Save outputs using `-oN`, `-oX`, or `-oA` for archiving.

## Files included in this repo
- `nmap_scan_results.txt` — full Nmap output + interpretation.  
- `screenshots/` — (add screenshots of your terminal & browser listing).  
- `scans/` — (optional: multiple Nmap output formats if used).  
- `report.md` — (optional short report).  
- `demo.mp4` — (optional short demonstration video).

## Ethical statement
All scans were performed on a lab VM owned by me. Do not scan or probe systems for which you do not have explicit authorization.
