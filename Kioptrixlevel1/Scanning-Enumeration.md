TARGET IP: 192.168.106.129 (I think lol)
 ## 1) Environment
	
- Open both VMs: **Kali** (attacker) and **Kioptrix L1** (target).
    
- Ensure networking: use the same VMnet/host-only or NAT as required by VMware.
    

## 2) Local network host discovery

- `arp-scan -l`
    
    - Purpose: quickly discover live hosts on the local network using ARP requests.
        
    - Why: fast way to find VM IPs when they are on the same virtual network.
        
    - Note: records MAC addresses and IPs; often finds the Kioptrix host.
        
- Take the IP of Kioptrix found by `arp-scan` and run:
    
    - `netdiscover -r <ip-range-or-ip>`
        
    - Purpose: passive/active ARP-based discovery to confirm host and map IPs on the subnet.
        
    - Why: cross-checks results, shows vendor MAC prefix, helps confirm correct host.
        

## 3) Full port & service scan

- `nmap -A -p- <target-ip>`
    
    - Purpose: `-p-` scans all TCP ports (1–65535), `-A` enables OS detection, version detection, script scanning, and traceroute.
        
    - Why: identify open ports (e.g., **80**), running services, versions, and useful NSE script results for later enumeration.
        
    - What to record: open ports, service names, service versions, OS guesses, HTTP title, and any NSE script output.
        

## 4) Webserver probing

- `nikto -h http://<ip>` and `nikto -h https://<ip>`
    
    - Purpose: automated webserver vulnerability scanning (common files, outdated server banners, misconfigurations).
        
    - Why: quick checks for known vulnerabilities, default pages, and interesting endpoints.
        
    - Note: noisy — good to run after initial mapping to get quick leads.
        
- Check HTTP manually:
    
    - Browse to `http://<ip>` in browser or use `curl -I http://<ip>` to view headers.
        
    - Purpose: observe titles, cookies, headers, redirects, server type.
        
- Look for common files: `/robots.txt`, `/favicon.ico`, `/index.php`, `/admin`, etc.
    

## 5) Directory brute-forcing

- `dirbuster` (or `gobuster` / `ffuf`) against HTTP
    
    - Purpose: brute-force directories and filenames to find hidden pages, admin panels, or uploads.
        
    - Why: uncovers interesting endpoints missed by nikto or manual browsing.
        
    - Tip: use wordlists like `common.txt`, adjust extensions (`.php`, `.html`) and recursion depth.
        

## 6) SMB enumeration

- Common tools/commands:
    
    - `enum4linux -a <ip>` — comprehensive SMB/NetBIOS enumeration (shares, users, OS info).
        
    - `smbclient -L //<ip>` — list SMB shares (can test anonymous login with `-N`).
        
    - `smbmap -H <ip>` — map share permissions and accessible files.
        
    - Nmap scripts: `--script smb-enum-shares,smb-os-discovery` can be used during nmap.
        
    - Purpose: find exposed SMB shares, readable files, usernames, and possible scriptable attack vectors.
        

## 7) SSH enumeration

- Check for open SSH (port 22) from nmap results.
    
- Banner & version check:
    
    - `nmap -sV -p 22 --script ssh-hostkey,ssh2-enum-algos <ip>`
        
    - Purpose: see SSH version and key algorithms; may reveal weak or outdated versions.
        
- If credentials are suspected:
    
    - `hydra` or `medusa` for password guessing (ONLY in lab/authorized scenarios).
        
    - Purpose: test weak/default passwords (use responsibly and ethically).
        

## 8) General notes / recording findings

- Always record:
    
    - Target IP, open ports, services & versions, web paths found, SMB shares, usernames discovered, and any files of interest.
        
    - Command outputs or screenshots for reproducibility.
        
- Prioritize leads: web paths → credentials leaks → SMB contents → local files with secrets.
    

## 9) Example quick checklist (what I ran)

1. `arp-scan -l` — find host IP.
    
2. `netdiscover -r <ip>` — confirm mapping.
    
3. `nmap -A -p- <ip>` — full port/service scan.
    
4. `nikto -h http://<ip>` and `nikto -h https://<ip>` — web vuln scan.
    
5. `dirbuster` (or `gobuster`) — directory brute force.
    
6. `enum4linux -a <ip>` / `smbclient -L //<ip>` / `smbmap -H <ip>` — SMB enumeration.
    
7. `nmap -sV -p 22 --script ssh-hostkey <ip>` — SSH banner/algorithms.
    

### Note: mod_ssl < 2.8.7 (ours: 2.8.4) may be vulnerable to OpenFuck — exploit reference: 80/443
https://www.exploid-db.com/exploits/764
https://github.com/heltonWernik/OpenLuck // more valid

### Note: Unix Samba 2.2.1a exploit: 139
https://www.rapid7.com/db/modules/exploit/linux/samba/trans2open/ // runs metasploit
https://www.exploit-db.com/exploits/10
