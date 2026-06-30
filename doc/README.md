# README — POS Data Pipeline & Supply Portal (Bata Thailand)

ระบบรวบรวมข้อมูลยอดขาย/สต็อกจากทุกสาขา Bata Thailand (30002–30016) เข้า Power BI Dashboard แบบอัตโนมัติทุกคืน พร้อมเว็บแอป `supply.batathai.com` สำหรับแลกเปลี่ยนเอกสารระหว่าง HQ กับสาขา

## เริ่มต้นอ่านจากตรงนี้

| ต้องการรู้... | อ่านไฟล์ |
|---------------|----------|
| ภาพรวมระบบ, กลุ่มสาขา, ส่วนประกอบ | `PROJECT_CONTEXT.md` |
| Schema, ตาราง, bucket ของ Supabase | `DATABASE.md` |
| วิธี deploy / โครงสร้าง repo / pipeline | `DEPLOYMENT.md` |
| ประวัติการแก้ไขแต่ละเวอร์ชัน | `CHANGELOG.md` |

## ส่วนประกอบหลัก

1. **`pos_extract.py`** — ดึงข้อมูลทุกคืนผ่าน Windows Task Scheduler จาก 3 แหล่ง (Foxpro `zp_db.fil`, Xstore `.dat`, Supabase Storage) → ออกเป็น `Stock.csv`, `daily_sales.csv`
2. **Power BI Dashboard** — เปิดไฟล์ CSV ที่สร้างขึ้น แสดงยอดขาย/สต็อก/Margin
3. **`supply.batathai.com`** (GitHub: `batathai/Supply-franchise`) — เว็บ static HTML บน Cloudflare Pages/GitHub Pages มี 3 โหมด: HQ Admin, Stock Upload (Xstore), แจ้งซ่อม
4. **Supabase** — PostgreSQL + Storage เป็น backend ของเว็บ (table `invoices`, `stock_uploads`, bucket `stock-files`)

## กลุ่มสาขา (สรุปย่อ)

| กลุ่ม | รหัสสาขา | แหล่งข้อมูลสต็อก |
|-------|----------|-------------------|
| Foxpro | 30002–30010 | `zp_db.fil` บน Network Drive |
| Xstore | 30012–30016 | อัปโหลดผ่านเว็บ → Supabase Storage |

รายละเอียดเต็มดูที่ `PROJECT_CONTEXT.md`
