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

### Step 1: Identify Target Project & Central README Path
- By default, the target project is the current working directory, and the central hub file is `../README.md`.
- If the user explicitly names another sibling folder (e.g., `remskill dms-portal-v1`), target `../<project-name>` and update `../README.md`.
- Determine the project folder name (e.g., `dms-portal-v1`).

### Step 2: Gather Git History, WIP, and Tech Stack
Run a single inspection command in the target project directory to collect:
1. **Current Branch:** `git branch --show-current`
2. **Recent Commits (Last 5):** `git log -n 5 --date=format:"%Y-%m-%d %H:%M" --pretty=format:"%h | %ad | %s"`
3. **Uncommitted Work (WIP):** `git status -s` and `git diff --stat`
4. **Tech Stack & Port (if not yet documented in `../README.md`):**
   - Check `package.json`, `.env`, `.env.example`, `docker-compose.yml`, `Dockerfile`, `go.mod`, or `requirements.txt` to infer the framework and default port.

Example PowerShell one-liner to inspect the project:
```powershell
git branch --show-current; echo "---COMMITS---"; git log -n 5 --date=format:"%Y-%m-%d %H:%M" --pretty=format:"%h | %ad | %s"; echo "`n---STATUS---"; git status -s; echo "---DIFF---"; git diff --stat
```

### Step 3: Create or Update `../README.md`
- If `../README.md` does not exist yet, create it using the **Standard Hub README Template** below.
- If `../README.md` already exists, read it first and perform a **non-destructive update**:
  1. **Section 1 (Project Overview Table):** Update the row for `<project-name>`, or append a new row if it isn't listed yet. Keep Tech Stack and Port if already known; update Branch, Last Updated date, and Status.
  2. **Section 3 (Current Focus & WIP):** Update or insert the bullet for `<project-name>` with `[YYYY-MM-DD] <project-name>: <concise summary of latest work + what is left unfinished>`.
  3. **Section 5 (Project Commit & WIP Log):** Update or insert the `### 📌 <project-name>` subsection with:
     - **อัปเดตล่าสุด:** `YYYY-MM-DD HH:mm`
     - **Branch:** `<branch>`
     - **Commit ล่าสุด:** 3–5 recent commits (`<hash>` `<message>`)
     - **ทำค้างอยู่ (Uncommitted / WIP):** Summary of modified/untracked files from `git status -s` (or `ไม่มี (Clean working tree)` if clean) + any extra note the user mentioned in chat.
  4. **Preserve everything else:** Never overwrite other projects' rows/sections, Architecture notes (Section 2), or Quick Commands (Section 4) unless the user asks to update them.

### Step 4: Confirm to User
Reply concisely showing:
- Which project was synced into `../README.md`
- A brief summary of the recorded commits and WIP status.

---

## Mode 2: Status / Recall Mode (Read `../README.md`)

1. Read `../README.md` (or `./README.md` if in the parent folder).
2. Present a concise, scannable summary in Thai:
   - **โปรเจคที่กำลังทำค้างอยู่ (WIP):** Highlight recent projects and uncommitted work.
   - **อัปเดตล่าสุดของแต่ละโปรเจค:** Summarize the table.
3. Ask which project the user wants to jump into or update next.

---

## Standard Hub README Template (`../README.md`)

Use this structure when initializing `../README.md` or adding entries:

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
