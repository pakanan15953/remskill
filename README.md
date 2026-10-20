# 🧠 remskill — Workspace Memory & Central README Sync

[🇬🇧 English](#-overview-english) | [🇹🇭 ภาษาไทย](#-ภาพรวม-ภาษาไทย)

---

## 🇬🇧 Overview (English)

**`remskill`** is a portable skill for **Antigravity** (and AI coding agents) that automatically tracks progress across multiple subprojects in a workspace. When invoked inside any subproject, it inspects recent `git log` commits, the active `git branch`, and uncommitted work-in-progress (`git status` / `git diff`), then non-destructively updates a central hub `../README.md` in the parent folder.

### ✨ Key Features

1. **Sync Mode (`remskill` or `remskill sync`)**
   - **First-time sync (New Project):** Previews recent commits and asks you how far back to include (e.g., last 5 commits, today's commits, last 10 commits, or since a specific commit).
   - **Incremental sync (Existing Project):** Automatically reads `Last Synced Commit` from `../README.md` and fetches **all new commits since the last update** (`git log <last_hash>..HEAD`), plus uncommitted WIP files (`git status` / `git diff`).
   - Detects the project's Tech Stack and default Port automatically.
   - Updates **only** the current project's row and log section in `../README.md` without overwriting other projects.
   - Uses relative paths (`../README.md`) exclusively — 100% portable across machines and operating systems.

2. **Batch Update Mode (`remskill update` or `remskill all`)**
   - Automatically checks and updates **all currently tracked projects in `../README.md`** in a single pass.
   - Pulls recent commits, detects uncommitted WIP, updates timestamps, and renders a fresh Workspace Summary Table.
   - Fast and clean — only updates projects you have actively added to `../README.md` without cluttering your workspace with unused or archived folders.

3. **Status / Recall Mode (`remskill status` or `remskill check`)**
   - Reads `../README.md` from the parent workspace directory and summarizes progress across all projects into a clean **Workspace Summary Table** with status badges (`🟢 Clean`, `🟡 WIP`, `⚠️ No Git`).

### 📁 Directory Structure

Structured 1-level deep to support Antigravity's `skills.json` discovery directly:

```text
remskill/
├── remskill/
│   └── SKILL.md           # Main skill instructions for the AI agent
├── skills.template.json   # Sample configuration for Global/Workspace setup
└── README.md              # Documentation (English & Thai)
```

### 🚀 Installation

#### Option 1: Link via Global `skills.json` (Recommended — Works across all projects)
1. Clone this repository anywhere on your machine:
   ```bash
   git clone <repo-url> ~/Documents/remskill
   ```
2. Add the cloned path to `~/.gemini/config/skills.json` (on Windows, use the full path with forward slashes `C:/Users/...` instead of `~/`):
   ```json
   {
     "entries": [
       {
         "path": "C:/Users/<your-username>/Documents/remskill"
       }
     ]
   }
   ```
   *(To update later, simply run `git pull` inside the cloned folder.)*

#### Option 2: Copy directly to Global Skills
Copy the inner `remskill/SKILL.md` folder to:
- `~/.gemini/config/skills/remskill/SKILL.md`

#### Option 3: Project-Specific Installation (Shared via Project Git)
Copy the inner `remskill/SKILL.md` folder into your project at:
- `.agents/skills/remskill/SKILL.md`

### 💡 Usage

When working inside any subproject (e.g., `my-frontend-app`):
- Type **`remskill`** or **`remskill sync`** → Syncs the current project's latest commits and WIP state into `../README.md`.
- Type **`remskill update`** or **`remskill all`** → Batch updates **all tracked projects** in `../README.md` to the latest state.
- Type **`remskill <project-folder>`** → Syncs a specific sibling project into `../README.md`.
- Type **`remskill status`** → Summarizes current progress and unfinished work across all projects from `../README.md`.

---

## 🇹🇭 ภาพรวม (ภาษาไทย)

**`remskill`** คือ Skill สำหรับ **Antigravity** ที่ช่วยจำว่าแต่ละโปรเจคย่อยทำอะไรไปถึงไหนแล้ว โดยเข้าไปอ่าน `git log`, `git branch`, และงานที่ทำค้างไว้ (`git status` / `git diff`) ของโปรเจคปัจจุบัน แล้วนำไปอัปเดตลงไฟล์ `../README.md` ที่โฟลเดอร์กลางให้อัตโนมัติ

### ✨ ฟีเจอร์หลัก

1. **โหมดบันทึก (`remskill` หรือ `remskill sync`)**
   - **รันครั้งแรกของโปรเจค (New Project):** สแกนดู Commit เบื้องต้นแล้วถามเราก่อนว่าจะให้ดึงย้อนหลังแค่ไหน (เช่น 5 Commit ล่าสุด, เฉพาะของวันนี้, 10 Commit ล่าสุด หรือระบุเอง)
   - **รันครั้งถัดๆ ไป (Incremental Sync):** เช็ค `Last Synced Commit` ที่เคยบันทึกไว้ใน `../README.md` แล้วดึง **เฉพาะ Commit ใหม่ทั้งหมดที่เกิดขึ้นหลังจากอัปเดตครั้งก่อน** (`git log <last_hash>..HEAD`) อัตโนมัติ พร้อมเช็คไฟล์ที่ทำค้างไว้ (`git status` / `git diff`)
   - ตรวจเช็ค Tech Stack และ Port เบื้องต้นให้อัตโนมัติ
   - อัปเดตเฉพาะส่วนของโปรเจคตัวเองลงใน `../README.md` โดยไม่ทับข้อมูลโปรเจคอื่น
   - ใช้ Relative Path (`../README.md`) เสมอ ทำให้แชร์ให้ทีมหรือย้ายเครื่องได้ทันทีโดยไม่ต้องแก้โค้ด

2. **โหมดอัปเดตทั้งหมด (`remskill update` หรือ `remskill all`)**
   - อัปเดต **ทุกโปรเจคที่บันทึกไว้ใน `../README.md`** ให้เป็นปัจจุบันพร้อมกันทั้งหมดในคำสั่งเดียว
   - ตรวจหา Commit ใหม่และไฟล์ที่ทำค้างไว้ (WIP) ของแต่ละโปรเจค แล้วแสดงผลตารางสรุปใหม่ทันที
   - รวดเร็วและไม่รก — โฟกัสเฉพาะโปรเจคที่เราเปิดใช้งานจริง ไม่กวาดเอาโฟลเดอร์สำรองหรือโปรเจคเก่ามารกตาราง

3. **โหมดทวนความจำ (`remskill status` หรือ `remskill check`)**
   - อ่านไฟล์ `../README.md` ที่โฟลเดอร์กลาง แล้วสรุปสถานะทุกโปรเจคออกมาเป็น **ตารางภาพรวม (Summary Table)** มองเห็นง่ายทันที พร้อมสัญลักษณ์สถานะ (`🟢 Clean`, `🟡 WIP`, `⚠️ No Git`)

### 🚀 วิธีติดตั้งและแชร์ให้คนอื่นใช้

#### วิธีที่ 1: เชื่อมผ่าน Global `skills.json` (แนะนำ — ใช้ได้ทุกโปรเจคในเครื่อง)
1. Clone repository นี้ไปไว้ในเครื่อง
2. เพิ่ม Path เต็มของโฟลเดอร์ `remskill` เข้าไปในไฟล์ `C:\Users\<ชื่อผู้ใช้>\.gemini\config\skills.json` (บน Windows แนะนำให้ใส่ Path เต็มโดยใช้ `/` แทน `~/`):
   ```json
   {
     "entries": [
       {
         "path": "C:/Users/gluee/Documents/workdms/remskill"
       }
     ]
   }
   ```
   *(เวลามีอัปเดตเวอร์ชันใหม่ แค่สั่ง `git pull` ก็ใช้งานต่อได้ทันที)*

#### วิธีที่ 2: ก๊อปปี้ไปวางใน Global Skills โดยตรง
ก๊อปปี้โฟลเดอร์ย่อย `remskill/SKILL.md` ไปไว้ที่:
- `~/.gemini/config/skills/remskill/SKILL.md`

#### วิธีที่ 3: ใช้เฉพาะในโปรเจค (แชร์ผ่าน Git ของโปรเจคนั้น)
ก๊อปปี้โฟลเดอร์ย่อย `remskill/SKILL.md` ไปไว้ที่:
- `.agents/skills/remskill/SKILL.md`

### 💡 วิธีเรียกใช้งาน

เมื่อเปิด Antigravity อยู่ในโปรเจคย่อย (เช่น `dms-portal-v1`):
- พิมพ์ **`remskill`** หรือ **`อัปเดต remskill`** → บันทึกความคืบหน้าล่าสุดของโปรเจคนั้นลง `../README.md`
- พิมพ์ **`remskill update`** หรือ **`remskill all`** → อัปเดตทุกโปรเจคที่อยู่ใน `../README.md` ให้เป็นปัจจุบันทั้งหมดในคำสั่งเดียว
- พิมพ์ **`remskill dms-portal-v1`** → สั่งอัปเดตโปรเจคที่ระบุลง `../README.md`
- พิมพ์ **`remskill status`** → ดูตารางสรุปภาพรวมว่าโปรเจคไหนทำอะไรค้างไว้ถึงไหนแล้ว
