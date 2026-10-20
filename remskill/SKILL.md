---
name: remskill
description: >-
  Syncs the current subproject's Git status, recent commits, branch, and work-in-progress (WIP) into the parent workspace hub README (../README.md), or reads ../README.md to summarize what was left off across projects. Use this skill whenever the user says "remskill", "/remskill", asks to update/sync the central or parent README, or asks to check project status across the workspace.
---

# Remskill — Workspace Project Memory & README Sync

`remskill` maintains a central `README.md` in the parent directory (`../README.md`) so developers can switch between multiple projects without losing track of what was done, what is still in progress (WIP), which ports are used, and how projects connect.

> **Portability & Language Rule:**
> - Always resolve paths relatively (`../README.md` from a subproject, or `./README.md` if run from the workspace hub root). Never hardcode machine-specific user paths.
> - Match the language already used in `../README.md` (or the language the user speaks in chat, e.g., Thai or English).

---

## Modes of Operation

Determine the mode from the user's request:

1. **Sync Mode (Default)** — Triggered when the user invokes `remskill` (or `remskill sync` / `อัปเดต remskill`) inside a subproject, or asks to sync a specific project (e.g., `remskill dms-portal-v1`).
2. **Status / Recall Mode** — Triggered when the user asks to check status (`remskill status`, `remskill check`, `ทำอะไรค้างไว้บ้าง`) to review `../README.md` without modifying it.

---

## Mode 1: Sync Mode (Update `../README.md`)

### Step 1: Identify Target Project & Read `../README.md`
- By default, the target project is the current working directory, and the central hub file is `../README.md`.
- If the user explicitly names another sibling folder (e.g., `remskill dms-portal-v1`), target `../<project-name>` and update `../README.md`.
- If the target project folder does not have `.git` at its root, check 1 level deep for sub-repositories (e.g., Frontend + Backend repos inside one project folder) and inspect each sub-repo.
- Read `../README.md` (if it exists) and check whether `### 📌 <project-name>` already exists and has a **Last Synced Commit (`<last_hash>`)** recorded.

### Step 2: Determine Commit Range to Read (Smart Incremental vs. First-Time Ask)

#### Case A: Project ALREADY exists in `../README.md` (Incremental Sync)
1. Look up the recorded `Last Synced Commit` hash (`<last_hash>`) under `### 📌 <project-name>`.
2. Run a single inspection command in the target project directory to fetch **all new commits since `<last_hash>`** (`<last_hash>..HEAD`), plus current branch and uncommitted WIP:
   ```powershell
   git branch --show-current; echo "---NEW_COMMITS---"; git log <last_hash>..HEAD --date=format:"%Y-%m-%d %H:%M" --pretty=format:"%h | %ad | %s"; echo "`n---STATUS---"; git status -s; echo "---DIFF---"; git diff --stat
   ```
3. **Edge Cases:**
   - If `<last_hash>..HEAD` returns **0 new commits** (e.g., user only has uncommitted WIP changes), keep the previous commit list intact and update only the **ทำค้างอยู่ (Uncommitted / WIP)** and **อัปเดตล่าสุด** fields.
   - If `<last_hash>` no longer exists (e.g., due to `git rebase` or `commit --amend`), fall back to reading `git log -n 5`.

#### Case B: Project is NEW (Not yet in `../README.md`)
1. Unless the user already specified a range in their prompt (e.g., `remskill ย้อนหลัง 5 commit`), first inspect the latest 10 commits and WIP status so you can give helpful context:
   ```powershell
   git branch --show-current; echo "---RECENT_COMMITS---"; git log -n 10 --date=format:"%Y-%m-%d %H:%M" --pretty=format:"%h | %ad | %s"; echo "`n---STATUS---"; git status -s; echo "---DIFF---"; git diff --stat
   ```
2. Also check `package.json`, `.env`, `.env.example`, `docker-compose.yml`, `go.mod`, or `requirements.txt` to infer Tech Stack and default Port.
3. **Ask the user** (using the `ask_question` tool if available, or a brief question) how far back to pull commits for this first-time setup:
   - **(Recommended) ดึง 5 Commit ล่าสุด** (Last 5 commits)
   - **ดึงเฉพาะ Commit ของรอบการทำงานล่าสุด / วันนี้** (Only latest session/day's commits)
   - **ดึง 10 Commit ล่าสุด** (Last 10 commits)
   - *(The user can also type a custom commit hash, number of commits, or extra WIP note.)*

### Step 3: Create or Update `../README.md` Non-Destructively
- If `../README.md` does not exist yet, create it using the **Standard Hub README Template** below.
- Perform a **non-destructive update** on `../README.md`:
  1. **Section 1 (Project Overview Table):** Update the row for `<project-name>`, or append a new row if it isn't listed yet. Keep Tech Stack and Port; update Branch, Last Updated date, and Status summary.
  2. **Section 3 (Current Focus & WIP):** Update or insert the bullet for `<project-name>` with `[YYYY-MM-DD] <project-name>: <concise summary of latest work + what is left unfinished>`.
  3. **Section 5 (Project Commit & WIP Log):** Update or insert the `### 📌 <project-name>` subsection using this format (always record `Last Synced Commit` so future runs know where to resume):
     ```markdown
     ### 📌 <project-name>
     - **อัปเดตล่าสุด (Last Updated):** YYYY-MM-DD HH:mm
     - **Branch:** `<branch>`
     - **Last Synced Commit:** `<latest_commit_short_hash>`
     - **Commit ล่าสุด (Recent Commits):**
       - `<hash>` `<commit message>` (YYYY-MM-DD HH:mm)
     - **ทำค้างอยู่ (Uncommitted / WIP):** <summary of modified/untracked files from git status, or "ไม่มี (Clean working tree)">
     ```
  4. **Preserve everything else:** Never overwrite other projects' rows/sections, Architecture notes (Section 2), or Quick Commands (Section 4) unless the user asks to update them.

### Step 4: Confirm to User
Reply concisely showing:
- Which project was synced into `../README.md`
- Whether it synced incrementally (`<old_hash>..HEAD`) or initialized a new project entry
- A brief summary of the recorded commits and WIP status.

---

## Mode 2: Status / Recall Mode (Read `../README.md`)

1. Read `../README.md` (or `./README.md` if in the parent folder).
2. **Always present the response as a Markdown Summary Table as the primary view**, followed only by brief bullet points for critical WIP or warnings:

   ```markdown
   ### 📊 ภาพรวมโปรเจคทั้งหมด (Workspace Summary)

   | โปรเจค | ส่วนงาน | Branch | อัปเดตล่าสุด | สถานะ | งานล่าสุด / งานที่ค้างอยู่ (WIP) |
   | :--- | :--- | :--- | :--- | :---: | :--- |
   | **`<project>`** | `<Frontend/Backend/etc>` | `<branch>` | `YYYY-MM-DD` | `🟢 Clean` / `🟡 WIP` / `⚠️ No Git` | `<concise summary of latest work or pending files>` |

   ---
   ```

   - **Status Badges:** Use `🟢 Clean` for clean working trees, `🟡 WIP` for uncommitted work, and `⚠️ No Git` if git is not initialized.
   - If there are uncommitted WIP files or warnings, add a short **🎯 สิ่งที่ควรทำต่อ / จุดสังเกต (Pending Action Items)** section below the table.
3. Conclude by asking which project the user wants to jump into, run, or update next.

---

## Standard Hub README Template (`../README.md`)

Use this structure when initializing `../README.md`:

```markdown
# 🗂️ Workspace Projects Hub

ไฟล์กลางสำหรับติดตามสถานะโปรเจคทั้งหมดในโฟลเดอร์นี้ (อัปเดตอัตโนมัติผ่านสกิล `remskill`)

---

## 1. 📊 ตารางสารบัญโปรเจค (Project Overview)

| โปรเจค (Folder) | รายละเอียด | Tech Stack | Port | Branch ล่าสุด | อัปเดตเมื่อ | สถานะ |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| *ยังไม่มีข้อมูล (รัน `remskill` ในโปรเจคย่อยเพื่อเพิ่มข้อมูล)* | - | - | - | - | - | - |

---

## 2. 🔗 ความเชื่อมโยงระหว่างโปรเจค (Architecture & Dependencies)

- *จดบันทึกความเชื่อมโยง เช่น Frontend ตัวไหนเรียก API ตัวไหน หรือใช้ Database/Docker ตัวไหนร่วมกัน*

---

## 3. 🎯 สถานะปัจจุบัน / ทำค้างไว้ถึงไหน (Current Focus & WIP)

- *ยังไม่มีรายการค้าง (รัน `remskill` ในโปรเจคย่อยเพื่อบันทึกสถานะล่าสุด)*

---

## 4. ⚡ คำสั่งที่ใช้บ่อย (Quick Commands)

```bash
# ตัวอย่างคำสั่งที่ใช้บ่อยใน Workspace นี้
```

---

## 5. 📝 รายละเอียดและประวัติแต่ละโปรเจค (Project Logs)

<!-- ข้อมูลแต่ละโปรเจคจะถูกอัปเดตอัตโนมัติด้วย remskill -->
```
