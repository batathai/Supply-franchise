# TODO.md

## 🔴 ยังไม่ได้ทำ (จาก backlog เดิม)
- [ ] แก้ `pos_extract.py` ให้ดึงไฟล์ Stock จาก Supabase Storage มา merge เข้า `Stock.csv` แบบอัตโนมัติเต็มรูปแบบ (ตอนนี้ยังทำผ่าน `pos_extract_full.py` บางส่วนเท่านั้น / ยัง manual export บางจุด)
- [ ] สอบสวนสาเหตุที่สาขา 30003 (Hokkee Bata) มี `size_code` ว่างเปล่าในบางแถวของ `Stock.csv`
- [ ] ยืนยันสถานะสาขา 30011 ว่าอยู่กลุ่มไหน (Foxpro หรือ Xstore) — ตอนนี้เอกสารระบุว่า "อยู่ระหว่างตรวจสอบ"
- [ ] เติมชื่อสาขาที่ยังว่างใน `PROJECT_CONTEXT.md` (30007–30010)

## 🟡 ควรพิจารณา
- [ ] ย้ายชุดเอกสารเก่า (`PROJECT.md`, `AI_MEMORY.md`, `ARCHITECTURE.md`, `KNOWN_ISSUES.md`, `REVIEW.md`, `BATA_WORKFLOW.md`) มารวม/อ้างอิงกับชุดใหม่ใน `docs/` เพื่อไม่ให้มีเอกสารซ้ำซ้อนสองชุด
- [ ] เพิ่ม automated test หรือ smoke test สำหรับ `pos_extract_full.py` ให้รันอัตโนมัติก่อน deploy แทนการเทสต์มือทุกครั้ง

## ✅ เสร็จแล้วรอบนี้ (2026-06-30)
- [x] สร้าง `docs/README.md`, `docs/PROJECT_CONTEXT.md`, `docs/CHANGELOG.md`, `docs/DATABASE.md`, `docs/DEPLOYMENT.md`
- [x] อัปเดต CHANGELOG, TODO, PROJECT_CONTEXT ตาม closing procedure
