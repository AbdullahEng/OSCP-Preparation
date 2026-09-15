# 🚀 Week 1 — Lab & Battle Station

> **Phase 0 · Foundations** — the week where nothing gets hacked yet, and that's the point.
> **Goal:** by Sunday night you have a working Kali lab, a note-taking system, a live GitHub repo, and you understand exactly what the OSCP exam asks of you. This is the foundation every other week stands on. Build it properly once.

**Time budget:** ~12 hours · 5 weekday sessions (~1.5 h) + one weekend block (~4 h)
**Dates:** Sep 14–20, 2026

---

## 🎯 Week 1 outcomes (what "done" looks like)

- [ ] Kali Linux running in VirtualBox, fully updated, with a clean **snapshot**
- [ ] Lab networking understood and set safely (NAT vs Host-Only vs Bridged)
- [ ] Disk space sorted (FYP VMs plan in place)
- [ ] Note-taking system installed with a **per-box template** + screenshot workflow
- [ ] GitHub repo **"OSCP Preparation"** created, structured, and pushed
- [ ] OSCP exam rules + methodology read and summarised in your own words
- [ ] Core Kali tools verified present
- [ ] A test TryHackMe room done end-to-end using the full pipeline (notes → screenshot → commit)

> ⚠️ **The one rule of this journey:** everything you attack must be *yours* or a platform that gives you permission (TryHackMe, Hack The Box, Proving Grounds). Never point a tool at a system you don't own or aren't authorised to test. This isn't just ethics — it's the law, and it's your future career on the line.

---

## 📅 Day 1 — VirtualBox + Kali, alive and updated (~1.5 h)

### Concept first
Your **host** is your Windows laptop. Inside it, **VirtualBox** runs a **guest** — a separate computer (Kali) that lives in a file. This is your "separate standalone lab": isolated, disposable, safe. You already run VirtualBox (your FYP AD lab), so you know the ropes — we're just adding your attacker machine.

**Kali Linux** is a Debian-based OS pre-loaded with hundreds of hacking tools. It's your cockpit for the whole journey.

### Steps

**1. Get the pre-built Kali VirtualBox image** (easiest — no manual install):
- Go to `https://www.kali.org/get-kali/` → **Virtual Machines** → download the **VirtualBox** 64-bit image (a `.7z` file).
- It unzips to a `.vbox` + `.vdi`. In VirtualBox: **Machine → Add** → pick the `.vbox`. Done.
- Default login: **`kali` / `kali`** — change the password immediately (next step).

**2. First boot + change the password:**
```bash
passwd
```
- `passwd` → changes the current user's password. Type a strong one (you'll type it a lot with `sudo`).

**3. Update the whole system** (do this first, every fresh Kali):
```bash
sudo apt update && sudo apt -y full-upgrade
```
| Part | Job |
|------|-----|
| `sudo` | run as root (admin) |
| `apt update` | refresh the list of available packages |
| `&&` | only run the next command if this one succeeds |
| `apt -y full-upgrade` | upgrade everything installed; `-y` = auto-answer "yes" |

This can take a while. Let it finish.

**4. Set screen resolution / clipboard sharing** (quality of life):
- VirtualBox menu → **Devices → Insert Guest Additions CD**, or Kali's image usually has them. Enable **bi-directional clipboard**: VirtualBox → machine **Settings → General → Advanced → Shared Clipboard: Bidirectional.**

### ✅ Verify
```bash
whoami          # should print: kali
uname -a        # kernel + arch — confirms you're on 64-bit Linux
ip a            # your network interfaces (note the IP)
```

### 📸 Snapshot NOW (this is the habit that saves you)
In VirtualBox: select the Kali VM → **Snapshots → Take** → name it `clean-updated-base`.

> A snapshot is a saved point in time. If you ever break Kali (bad config, malware from a box, botched update), you **restore the snapshot** and you're back to perfect in 10 seconds. Take one after every big change.

**Commit to GitHub today?** Not yet — repo comes Day 4. But screenshot your `whoami`/`ip a` output; you'll add it to the repo as proof of your Day 1.

---

## 📅 Day 2 — Lab networking + disk space (~1.5 h)

### Concept first — the 3 network modes you MUST understand
This is where beginners accidentally expose their lab or can't reach targets. Know these cold:

| Mode | What it does | Use it for |
|------|--------------|------------|
| **NAT** | Kali shares your laptop's internet through a private translated address. Kali can reach out; nothing can reach in. | Downloading tools, updating Kali |
| **Host-Only** | A private network *between your VMs and your host only*. **No internet.** Fully isolated. | Attacking your own lab VMs (like your FYP AD lab) safely |
| **NAT Network** | Like NAT but multiple VMs can also see each other. | A multi-VM lab that also needs internet |
| **Bridged** | Kali gets a real IP on your home network, like a physical PC. | ⚠️ Rarely needed — avoid; it exposes the VM to your LAN |

💡 **Golden rule for your lab:** target VMs (vulnerable machines) go on **Host-Only** so they can *never* touch the internet or your home network. Kali can have **two adapters**: one NAT (for updates) + one Host-Only (to reach targets). For cloud platforms (TryHackMe/HTB), you'll connect over their **VPN** instead — NAT is enough there.

### Steps
**1. Confirm your adapters** (VirtualBox → Kali → Settings → Network):
- Adapter 1: **NAT** (internet)
- Adapter 2 (optional now, needed later): **Host-Only Adapter** (lab targets)

**2. Sort your disk** — you have ~177 GB free, and your FYP VMs (CLIENT01/SIEM01/SIEM02) use ~148 GB. Plan:
- You don't need the FYP lab running day-to-day now. When you need room for OSCP targets, **export** it, don't delete it (it's your graded project).
```
# In VirtualBox: right-click each FYP VM → Export Appliance → save the .ova to an external drive,
# then Remove (keep files? No, once exported safely) to reclaim space.
```
- Keep **Kali** local (your home base). Run one practice target at a time locally; use cloud labs (HTB/PG) for anything multi-machine.

### ✅ Verify
```bash
df -h ~          # how much disk is free
free -h          # how much RAM (you have ~20 GB — plenty for Kali + 1 target)
```

---

## 📅 Day 3 — Note-taking system (~1.5 h)

### Concept first — why this is a Phase 0 priority
On the exam you get **24 hours to hack + 24 hours to write the report**. If your notes are messy, you fail the report even if you rooted every box. Great note-taking is a *graded skill*, not an afterthought. Start now so it's a habit by exam day.

### Tool choice: **Obsidian** (recommended)
- Free, stores notes as plain **Markdown** files on your disk → which means they drop straight into your GitHub repo. One system for notes *and* portfolio.
- Alternative: **CherryTree** (tree-structured, popular with OSCP folks) — pick Obsidian if you want the GitHub synergy.

### Steps
**1. Install Obsidian on your host (Windows):** download from `https://obsidian.md`. Create a **Vault** called `OSCP-Notes`.

**2. Install a screenshot tool on Kali** — you'll screenshot constantly:
```bash
sudo apt install -y flameshot
```
- `flameshot gui` → launches the pretty capture tool (draw arrows, boxes, blur). Bind it to the PrintScreen key in Kali's settings.

**3. Create your per-box note template.** Every target gets one note from this template (also saved in the repo at `templates/box-note-template.md`):

```markdown
# Box: <name> — <ip>

## Recon
- Nmap all-ports:
- Nmap deep:
- Open ports / services:

## Enumeration
- Web:
- SMB:
- Other:

## Foothold (initial access)
- Vulnerability:
- Steps to exploit:
- Shell as: <user>
- local.txt:

## Privilege Escalation
- Enumeration finding:
- Exploit steps:
- Shell as: root/SYSTEM
- proof.txt:

## Loot & credentials
-

## Screenshots
- ![foothold](../assets/screenshots/<box>-foothold.png)
```

💡 **Screenshot discipline:** capture the *proof* moments as you go — the shell prompt showing `whoami`, the `local.txt`/`proof.txt` contents with your IP visible. Don't wait until the end; you'll forget how you got in.

---

## 📅 Day 4 — Launch your GitHub repo "OSCP Preparation" 🌟 (~1.5 h)

### Concept first — why a repo is your best career move
Recruiters and internship leads (you're already job-hunting in Malaysia) can't see your TryHackMe hours. They *can* see a clean, active GitHub. A well-run "OSCP Preparation" repo shows discipline, methodology, and communication — the exact things that get a junior hired. It's your **public proof of work.**

### Steps

**1. Set your git identity** (one-time, on the machine you'll push from):
```bash
git config --global user.name "Abdullah Darwish"
git config --global user.email "darabdullah507@gmail.com"
```

**2. Create the repo on GitHub:** go to `https://github.com/new`
- **Repository name:** `OSCP-Preparation`
- **Description:** "My self-study journey to the OSCP certification — notes, methodology, and lab writeups."
- **Public** (that's the whole point — it's a portfolio)
- Tick **Add a README** (you'll replace it with the one I built you)
- Create.

**3. Clone it and drop in the structure** (the folders + files I prepared):
```bash
git clone https://github.com/AbdullahEng/OSCP-Preparation.git
cd OSCP-Preparation
# copy in the files/folders I gave you, then:
git add .
git commit -m "Initial structure: plan, tools reference, Week 1"
git push
```
| Part | Job |
|------|-----|
| `git clone <url>` | download your empty repo to your machine |
| `cd OSCP-Preparation` | move into the repo folder |
| `git add .` | stage **all** changed files for the next commit |
| `git commit -m "..."` | save a snapshot with a message |
| `git push` | upload your commits to GitHub |

> First `git push` asks you to log in. Use a **Personal Access Token** (GitHub → Settings → Developer settings → Tokens) as the password, or install **GitHub CLI** (`gh auth login`) for an easier flow. I'll walk you through whichever you pick.

**4. Embed a screenshot** (the workflow you'll repeat forever):
```markdown
![Kali first boot](assets/screenshots/day1-kali-boot.png)
```
- Save the image into `assets/screenshots/`
- Reference it with `![description](path)` in any `.md` file
- `git add`, `commit`, `push` → it renders on GitHub automatically

### ✅ Verify
Visit `https://github.com/AbdullahEng/OSCP-Preparation` — you should see your README rendered with the big **"OSCP Preparation"** title. That's your lab, live. 🎉

---

## 📅 Day 5 — Know your enemy: the exam + methodology (~1.5 h)

### Steps
**1. Read the official OSCP exam guide** (bookmark it):
`https://help.offsec.com/hc/en-us/articles/4412170923924-OSCP-Exam-FAQ`

**2. Write the exam facts in your own words** (in your notes + a repo file). Test yourself — can you recall:
- Passing score? **70 / 100**
- 3 standalone machines = **60 pts** (10 shell + 10 privesc each)
- 1 AD set of 3 machines = **40 pts** (partial credit 10/10/20, assumed compromise)
- Metasploit limit? **One machine, never pivoting**
- Bonus points? **None** (removed Nov 2024)
- Time? **24 h hack + 24 h report**

**3. Learn the OffSec methodology / mindset** — the loop for every box:
```
Recon → Enumerate → Find a vulnerability → Exploit (get a shell)
      → Enumerate again (as the new user) → Escalate to root/SYSTEM
      → Loot → Document everything
```
💡 The whole game is **enumeration**. When you're stuck, you haven't enumerated enough — you don't need a fancier exploit.

**4. Verify your toolkit** is present in Kali:
```bash
which nmap gobuster hydra john nc searchsploit msfconsole enum4linux-ng
```
- `which <prog>` → prints the path if it's installed, nothing if it's missing. Install any missing one with `sudo apt install <name>`.

**5. Learn `tmux` basics** (keep many terminals in one window — essential when juggling scans):
- `tmux` → start · `Ctrl-b "` → split horizontally · `Ctrl-b %` → split vertically · `Ctrl-b <arrow>` → move between panes · `Ctrl-b d` → detach (keeps running)

---

## 📅 Day 6–7 (weekend) — Prove the pipeline end-to-end (~4 h)

### The mission
Do **one very easy TryHackMe room** (you're ~52% through Jr Pen Tester — pick a completed-type room or an easy new one) and run the **entire workflow** you just built:

1. Connect to TryHackMe's VPN, get the target IP.
2. `nmap -p-` then deep-scan → **save to files**, screenshot.
3. Enumerate the open services → find the way in.
4. Get a shell → **stabilise it** → grab the flag.
5. Escalate if the room has a privesc step.
6. Write it up in your **box-note template**.
7. Save it as your **first writeup** in the repo (`writeups/`), embed 2–3 screenshots.
8. `git add . && git commit -m "First writeup: <room>" && git push`.

### ✅ End-of-week self-check
- [ ] Could I re-image Kali from snapshot if it broke? (Yes = you understand snapshots)
- [ ] Do my notes let a stranger reproduce my hack? (That's the report standard)
- [ ] Is my repo live with a README, tools reference, and one writeup?
- [ ] Can I recite the exam scoring from memory?

If all four are yes — **Week 1 complete.** Tick it in the tracker. You're no longer setting up; you're a pentester with a lab. 🔥

---

## 🃏 Week 1 Flashcards (cover the right column, test yourself)

| Question | Answer |
|----------|--------|
| First command on a fresh Kali? | `sudo apt update && sudo apt -y full-upgrade` |
| How do you undo breaking your Kali VM? | Restore a **snapshot** |
| Which network mode isolates lab targets from the internet? | **Host-Only** |
| Passing score on OSCP? | **70 / 100** |
| Points for the AD set? | **40** (partial 10/10/20, assumed compromise) |
| Metasploit exam limit? | **One machine, never for pivoting** |
| Command to see if a tool is installed? | `which <tool>` |
| The mindset when stuck? | **Enumerate harder, not exploit harder** |
| Two things every writeup needs? | Reproducible steps + proof screenshots |
| `git` command to upload commits? | `git push` |

---

## 🧠 What to memorise from Week 1
1. `sudo apt update && sudo apt -y full-upgrade` — the fresh-Kali ritual.
2. **Snapshot after every big change.** It's your undo button.
3. **Host-Only = isolated lab.** Never bridge a vulnerable VM.
4. The **exam scoring** (70 to pass · 60 standalone + 40 AD).
5. The **methodology loop:** Recon → Enumerate → Exploit → Enumerate → Escalate → Document.
6. The **git cycle:** `add → commit → push`.

---

**➡️ Next week:** [Week 2 — Refresh the fundamentals](week-02-fundamentals.md) *(networking, Linux/Windows, bash, tmux — I'll build this when you finish Week 1).*

*Part of my [OSCP Preparation](../README.md) journey.*
