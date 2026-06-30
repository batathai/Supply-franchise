# DATABASE.md — Supabase Schema

Project: `ohoropbhdypmomgsqjwt` ([supabase.com/dashboard](https://supabase.com/dashboard)) — ใช้เป็น backend ของ `supply.batathai.com` (database + storage)

## Tables

### `invoices`
เก็บ invoice ที่ HQ อัปโหลดให้แต่ละสาขาดาวน์โหลด (อัปโหลดซ้ำจะ upsert ทับของเดิม)

### `downloads`
log การดาวน์โหลด invoice ของแต่ละสาขา ใช้คุมสถานะ "ดาวน์โหลดแล้ว/ยัง" — HQ กด **Reset DL** เพื่อปลดล็อกให้ดาวน์โหลดซ้ำได้

### `stock_uploads`
บันทึกประวัติการอัปโหลดไฟล์ Stock ของสาขา Xstore
```sql
create table stock_uploads (
  id bigint generated always as identity primary key,
  store_id text not null,
  inquiry_filename text,
  recap_filename text,
  uploaded_at timestamptz default now()
);
```
ใช้ทำเช็คลิสต์ในหน้า HQ ว่าสาขาไหน (30012–30016) ส่งไฟล์แล้วบ้าง พร้อมวันเวลาล่าสุด — ตารางเช็คลิสต์ดึงเฉพาะ "แถวล่าสุดต่อสาขา"

## Storage Buckets

| Bucket | ใช้เก็บ | Public |
|--------|---------|--------|
| (bucket invoices เดิม) | ไฟล์ Invoice จาก HQ | ใช่ |
| `stock-files` | ไฟล์ Stock จากสาขา Xstore | ใช่ |

### รูปแบบชื่อไฟล์ใน `stock-files`
- `{store_id}_Stock_Inquiry.xlsx`
- `{store_id}_Stock_Recap_by_Category.csv`

ตรงกับที่ `pos_extract_full.py` / `merge_stock_dat_stores.py` ใช้อ่านไปต่อ

## Row Level Security (RLS)
- เว็บเรียก Supabase ตรงด้วย `anon key` ฝังในโค้ด client-side — ปลอดภัยเพราะคุมสิทธิ์ด้วย RLS policy แทน ไม่ใช่การซ่อนคีย์
- ปัญหาที่เคยเจอ: publish ล้มเหลว (401 Unauthorized) → แก้ด้วย `ALTER TABLE invoices DISABLE ROW LEVEL SECURITY;` (กรณีฉุกเฉิน) หรือเช็ค policy ของ `stock_uploads` ว่าอนุญาต public `select` + `insert` หรือยัง

## ความจุที่ใช้ (ล่าสุดที่เช็ค)
- Database: ~0.034 / 0.5 GB (7%)
- Storage: ใกล้ 0 / 1 GB — เหลือเฟือสำหรับไฟล์ Stock ของ 5 สาขา (ไฟล์ละไม่กี่ร้อย KB ต่อสัปดาห์)

## Quick Reference
| Item | Value |
|------|-------|
| Supabase Dashboard | supabase.com/dashboard |
| Project ref | `ohoropbhdypmomgsqjwt` |
| Stock Bucket | `stock-files` |
| Stock Filename Format | `{store_id}_Stock_Inquiry.xlsx`, `{store_id}_Stock_Recap_by_Category.csv` |
