# BATA Services Portal

> **URL:** https://batathai.github.io/Supply-franchise/

---

## HQ — Publish Invoices

| # | Step |
|---|------|
| 1 | Open the portal → click the **BATA logo 3 times** to enter HQ mode |
| 2 | Enter password: `Bata@min1234` |
| 3 | Go to the **Upload & Publish** tab |
| 4 | Drag all supply files into the drop zone |
| 5 | Review the preview → click **Publish All to Portal** |
| 6 | Wait for the green ✅ success message |

> Re-publishing the same files is safe — the system uses upsert and will never duplicate data.

---

## Store — Upload Weekly Stock Files

| # | Step |
|---|------|
| 1 | Open the portal → click **Stock Upload** |
| 2 | Select your Store ID from the dropdown (`30012`–`30016` only) |
| 3 | Upload **Stock Inquiry** (`.xlsx`) — exported from Bata Reporting Client → Stock Inquiry |
| 4 | Upload **Stock Recap by Category** (`.csv`) — exported from Bata Reporting Client → Stock Recap By Category |
| 5 | Click **☁️ ส่งไฟล์** to submit |
| 6 | Check the list at the bottom — your store should show **✓ ส่งแล้ว** with today's date/time |

> Files are stored as `{store_id}_Stock_Inquiry.xlsx` and `{store_id}_Stock_Recap_by_Category.csv` in the `stock-files` Supabase Storage bucket. Re-uploading overwrites the previous file for that store (upsert), so it's safe to submit again if you made a mistake.

> Only stores `30012`–`30016` (Xstore, no automatic stock feed) need to do this. Stores `30002`–`30010` (Foxpro) already have stock pulled automatically via `zp_db.fil`.

---

## Store — Download Invoices

| # | Step |
|---|------|
| 1 | Open the portal → click **Store** |
| 2 | Select your Store ID from the dropdown |
| 3 | Tick the invoices you want → click **⬇ Download** |
| 4 | **Foxpro:** copy the `.txt` file to `C:\pos\` then run Foxpro import |
| 5 | **Xstore:** run `run_dataloader.bat` on the Desktop |

---

## Quick Reference

| Item | Value |
|------|-------|
| HQ Password | `Bata@min1234` |
| Foxpro Stores | `30002` – `30010` |
| Xstore Stores | `30011` – `30016` |
| Stock Upload Stores | `30012` – `30016` |
| Foxpro file path | `C:\pos\` |
| Xstore import tool | `run_dataloader.bat` |
| Stock Storage Bucket | `stock-files` (Supabase Storage) |
| Stock Filename Format | `{store_id}_Stock_Inquiry.xlsx`, `{store_id}_Stock_Recap_by_Category.csv` |
| Database | [supabase.com/dashboard](https://supabase.com/dashboard) |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Publish fails (401 Unauthorized) | Run in Supabase SQL Editor: `ALTER TABLE invoices DISABLE ROW LEVEL SECURITY;` |
| Supabase project paused | Go to supabase.com/dashboard → click **Restore project** |
| Store cannot re-download | HQ clicks **↺ Reset DL** in the Manage Invoices tab |
| Stock Upload submit fails | Check Supabase Storage bucket `stock-files` exists and is Public; check `stock_uploads` table has RLS policies allowing public `select` + `insert` |
| Stock Upload checklist stuck on "ยังไม่ส่ง" after submitting | Click **↻ Refresh** on the checklist — the table only shows the latest upload per store |