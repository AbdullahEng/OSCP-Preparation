# 🔧 How I maintain this repo

A tiny, repeatable routine so the repo stays alive and looks professional.

## The daily/weekly cycle
```bash
git pull                                   # get any changes first
# ...study, add notes, drop screenshots...
git add .                                  # stage everything changed
git commit -m "Week X: <what I did>"       # save with a clear message
git push                                   # upload to GitHub
```

## Good commit messages
- ✅ `Week 3: nmap + service enumeration notes`
- ✅ `Writeup: Blue (HTB retired) — EternalBlue`
- ✅ `Tools: add ffuf card + UDP scan note`
- ❌ `update`, `stuff`, `asdf`

Small, frequent commits beat one giant dump — the green contribution graph shows consistency, which is exactly what recruiters read.

## Embedding a screenshot
1. Capture with Flameshot on Kali (`flameshot gui`).
2. Save it into `assets/screenshots/` with a clear name: `blue-foothold.png`.
3. Reference it in any `.md`:
   ```markdown
   ![Foothold on Blue](../assets/screenshots/blue-foothold.png)
   ```
   *(Use `assets/...` from the README, `../assets/...` from inside a subfolder.)*
4. `git add . && git commit -m "..." && git push` → it renders on GitHub.

## First-time authentication
GitHub no longer accepts your account password on the command line. Pick one:
- **Personal Access Token (PAT):** GitHub → Settings → Developer settings → Personal access tokens → generate one with `repo` scope → use it as the *password* when git prompts.
- **GitHub CLI (easier):** install `gh`, run `gh auth login`, follow the browser prompt. After that, `git push` just works.

## Keep it clean
- Never commit real credentials, VPN configs, or client/target data.
- Never commit OSCP exam material.
- A `.gitignore` for scratch files keeps the repo tidy.
