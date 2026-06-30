# PROJECT_CONTEXT.md — บริบทโปรเจกต์

## วัตถุประสงค์
รวบรวมข้อมูลยอดขายและสต็อกจากทุกสาขา Bata Thailand (30002–30016) มาแสดงบน Power BI Dashboard อัตโนมัติทุกคืน และมีเว็บ `supply.batathai.com` สำหรับส่งเอกสาร Invoice/Stock ระหว่าง HQ กับสาขา

## กลุ่มสาขา — กฎสำคัญ ห้ามสับสน

| กลุ่ม | รหัสสาขา | ระบบ POS | แหล่งข้อมูลสต็อก | แหล่งยอดขาย |
|-------|----------|----------|-------------------|--------------|
| Foxpro | 30002–30010 | Foxpro | `zp_db.fil` (`stat.dbf`, `ram.dbf`) บน `\\192.1.1.5\pos\524C\store\` | `sal.dbf` ใน `zp_db.fil` เดียวกัน |
| Xstore | 30012–30016 | Xstore/Oracle | อัปโหลด Excel ผ่านเว็บ → Supabase Storage | ไฟล์ `.dat` รายวัน (`MMDDs{store}.dat`) |

> 30011 อยู่ระหว่างตรวจสอบสถานะ — Stock Upload ใช้งานจริงเฉพาะ 30012–30016

ข้อควรระวัง:
- Foxpro: Size Code ต้องอ่านจาก `ram.dbf` (SCODE) แล้ว map ด้วย GRIP_EU_MAP — **ห้าม hardcode EU34–46**
- Xstore: ยอดเงินสด/บัตรเครดิตอยู่ใน Oracle table `ttr_tndr_lineitm` ไม่ใช่ในไฟล์ `.dat`
- `Stock.csv` ต้องมีคอลัมน์ `size_code` หนึ่งแถวต่อหนึ่งไซส์ (ไม่ใช่ค่ารวม)
- กฎ fw_nfw: Article ขึ้นต้นด้วย `9` → NFW, นอกนั้น → FW

## ส่วนประกอบของระบบ

1. **`pos_extract.py`** (รันทุกคืนผ่าน Windows Task Scheduler) — ดึงจาก 3 แหล่ง (Foxpro zip, Xstore .dat, Supabase stock) → เขียน `Stock.csv`, `daily_sales.csv` มี `write_csv_with_retry()` รองรับไฟล์ถูกล็อก (เช่น เปิดค้างใน Excel)
2. **Power BI Dashboard** — เปิดไฟล์ CSV แสดงยอดขาย/สต็อก/Margin
3. **`supply.batathai.com`** (GitHub: `batathai/Supply-franchise`, static HTML, deploy ผ่าน Cloudflare Pages auto-deploy เมื่อ push เข้า `main`)
   - HQ Admin (รหัส `Bata@min1234`) — อัปโหลด Invoice, Reset DL, ดู Log
   - Stock Upload — สาขา Xstore (30012–30016) ส่งไฟล์ Stock รายสัปดาห์
   - แจ้งซ่อม — iframe ไป Google Apps Script form
4. **Supabase** (project: `ohoropbhdypmomgsqjwt`) — รายละเอียดดู `DATABASE.md`

## สถานะปัจจุบัน (ล่าสุด)
- Stock Upload (เมนูที่ 3) ใช้งานได้แล้ว เชื่อมกับ Supabase Storage bucket `stock-files`
- `pos_extract_full.py` รองรับ `detect_file_type()` แยกแยะ xlsx/csv/pdf/unknown กัน crash เมื่อสาขาอัปโหลดไฟล์ผิดชนิด
- ยังไม่ได้แก้: `pos_extract.py` ดึงไฟล์จาก Supabase Storage มา merge เข้า `Stock.csv` แบบอัตโนมัติเต็มรูปแบบ (บางส่วนทำแล้วใน `pos_extract_full.py`)
- Known issue: สาขา 30003 (Hokkee Bata) มี `size_code` ว่างเปล่าในบางแถว — ยังไม่ได้แก้ root cause

## เทคโนโลยีที่ใช้
| ส่วน | เทคโนโลยี |
|------|-----------|
| Data Pipeline | Python 3, pandas, dbfread, openpyxl, supabase-py |
| Automation | Windows Task Scheduler |
| Dashboard | Power BI Desktop |
| Web App | HTML/JS (Static), Cloudflare Pages / GitHub Pages |
| Database | Supabase (PostgreSQL) |
| File Storage | Supabase Storage |
| Network Drive | `\\192.1.1.5\pos\524C\store\` |

## บันทึกปิดงาน (2026-06-30)
สร้างชุดเอกสารอ้างอิงใหม่ใน `docs/` (`README.md`, `PROJECT_CONTEXT.md`, `CHANGELOG.md`, `DATABASE.md`, `DEPLOYMENT.md`) รวมข้อมูลจากเอกสารชุดก่อนหน้า (`PROJECT.md`, `ARCHITECTURE.md` ฯลฯ) ให้อยู่ในที่เดียว งานที่ค้างอยู่ดูที่ `TODO.md`

## วิธีคิดเมื่อมีปัญหา
1. ตรวจกลุ่มสาขาก่อน — Foxpro หรือ Xstore?
2. ตรวจแหล่งข้อมูล — ไฟล์อยู่ที่ไหน รูปแบบอะไร?
3. ตรวจ schema — คอลัมน์ครบไหม `size_code` ว่างหรือเปล่า?
4. ขอตัวอย่างข้อมูลจริงก่อนแก้โค้ดเสมอ อย่าเดา
