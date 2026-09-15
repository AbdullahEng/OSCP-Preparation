# Box: <name> — <ip>

> Platform: <TryHackMe / HTB / Proving Grounds / local> · Started: <date> · OS: <Linux/Windows>

## 1. Recon
```
# Nmap all-ports
nmap -p- --min-rate 1000 -oN nmap/allports <ip>

# Nmap deep scan of open ports
nmap -p <ports> -sC -sV -oN nmap/deep <ip>
```
- Open ports / services:
  - `port` →

## 2. Enumeration
- **Web (80/443):** dirs found, tech stack, interesting pages
- **SMB (139/445):** shares, null session?, users
- **Other services:**
- Interesting findings / leads:

## 3. Foothold (initial access)
- Vulnerability:
- How I exploited it (step by step):
- Shell obtained as: `<user>`
- Shell stabilised: yes/no
- **local.txt:** `________`
- 📸 screenshot: `![foothold](../assets/screenshots/<box>-foothold.png)`

## 4. Privilege Escalation
- Enumeration tool/finding (LinPEAS/WinPEAS/manual):
- Vulnerability / misconfig:
- Exploit steps:
- Shell obtained as: `root` / `SYSTEM`
- **proof.txt:** `________`
- 📸 screenshot: `![root](../assets/screenshots/<box>-root.png)`

## 5. Loot & credentials
- Users / hashes / passwords / keys found:

## 6. Lessons & tools used
- What was new / what I got stuck on:
- Tools used:
- Would I recognise this pattern again? What's the tell?
