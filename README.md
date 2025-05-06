# first-htb-box-walkthrough
My first Hack The Box machine — a full walkthrough of how I compromised it.
# Hack The Box - First Machine Walkthrough

## 🧠 Summary
This is my personal walkthrough of my first Hack The Box machine. I document how I discovered, enumerated, and exploited the box using real-world pentesting techniques and tools.

---

## 🔍 Initial Recon - Network Discovery

```bash
netdiscover -r 192.xxx.xx.x/24
-r stands for range.

Detects live hosts in the subnet.

Use Ctrl+C to stop the scan and Ctrl+L to clear the terminal.

⚡ Port Scanning with Nmap
bash
Copy
Edit
nmap -T4 -p- -A 192.168.1.193
-T4 = faster execution.

-p- = scan all 65535 ports.

-A = aggressive scan (OS, version detection, scripts, and traceroute).

UDP Note:
For UDP scans, avoid -A (too slow). Use:

bash
Copy
Edit
nmap -sU -T4 -p- 192.xxx.xx.x
🌐 Web Enumeration
Nikto
bash
Copy
Edit
nikto -h http://192.168.1.193
Identifies potential vulnerabilities like remote buffer overflow or code execution.

DirBuster
Scans for hidden directories and file extensions.

You can add .pdf, .zip, .rar, etc., for deeper scans.

Tip: Always check source code and comments in HTML for clues.

🔒 SMB Enumeration
SMB = file sharing used in internal environments (like printers or network drives).

From Nmap -A, we find the SMB version (e.g., Samba 2.2.1a).

Using Metasploit
Use search smb to find available modules.

Modules are typically under auxiliary/scanner/smb/.

RHOST = remote host (target)
LHOST = your host (attacker machine)

SMBClient
bash
Copy
Edit
smbclient //192.168.1.193/IPC$ -N
Attempts anonymous connection.

Could not access admin$, but IPC$ gave us command access.

🛠 Tools Used
netdiscover

nmap

nikto

dirbuster

burpsuite

metasploit

smbclient

🔚 Conclusion
Learned enumeration through ports, HTTP, SMB.

Discovered Samba version.

Identified potential exploits.

Practiced scanning, analyzing, and documenting responsibly.
