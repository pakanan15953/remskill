# 🧠 remskill (Workspace Memory & Central README Sync)

Skill สำหรับ **Antigravity** ที่ช่วยจำว่าแต่ละโปรเจคย่อยทำอะไรไปถึงไหนแล้ว โดยเข้าไปอ่าน `git log`, `git branch`, และงานที่ทำค้างไว้ (`git status` / `git diff`) ของโปรเจคปัจจุบัน แล้วนำไปอัปเดตลงไฟล์ `../README.md` ที่โฟลเดอร์กลางให้อัตโนมัติ

---

## ✨ ฟีเจอร์หลัก

1. **โหมดบันทึก (`remskill` หรือ `remskill sync`)**
   - อ่าน Commit ล่าสุด 5 รายการ, Branch ปัจจุบัน และไฟล์ที่แก้ไขค้างไว้ (Uncommitted / WIP)
   - ตรวจเช็ค Tech Stack และ Port เบื้องต้น
   - อัปเดตเฉพาะส่วนของโปรเจคตัวเองลงใน `../README.md` โดยไม่ทับข้อมูลโปรเจคอื่น
   - ใช้ Relative Path (`../README.md`) เสมอ ทำให้แชร์ให้ทีมหรือย้ายเครื่องได้ทันทีโดยไม่ต้องแก้โค้ด

2. **โหมดทวนความจำ (`remskill status` หรือ `remskill check`)**
   - อ่านไฟล์ `../README.md` ที่โฟลเดอร์กลาง แล้วสรุปให้ฟังว่าโปรเจคไหนทำอะไรค้างไว้บ้าง

---

## 📁 โครงสร้างโฟลเดอร์

จัดวางแบบ 1-level deep เพื่อให้รองรับระบบค้นหา `skills.json` ของ Antigravity:

```text
remskill/
├── remskill/
│   └── SKILL.md           # ตัวคำสั่งหลักของ Skill
├── skills.template.json   # ตัวอย่างการตั้งค่าเชื่อมเข้า Global/Workspace
└── README.md              # คู่มือการใช้งานและการแชร์ให้คนอื่น
```

---

## 🚀 วิธีติดตั้งและแชร์ให้คนอื่นใช้

### วิธีที่ 1: เชื่อมผ่าน Global `skills.json` (แนะนำ — ใช้ได้ทุกโปรเจคในเครื่อง)
เพิ่ม Path ของโฟลเดอร์ `remskill` เข้าไปในไฟล์ `~/.gemini/config/skills.json`:

```json
{
  "entries": [
    {
      "path": "~/Documents/workdms/remskill"
    }
  ]
}
```

### วิธีที่ 2: ก๊อปปี้ไปวางใน Global Skills โดยตรง
ก๊อปปี้โฟลเดอร์ย่อย `remskill/SKILL.md` ไปไว้ที่:
- `~/.gemini/config/skills/remskill/SKILL.md`

### วิธีที่ 3: ใช้เฉพาะในโปรเจค (แชร์ผ่าน Git ของโปรเจคนั้น)
ก๊อปปี้โฟลเดอร์ย่อย `remskill/SKILL.md` ไปไว้ที่:
- `.agents/skills/remskill/SKILL.md`

---

## 💡 วิธีเรียกใช้งาน

เมื่อเปิด Antigravity อยู่ในโปรเจคย่อย (เช่น `dms-portal-v1`):
- พิมพ์ **`remskill`** หรือ **`อัปเดต remskill`** → บันทึกความคืบหน้าล่าสุดของโปรเจคนั้นลง `../README.md`
- พิมพ์ **`remskill dms-portal-v1`** → สั่งอัปเดตโปรเจคที่ระบุลง `../README.md`
- พิมพ์ **`remskill status`** → ดูสรุปภาพรวมว่าโปรเจคไหนทำอะไรค้างไว้ถึงไหนแล้ว
