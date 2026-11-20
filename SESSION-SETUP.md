# วิธีให้ Session อื่นมี Skill เหมือน Session นี้

บันทึกไว้ให้ Session อื่นอ่านแล้วทำตามได้เลย

## Repo ที่เกี่ยวข้อง

| Repo | คืออะไร |
|---|---|
| `fonghwin-4114/taste-skill` | Skill ออกแบบ UI/เว็บ (v2.5.1, install name: `design-taste-frontend`) |
| `fonghwin-4114/skills` | ชุด skill ของ Anthropic (docx, pptx, xlsx, pdf, mcp-builder ฯลฯ) และ `template/` สำหรับสร้าง skill ใหม่ |
| `fonghwin-4114/ffmpeg-skill` | Skill สำหรับงาน FFmpeg |

Branch ที่ใช้พัฒนา: `claude/determined-brown-761bsd`

## ข้อกำหนดเบื้องต้น (ต้องมีเสมอ)

- เชื่อม GitHub กับ Claude ที่ https://claude.ai/connect-github
- ติดตั้ง Claude GitHub App บน repo ที่ต้องใช้
- ถ้าไม่มี Session อื่นจะ clone ไม่ได้

## ทางที่ 1: Setup script ของ environment (ถาวร แนะนำ)

1. เปิดเมนู cloud environment ที่ title bar ของ session แล้วกด Edit
2. ถ้า repo เป็น private ให้เพิ่ม repo ในหัวข้อ Repositories (Default environment ไม่มีหัวข้อนี้) แล้วเริ่ม session ใหม่โดยเลือก repo นั้น
3. วางสคริปต์นี้ในช่อง Setup script

```bash
# สคริปต์เริ่มที่โฟลเดอร์ที่เก็บ repo ที่ clone ไว้ (ไม่ใช่ในตัว repo)
# ข้ามเองถ้าไม่มี repo เพื่อไม่ให้ session สตาร์ตไม่ขึ้น
if [ -d taste-skill ]; then
  bash taste-skill/install.sh
fi
```

`install.sh` สร้าง symlink ไปที่ `~/.claude/skills/design-taste-frontend` (ทดสอบแล้วใน Session นี้ ได้ผลตามนี้) และ `git pull` จะอัปเดต skill ให้เอง

หมายเหตุ:
- สคริปต์รันทุกครั้งที่สร้าง container ใหม่ และรันตอน session เริ่มต้นเท่านั้น
- สคริปต์อ่าน secret ที่บันทึกไว้ใน environment ไม่ได้
- ใน Session ที่ไม่ได้ clone repo `taste-skill` มาด้วย สคริปต์จะไม่ทำอะไร

## ทางที่ 2: Commit skill เข้า repo โปรเจกต์

วาง skill ไว้ที่ `.claude/skills/<ชื่อ>/SKILL.md` ใน repo ที่ session ต้องใช้ แล้ว session ที่ clone repo นั้นจะโหลดให้เอง เหมาะกับ skill ของ Building Myself (brand voice, hook, สคริปต์ TikTok/Reels) โดยใช้ `skills/template` เป็นต้นแบบ

## ทางที่ 3: แนบ repo ทีละ Session (ชั่วคราว)

- ตอนสร้าง session ใหม่ ให้เลือก `taste-skill`, `skills`, `ffmpeg-skill` เป็น source
- กลาง session ให้เรียก `add_repo` ตามด้วย clone ตามที่เครื่องมือบอก แล้วเรียก `register_repo_root` เพื่อให้โหลด skill ในเทิร์นถัดไป
- ถ้าจะใช้ taste-skill ทันที ให้รัน `bash <path-ของ-taste-skill>/install.sh`

## ข้อควรระวัง

- Container ของ session เป็นแบบชั่วคราว ไฟล์ที่ไม่ commit และ push จะหายเมื่อ session จบ
- ห้ามสร้าง PR ถ้าผู้ใช้ไม่ได้สั่ง
- เนื้อหาของ Building Myself ต้องไม่แต่งประสบการณ์ส่วนตัวแทน Creator ถ้าข้อมูลไม่พอให้ถามก่อน
