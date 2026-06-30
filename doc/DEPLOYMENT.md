# DEPLOYMENT.md — การ Deploy ระบบ

## เว็บ `supply.batathai.com`

- **Repo**: [github.com/batathai/Supply-franchise](https://github.com/batathai/Supply-franchise) (public, static site, `HTML 100%`)
- **Hosting**: GitHub Pages (`batathai.github.io/Supply-franchise/`) เชื่อม CNAME มาที่ `supply.batathai.com`, ให้บริการจริงผ่าน **Cloudflare Pages** (project: `bata-convertpages`)
- **วิธี deploy**: แก้ไฟล์ (เช่น `index.html`) แล้ว push เข้า branch `main` → Cloudflare Pages auto-deploy ทันที ไม่ต้องตั้งค่าเพิ่ม
- **ไม่มี backend function** — ทุกอย่างเป็น client-side JavaScript เรียก Supabase REST API ตรงด้วย `anon key`

### ขั้นตอน deploy ทีละขั้น
1. แก้ไฟล์ `index.html` (หรือไฟล์อื่นใน repo)
2. ตรวจ diff ว่าไม่กระทบโค้ดเดิมที่ไม่เกี่ยวข้อง
3. Commit + push เข้า `main`
4. Cloudflare Pages จะ build/deploy อัตโนมัติ (ปกติเสร็จในไม่กี่นาที)
5. เปิด `supply.batathai.com` ทดสอบจริง (hard refresh / incognito ถ้า cache ค้าง)

## `pos_extract.py` (Data Pipeline)

- รันบนเครื่อง HQ ผ่าน **Windows Task Scheduler** ทุกคืน (ไม่ใช่ cloud)
- ต้องเข้าถึง Network Drive `\\192.1.1.5\pos\524C\store\` ได้ (สำหรับกลุ่ม Foxpro)
- ต้องมี credential อ่าน SQL Server (`192.1.1.37`, database `POSServer`) สำหรับยอดขาย Xstore
- ต้องเข้าถึง Supabase ผ่าน internet ได้ (ดึงไฟล์ Stock ที่สาขา Xstore อัปโหลด)
- Output: `Stock.csv`, `daily_sales.csv` → ใช้เปิดใน Power BI Desktop โดยตรง (refresh data source)
- มี `write_csv_with_retry()` กัน fail เวลาไฟล์ output ถูกเปิดค้างใน Excel/Power BI

## Power BI Dashboard
- ไม่ได้ deploy แบบ cloud — เปิดไฟล์ `.pbix` บนเครื่อง แล้วกด Refresh เพื่อดึง `Stock.csv` / `daily_sales.csv` ล่าสุด

## Environment / Credentials ที่เกี่ยวข้อง
| รายการ | ใช้ที่ไหน |
|--------|-----------|
| `Bata@min1234` | รหัส HQ Admin บนเว็บ |
| Supabase anon key | ฝังใน `index.html` (client-side, คุมสิทธิ์ผ่าน RLS) |
| SQL Server `192.1.1.37` (user `reader`) | `pos_extract.py` ดึงยอดขาย Xstore |
| `\\192.1.1.5\pos\524C\store\` | `pos_extract.py` ดึง Stock กลุ่ม Foxpro |

## Checklist ก่อน deploy การเปลี่ยนแปลงที่กระทบเว็บ
- [ ] ทดสอบบนไฟล์สำเนาก่อน ไม่แก้ตรงใน repo ทันที
- [ ] diff แล้วมีแต่ "เพิ่ม" ไม่ใช่ "ลบ/แก้" ของเดิม (ยกเว้นตั้งใจแก้)
- [ ] ตรวจว่า RLS policy ของ table/bucket ที่เกี่ยวข้องอนุญาตสิทธิ์ที่จำเป็นแล้ว
- [ ] หลัง push แล้วเปิดเว็บจริงทดสอบ flow ที่แก้ก่อนปิดงาน
