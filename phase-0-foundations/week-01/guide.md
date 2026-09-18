# 📘 Week 1 Guide — Lab & Battle Station

> **How to use this file:** work top to bottom. Each block says *concept first* (why it matters), then *steps*, then *✅ verify*. Every command is broken down piece by piece — **never run a command you can't read.** The weekday parts are light; the weekend part is the hands-on build.
>
> ⚠️ **The one rule of this whole journey:** only ever attack machines you **own** (your own VMs) or platforms that **give you permission** (TryHackMe, Hack The Box, Proving Grounds). Pointing a tool at anything else is illegal (Malaysia's Computer Crimes Act 1997) and ends careers. This isn't a footnote — it's the job.

---

## 🧭 Map of the week

| Session | Topic | File this feeds |
|---|---|---|
| Mon | Read the OSCP+ exam + start Kali download | `memorize.md` |
| Tue | Lab networking + disk plan | `progress-log.md` |
| Wed | Note-taking system | `templates/box-note-template.md` |
| Thu | Toolkit + `tmux` | `tools.md` |
| Fri | Revision + read the weekend mission | `memorize.md` |
| Weekend | Build the lab + one warm-up room | `progress-log.md`, `writeups/` |

---

# 🗓️ MONDAY (~30 min) — Know your enemy, and start the download

### Concept first
You can't train for an exam you don't understand. The OSCP+ is not a quiz — it's a 24-hour hands-on hack followed by a 24-hour report. Knowing the scoring **changes your strategy** on exam day (e.g. the Active Directory set alone is worth 40 of the 100 points — it's the biggest single prize). Learn the numbers now so every week has a target.

### Step 1 — Read the official exam guide (bookmark it)
- OSCP+ Exam Guide: `https://help.offsec.com/hc/en-us/articles/360040165632-OSCP-Exam-Guide`
- OSCP+ Exam FAQ: `https://help.offsec.com/hc/en-us/articles/4412170923924-OSCP-Exam-FAQ`

### Step 2 — Write the exam facts in your own words (verified Sep 2026)
Put these in your notes and test yourself later from `memorize.md`:

| Question | Answer |
|---|---|
| Total points / to pass | **100 / need 70** |
| Standalone machines | **3 machines × 20 pts = 60** — each is **10 (initial access / `local.txt`) + 10 (privesc / `proof.txt`)** |
| Active Directory set | **1 set of 3 chained machines = 40 pts**, scored **10 / 10 / 20**, **assumed breach** (you're handed starting credentials) |
| Bonus points | **None** (there are no bonus points in the current exam) |
| Metasploit rule | Full **Metasploit modules + Meterpreter on ONE machine only**; `msfvenom` and the multi/handler listener are **unrestricted** |
| Time | ~**24 h to attack** (officially **23 h 45 m**) + **24 h to write the report** |
| Report | A **PDF**, archived as a **`.7z`** (≤ 200 MB), uploaded to **`upload.offsec.com`** |
| Proctoring | The exam is **proctored** (webcam + screen share) |
| OSCP vs OSCP+ | Same exam. **OSCP+** is what you earn today — it includes the AD set and carries a 3-year validity with continuing education. Your target = **OSCP+**. |

> 💡 A classic winning line: **full AD (40) + 3 initial footholds (30) = 70.** You do not need to fully root every standalone box to pass — but you do need enumeration everywhere.

### Step 3 — Start the Kali download now (so it's ready for the weekend)
- Go to `https://www.kali.org/get-kali/` → **Virtual Machines** → download the **VirtualBox** 64-bit image (a `.7z` file, several GB).
- Let it download in the background while you do the rest of the week. You'll import it in the weekend block.

### ✅ Verify
- [ ] I can recite the scoring without looking.
- [ ] The Kali `.7z` is downloading / downloaded.

---

# 🗓️ TUESDAY (~30 min) — Lab networking + disk plan

### Concept first — the 3 network modes you MUST understand
This is where beginners either expose their lab to the internet or can't reach their targets. Know these cold:

| Mode | What it does | Use it for |
|---|---|---|
| **NAT** | Kali shares your laptop's internet through a private, translated address. Kali can reach **out**; nothing can reach **in**. | Updating Kali, downloading tools |
| **Host-Only** | A private network **between your VMs and your host only**. **No internet.** Fully isolated. | Attacking your own lab VMs (like your FYP AD lab) safely |
| **NAT Network** | Like NAT, but several VMs can also see each other. | A multi-VM lab that also needs internet |
| **Bridged** | Kali gets a real IP on your home Wi-Fi, like a physical PC on the LAN. | ⚠️ Rarely needed — avoid; it exposes the VM to your home network |

> 💡 **Golden rule:** vulnerable target VMs go on **Host-Only** so they can *never* touch the internet or your home network. Kali can run **two adapters** — Adapter 1 = **NAT** (updates), Adapter 2 = **Host-Only** (reach targets). For cloud platforms (TryHackMe / HTB), you connect over **their VPN** instead, and NAT is all Kali needs.

### Steps
1. You'll set adapters when you import Kali this weekend (VirtualBox → Kali → **Settings → Network**): Adapter 1 = **NAT**; Adapter 2 = **Host-Only Adapter** (optional now, needed once you run local targets).
2. **Disk plan.** You have ~177 GB free; your FYP VMs (CLIENT01 / SIEM01 / SIEM02) use ~148 GB. When you need room for OSCP targets:
   - **Export**, don't delete (it's your graded project): VirtualBox → right-click each FYP VM → **Export Appliance** → save the `.ova` to an external drive → then remove it locally to reclaim space.
   - Keep **Kali** local as your home base. Run **one** local target at a time; use cloud labs (HTB / Proving Grounds) for anything multi-machine.

### ✅ Verify
- [ ] I can explain NAT vs Host-Only in one sentence each.
- [ ] I have a disk plan written in `progress-log.md`.

---

# 🗓️ WEDNESDAY (~30 min) — Note-taking system

### Concept first — why this is a Phase 0 priority
On the exam you get **24 h to hack + 24 h to write the report**. Root every box and still fail the report = you fail. Note-taking is a **graded skill**, not an afterthought. Build the habit now so it's automatic by exam day.

### Tool choice: **Obsidian** (recommended)
- Free; stores notes as plain **Markdown** files on disk → they drop straight into your GitHub repo. One system for notes **and** portfolio.
- Alternative: **CherryTree** (tree-structured, popular with OSCP folks). Pick Obsidian for the GitHub synergy.

### Steps
1. **Install Obsidian** on Windows: `https://obsidian.md` → create a **Vault** called `OSCP-Notes`.
2. **Screenshot tool on Kali** (you'll do this in the weekend block, note it now):
   ```bash
   sudo apt install -y flameshot
   ```
   | Part | Job |
   |---|---|
   | `sudo` | run as root (admin) |
   | `apt install` | install a package |
   | `-y` | auto-answer "yes" to prompts |
   | `flameshot` | the screenshot tool to install |
3. **Your per-box note template already lives in the repo** at `../../templates/box-note-template.md`. Every target gets a fresh copy of it. Open it now and read it so you know the shape (Recon → Enumeration → Foothold → Privesc → Loot → Lessons).

> 💡 **Screenshot discipline:** capture the *proof* moments **as they happen** — the shell prompt showing `whoami`, the `local.txt` / `proof.txt` with your IP visible. Don't wait till the end; you'll forget how you got in.

### ✅ Verify
- [ ] Obsidian vault `OSCP-Notes` exists.
- [ ] I've read `templates/box-note-template.md` and understand each section.

---

# 🗓️ THURSDAY (~30 min) — Toolkit check + tmux

### Concept first
Kali ships with hundreds of tools, but you only need to *confirm the core ones are present* and know how to check for any tool. And when you're juggling scans, you need many terminals in one window — that's `tmux`.

### Step 1 — The toolkit check (you'll run this on Kali in the weekend block)
```bash
which nmap gobuster hydra john nc searchsploit msfconsole enum4linux-ng
```
| Part | Job |
|---|---|
| `which` | prints the path if a program is installed, nothing if it's missing |
| the list | the core OSCP tools you want present |

Install anything missing with `sudo apt install <name>`.

### Step 2 — `tmux` basics (memorise the 5 you'll actually use)
`tmux` keeps multiple terminals alive in one window — essential when a scan runs in one pane while you work in another.

| Keys | Action |
|---|---|
| `tmux` | start a session |
| `Ctrl-b` then `"` | split pane **horizontally** |
| `Ctrl-b` then `%` | split pane **vertically** |
| `Ctrl-b` then arrow | move between panes |
| `Ctrl-b` then `d` | **detach** (session keeps running in the background) |

> 💡 `Ctrl-b` is the "prefix" — you press it first, release, then the next key. `tmux attach` brings a detached session back.

### ✅ Verify
- [ ] I know how to check if any tool is installed.
- [ ] I can split a pane and detach in `tmux`.

---

# 🗓️ FRIDAY (~20 min) — Revision + read the weekend mission

- Run through **[memorize.md](memorize.md)** once (cover the answers).
- Read the **weekend mission** below so Saturday starts with momentum, not planning.
- Make sure the Kali `.7z` finished downloading.

---

# 🗓️ WEEKEND BLOCK (4–5 h) — Build the battle station

This is the hands-on heart of the week. Work through it in order; log everything in `progress-log.md` as you go.

## Part A — Kali alive & updated (~45 min)

### Concept
Your **host** is Windows. Inside it, **VirtualBox** runs a **guest** — a whole separate computer (Kali) living in a file. That's your isolated, disposable lab. Kali is a Debian-based OS pre-loaded with hacking tools — your cockpit for the journey.

### Steps
1. **Import the pre-built image:** the `.7z` you downloaded extracts to a `.vbox` + `.vdi`. In VirtualBox: **Machine → Add** → pick the `.vbox`. Done (no manual install needed).
2. **First boot + change the password.** Default login is **`kali` / `kali`** — change it immediately:
   ```bash
   passwd
   ```
   - `passwd` → changes the current user's password. Pick a strong one (you'll type it constantly with `sudo`).
3. **Update the whole system** (do this on every fresh Kali):
   ```bash
   sudo apt update && sudo apt -y full-upgrade
   ```
   | Part | Job |
   |---|---|
   | `sudo` | run as root (admin) |
   | `apt update` | refresh the list of available packages |
   | `&&` | only run the next command if this one **succeeded** |
   | `apt -y full-upgrade` | upgrade everything installed; `-y` auto-confirms |

   This takes a while. Let it finish.
4. **Quality of life:** VirtualBox menu → **Devices → Insert Guest Additions CD**; and **Settings → General → Advanced → Shared Clipboard: Bidirectional** (copy-paste between host and Kali).

### ✅ Verify
```bash
whoami      # should print: kali
uname -a    # kernel + arch — confirms 64-bit Linux
ip a        # your interfaces — note Kali's IP
```

### 📸 Snapshot NOW — the habit that saves you
VirtualBox → select the Kali VM → **Snapshots → Take** → name it `clean-updated-base`.

> A snapshot is a saved point in time. Break Kali later (bad config, junk from a box, botched update)? **Restore the snapshot** and you're back to perfect in seconds. Take one after every big change. Screenshot your `whoami` / `ip a` output for `progress-log.md`.

## Part B — Networking + notes (~45 min)
1. Set Kali's adapters (Settings → Network): **Adapter 1 = NAT**. Add **Adapter 2 = Host-Only** only when you're about to run a local target.
2. Install Flameshot on Kali: `sudo apt install -y flameshot`, then `flameshot gui` to test. (Optionally bind it to the PrintScreen key in Kali's keyboard settings.)
3. Confirm your Obsidian vault is ready and you have the box template handy.

## Part C — Commit this week to GitHub (~30 min)

### Concept — why the repo is your best career move
Recruiters and internship leads (you're job-hunting in Malaysia right now) can't see your TryHackMe hours. They **can** see a clean, active GitHub. A well-run "OSCP-Preparation" repo shows discipline, methodology, and communication — exactly what gets a junior hired. It's your **public proof of work**, and the green contribution graph shows consistency.

> ✅ Good news: your repo is **already created and pushed** (`github.com/AbdullahEng/OSCP-Preparation`). So this week isn't "create the repo" — it's "make your first study-driven commit and keep the cycle going."

### Steps
1. **Confirm your git identity** (already set on this machine, but good to know):
   ```bash
   git config --global user.name "Abdullah Darwish"
   git config --global user.email "darabdullah507@gmail.com"
   ```
2. **The core cycle — burn this in:**
   ```bash
   git pull                              # get any remote changes first
   git add .                             # stage everything changed
   git commit -m "Week 1: lab & battle station"   # save a snapshot with a clear message
   git push                              # upload commits to GitHub
   ```
   | Part | Job |
   |---|---|
   | `git pull` | download any changes from GitHub before you start |
   | `git add .` | stage **all** changed files for the next commit |
   | `git commit -m "…"` | save a snapshot locally with a message |
   | `git push` | upload your commits to GitHub |
3. **Polish the front page:** open the repo `README.md` and fill in your **TryHackMe / Hack The Box / LinkedIn** profile links (the "Connect" section has placeholders).
4. **First push authentication** (if it ever asks again): use a **Personal Access Token** (GitHub → Settings → Developer settings → Tokens, `repo` scope) as the *password*, or install **GitHub CLI** and run `gh auth login`.

> 💡 Small, frequent commits beat one giant dump. `Week 1: …`, `Writeup: Blue (HTB) — EternalBlue`, `Tools: add ffuf card` — clear messages, green graph.

## Part D — Prove the pipeline: one warm-up room (~2–2.5 h)

### The mission
Do **one very easy TryHackMe room** (you're ~52% through Jr Pen Tester — pick an easy one) and run the **entire workflow** you just built:

1. Connect to TryHackMe's **VPN**, get the target IP.
2. Recon — save scans to files, screenshot:
   ```bash
   nmap -p- --min-rate 1000 -oN nmap-allports.txt <ip>   # all 65535 ports, fast
   nmap -p <open_ports> -sC -sV -oN nmap-deep.txt <ip>    # deep scan the open ones
   ```
   | Part | Job |
   |---|---|
   | `-p-` | scan **all** 65535 ports (default is only top 1000) |
   | `--min-rate 1000` | send ≥1000 packets/sec (faster) |
   | `-p <ports>` | scan only these specific ports |
   | `-sC` | run Nmap's default safe scripts |
   | `-sV` | detect service **versions** |
   | `-oN <file>` | save output to a file (always save your scans) |
3. **Enumerate** the open services → find the way in.
4. Get a shell / grab the flag. (Stabilising shells is a Week-2+ skill — for now, just complete the room.)
5. Write it up in a copy of `../../templates/box-note-template.md`.
6. Save it as your **first writeup** in `../../writeups/`, embed 2–3 screenshots.
7. Commit it:
   ```bash
   git add . && git commit -m "First writeup: <room name>" && git push
   ```

> The whole game is **enumeration**. When you're stuck, you haven't enumerated enough — you don't need a fancier exploit. Follow the loop: **Recon → Enumerate → Exploit → (re-)Enumerate → Escalate → Document.**

### ✅ End-of-week self-check
- [ ] I could re-image Kali from a snapshot if it broke.
- [ ] My notes let a stranger reproduce my steps.
- [ ] My repo is live with README, tools reference, and `week-01/` pushed.
- [ ] I can recite the OSCP+ scoring from memory.

All four yes → **Week 1 complete.** Tick it in the tracker. 🔥

---

## 📚 References (curated — 5 only, each worth your time)

| # | Link | Why it's worth it |
|---|------|-------------------|
| 1 | [OSCP+ Exam Guide (OffSec)](https://help.offsec.com/hc/en-us/articles/360040165632-OSCP-Exam-Guide) | The **only** authoritative source for scoring, rules, and report format — read it before anyone's blog. |
| 2 | [OSCP+ Exam FAQ (OffSec)](https://help.offsec.com/hc/en-us/articles/4412170923924-OSCP-Exam-FAQ) | Answers the edge-case rules (Metasploit, restarts, proctoring) in OffSec's own words. |
| 3 | [Kali in VirtualBox — official docs](https://www.kali.org/docs/virtualization/import-premade-virtualbox/) | The exact, current steps to import the pre-built image — no guesswork. |
| 4 | [Obsidian](https://obsidian.md) | Free Markdown notes that live as files → they drop straight into this repo. |
| 5 | [TryHackMe — Jr Penetration Tester path](https://tryhackme.com/path/outline/jrpenetrationtester) | Your current ladder (~52% done) — finish easy rooms here for the warm-up. |

---

*Part of my [OSCP Preparation](../../README.md) journey.*
