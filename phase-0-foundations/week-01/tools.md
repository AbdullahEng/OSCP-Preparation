# 🧰 Week 1 — Tools I Touched

> **Only** the tools I actually used this week, each with a plain-English explanation and **one** worked example.
> Deeper cards for recon/exploitation tools live in the main [tools-reference.md](../../references/tools-reference.md) — this file is just Week 1.

---

## 🖥️ VirtualBox — the hypervisor
**What it is:** software that runs a whole separate computer (a *guest*, e.g. Kali) inside your Windows laptop (the *host*). Your isolated, disposable lab.
**When I reach for it:** to run Kali and any target VMs.
**Worked example — take a snapshot (do this constantly):**
> `Machine → Snapshots → Take → name it "clean-updated-base"`
💡 A snapshot is your undo button — restore it to get a broken VM back in seconds.

---

## 📦 apt — the package manager
**What it is:** installs and updates software on Debian/Kali.
**When I reach for it:** first thing on a fresh Kali, and any time a tool is missing.
```bash
sudo apt update && sudo apt -y full-upgrade
```
| Part | Job |
|---|---|
| `sudo` | run as admin |
| `apt update` | refresh the package list |
| `&&` | run the next part only if this succeeded |
| `apt -y full-upgrade` | upgrade everything; `-y` auto-confirms |
💡 Install one tool: `sudo apt install -y <name>`.

---

## 🧭 Orientation commands — whoami / uname / ip
**What they are:** the three quick checks after booting Kali.
```bash
whoami        # which user am I?      → kali
uname -a      # kernel + architecture → confirms 64-bit Linux
ip a          # network interfaces    → note Kali's IP address
```
💡 `ip a` is how you find your own attack IP (your `LHOST` later).

---

## 💾 df -h / free -h — disk & memory check
**What they are:** how much disk and RAM you have (matters — your FYP VMs eat ~148 GB).
```bash
df -h ~       # free disk space, human-readable
free -h       # free RAM, human-readable
```
| Part | Job |
|---|---|
| `-h` | "human-readable" (GB/MB instead of raw blocks) |
💡 Run `df -h` before importing a big VM so you don't run out of space mid-copy.

---

## 🔎 which — is this tool installed?
**What it is:** prints the path to a program, or nothing if it's missing.
```bash
which nmap gobuster hydra john nc searchsploit msfconsole enum4linux-ng
```
💡 Missing something? `sudo apt install <name>`.

---

## 📸 flameshot — screenshots
**What it is:** a screenshot tool (draw arrows/boxes/blur) — you'll capture proof constantly.
```bash
flameshot gui
```
| Part | Job |
|---|---|
| `flameshot` | the program |
| `gui` | open the interactive capture overlay |
💡 Save proof shots into `screenshots/` and bind Flameshot to the PrintScreen key.

---

## 🪟 tmux — many terminals in one window
**What it is:** a terminal multiplexer — run a scan in one pane while you work in another; sessions survive if you detach.
```bash
tmux                 # start; then use the prefix Ctrl-b:
# Ctrl-b "  split horizontally   Ctrl-b %  split vertically
# Ctrl-b <arrow>  move panes     Ctrl-b d  detach (keeps running)
```
💡 `Ctrl-b` is the "prefix": press+release it, then the next key. `tmux attach` brings a session back.

---

## 🌿 git — save work to GitHub
**What it is:** version control; your commits become your public proof-of-work.
```bash
git add . && git commit -m "Week 1: lab & battle station" && git push
```
| Part | Job |
|---|---|
| `git add .` | stage all changed files |
| `git commit -m "…"` | save a snapshot locally with a message |
| `git push` | upload commits to GitHub |
💡 `git pull` first if you edit from more than one machine.

---

## 🔍 nmap — the port & service scanner *(warm-up room)*
**What it is:** tells you what's listening on a target and what software it is. First move on every box.
```bash
nmap -p- --min-rate 1000 -oN nmap-allports.txt <ip>
```
| Part | Job |
|---|---|
| `-p-` | scan **all** 65535 ports |
| `--min-rate 1000` | ≥1000 packets/sec (faster) |
| `-oN <file>` | save normal output to a file |
💡 Two-step: all-ports fast → then `-sC -sV` deep-scan the open ones. Full card in [tools-reference.md](../../references/tools-reference.md).

---

## 📝 Obsidian — note-taking
**What it is:** a free notes app that stores everything as **Markdown files** → they drop straight into this repo.
**Worked example:** create a Vault called `OSCP-Notes`, then start each target from `../../templates/box-note-template.md`.
💡 Notes as `.md` = one system for studying **and** your portfolio.

---

*Part of my [OSCP Preparation](../../README.md) journey · new flags I learn get added to [tools-reference.md](../../references/tools-reference.md).*
