# CHANGELOG.md

## [v1.4] — 2026-06-29
### เพิ่ม
- สร้างชุดเอกสารระบบ: `PROJECT.md`, `AI_MEMORY.md`, `ARCHITECTURE.md`, `CHANGELOG.md`, `KNOWN_ISSUES.md`, `REVIEW.md`, `BATA_WORKFLOW.md`
- คู่มือการใช้งานแยกสาขา: `คู่มือ_Xstore.md`, `คู่มือ_Foxpro.md`

## [v1.3] — 2026-06 (กลางเดือน)
### เพิ่ม
- `pos_extract_full.py`: เพิ่ม `detect_file_type()` แยกแยะไฟล์ xlsx/csv/pdf/unknown ก่อนประมวลผล Stock ของสาขา Xstore กันระบบ crash เมื่อสาขาอัปโหลดไฟล์ผิดชนิด
- เพิ่ม `write_csv_with_retry()` ใน `pos_extract_full.py` — retry การเขียนไฟล์สูงสุด 3 ครั้งเมื่อไฟล์ถูกล็อก (เช่น เปิดค้างใน Excel/Power BI) พร้อม error message ที่อ่านเข้าใจง่ายแทน traceback
### แก้ไข
- เมื่อสาขาอัปโหลดไฟล์ผิดชนิด (เช่น PDF แทน xlsx) ระบบจะคืนค่าว่างพร้อม error ที่ชัดเจน แทนที่จะ crash ทั้ง batch

## [v1.2] — 2026-06 (ต้นเดือน)
### เพิ่ม
- เว็บ `supply.batathai.com`: เพิ่มเมนู Stock Upload (โหมดที่ 3)
- สาขา Xstore (30012–30016) อัปโหลดไฟล์ Stock Inquiry/Recap รายสัปดาห์ผ่านเว็บ
- Supabase Storage bucket `stock-files`
- Table `stock_uploads` พร้อม checklist ฝั่ง HQ
### แก้ไข
- Troubleshooting: Stock Upload submit fails → ตรวจ RLS policy ของ `stock_uploads`
- Checklist ค้างที่ "ยังไม่ส่ง" หลัง submit → กด Refresh

## [v1.1] — 2026-06 (ก่อนหน้า)
### เพิ่ม
- `pos_extract.py`: รองรับ Xstore group (30012–30016) ดึงยอดขายจากไฟล์ `.dat`
- วิเคราะห์โครงสร้าง `.dat`: `GOW` record = sales, `RM80` record = payment/tender
- ยืนยันว่าข้อมูลเงินสด/บัตรเครดิตอยู่ใน Oracle DB ไม่ใช่ใน `.dat`
### แก้ไข
- `stock_check.py`: เปลี่ยนจาก hardcode article list เป็นอ่านจาก `articles.txt`

## [v1.0] — 2026-06 (เริ่มต้น)
### เพิ่ม
- `pos_extract.py` ดึงข้อมูล Foxpro group (30002–30010) จาก `zp_db.fil`
- ดึง Stock, Sales, Cost, Margin, Supplier จาก `stat.dbf`, `ram.dbf`, `sal.dbf`
- Power BI Dashboard หน้าแรก
- เว็บ `supply.batathai.com`: HQ Upload Invoice + สาขา Download Invoice

---

## [Unreleased] — 2026-06-30
### เพิ่ม
- สร้างชุดเอกสารใหม่ใน `docs/`: `README.md`, `PROJECT_CONTEXT.md`, `CHANGELOG.md`, `DATABASE.md`, `DEPLOYMENT.md` (รวมข้อมูลจากเอกสารชุดก่อนหน้าให้อยู่ในที่เดียว เน้นใช้อ้างอิงเร็ว)
### หมายเหตุ
- ปิดรอบงานเอกสาร (project closing procedure) — ดูรายละเอียดเพิ่มเติมใน `TODO.md`
