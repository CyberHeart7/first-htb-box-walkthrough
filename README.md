# first-htb-box-walkthrough
My first Hack The Box machine — a full walkthrough of how I compromised it.
# Hack The Box - First Machine Walkthrough

## 🧠 Summary
This is my personal walkthrough of my first Hack The Box machine. I document how I discovered, enumerated, and exploited the box using real-world pentesting techniques and tools.

---

## 🔍 Initial Recon - Network Discovery

```bash
netdiscover -r 192.xxx.xx.x/24
