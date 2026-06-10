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
| Foxpro file path | `C:\pos\` |
| Xstore import tool | `run_dataloader.bat` |
| Database | [supabase.com/dashboard](https://supabase.com/dashboard) |

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Publish fails (401 Unauthorized) | Run in Supabase SQL Editor: `ALTER TABLE invoices DISABLE ROW LEVEL SECURITY;` |
| Supabase project paused | Go to supabase.com/dashboard → click **Restore project** |
| Store cannot re-download | HQ clicks **↺ Reset DL** in the Manage Invoices tab |
