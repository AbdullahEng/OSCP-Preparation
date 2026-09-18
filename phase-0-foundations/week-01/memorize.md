# 🧠 Week 1 — Memorize

> Curated on purpose. Only what's genuinely worth locking in this week — no padding.
> **How to drill:** cover the right column, answer out loud, uncover. 5 minutes, daily. Do it during your weekday sessions.

---

## 🃏 Flashcards (Q → A)

### The exam (know these cold)
| Question | Answer |
|---|---|
| OSCP+ passing score? | **70 / 100** |
| Standalone machines — how many & worth? | **3 × 20 = 60** (each: **10** initial access + **10** privesc) |
| The two proof files on each box? | **`local.txt`** (normal user) + **`proof.txt`** (root / SYSTEM) |
| Active Directory set — worth & structure? | **40 pts**, 3 chained machines scored **10 / 10 / 20**, **assumed breach** (given creds) |
| Bonus points? | **None** |
| Metasploit rule? | Full **MSF + Meterpreter on ONE machine only**; `msfvenom` + multi/handler **unlimited** |
| Exam time? | ~**24 h attack** (officially 23 h 45 m) + **24 h report** |
| Report format & destination? | **PDF** inside a **`.7z`** (≤200 MB) → **upload.offsec.com** |
| OSCP vs OSCP+? | Same exam; **OSCP+** is what you earn now (includes AD, 3-yr validity) |
| A common winning line? | **Full AD (40) + 3 footholds (30) = 70** |

### The lab
| Question | Answer |
|---|---|
| First command on a fresh Kali? | `sudo apt update && sudo apt -y full-upgrade` |
| How do you undo breaking your Kali VM? | Restore a **snapshot** |
| Network mode that **isolates** lab targets from the internet? | **Host-Only** |
| Network mode Kali uses to get updates? | **NAT** |
| Which mode should a vulnerable VM **never** use? | **Bridged** (exposes it to your home LAN) |
| Command to check if a tool is installed? | `which <tool>` |
| `tmux`: split vertically / detach? | `Ctrl-b %` / `Ctrl-b d` |

### The workflow
| Question | Answer |
|---|---|
| The git cycle to save work? | `git add .` → `git commit -m "…"` → `git push` |
| The methodology loop for every box? | Recon → Enumerate → Exploit → (re-)Enumerate → Escalate → Document |
| The mindset when stuck? | **Enumerate harder, not exploit harder** |
| The one rule of the whole journey? | Only attack what you **own or are authorised** to test |

---

## ⭐ Must-memorise (the short list)

1. **Fresh-Kali ritual:** `sudo apt update && sudo apt -y full-upgrade`
2. **Snapshot after every big change** — it's your undo button.
3. **Host-Only = isolated targets · NAT = internet.** Never bridge a vulnerable VM.
4. **OSCP+ scoring:** 70 to pass · **60** standalone (3×20) + **40** AD (10/10/20).
5. **Metasploit = one machine only** (msfvenom is unlimited).
6. **git cycle:** `add → commit → push`.

> 💡 If you can teach these six to an imaginary beginner without notes, Week 1's knowledge is locked.

---

*Part of my [OSCP Preparation](../../README.md) journey.*
