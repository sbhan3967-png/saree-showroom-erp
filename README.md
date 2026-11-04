# v0.2.3 - READ THIS FIRST
- NEW IN POS: search a customer by name or mobile right on the bill, and add a new customer inline in POS (name, mobile, type, WhatsApp, email, state, address) — it saves and selects them without leaving the bill or clearing your billed sarees, discount or payments.
- NEW: sell a saree by name — type 2+ letters in "sell by saree name", pick the saree, Add Piece adds its oldest available barcoded piece. Barcode scan still works exactly as before.
- FIXED: a product created with opening stock (e.g. 3 pieces) wrongly showed double stock (6). New products now show the correct stock.
- AUTO-UPDATE: if you are already on v0.2.2, v0.2.3 downloads in the background and installs on the next launch — no manual download needed. If you are still on v0.2.1 (empty update channel), install v0.2.3 once manually (unzip to a NEW folder, copy your old data\saree.db in), and auto-update works from then on.

# v0.2.2 - READ THIS FIRST
- NEW: Suppliers section now has an "Add New Supplier" panel (name is the only required field) plus a Supplier Directory table. Add a supplier there first, then they appear in Purchase, Payment and Ledger lists.
- AUTO-UPDATE: from v0.2.2 onward the update channel is already set in Settings to the official GitHub channel, mode install_on_restart. Future updates download in the background and install on the next launch — you do NOT need to re-download manually. The updater never touches data\ or backups\, and never overwrites the running Python/launcher.
- NOTE: v0.2.1 could not auto-update to v0.2.2 on its own, because v0.2.1 shipped with an empty update channel. Install v0.2.2 once from GitHub (unzip to a NEW folder, copy your old data\saree.db in, as in UPDATE-INSTRUCTIONS.txt), and auto-update works from then on.

# FIXED PACKAGE v0.2.1 - READ THIS FIRST
- Demo data REMOVED: the old v0.2.0 portable zip shipped with 100 demo products/customers/sales. This fixed package starts EMPTY on first run.
- NOT Electron: the .exe is a .NET launcher that starts the bundled Python engine and opens Chrome/Edge in app-window mode. There is no Electron runtime in this zip.
- Data lives in data\ inside THIS folder, NOT in %APPDATA%. Before updating, run BACKUP-DATA.bat and follow UPDATE-INSTRUCTIONS.txt - never unzip a new version over your live data\ folder.
- Fixed launchers: Saree-ERP.bat now uses python-win\python.exe (no system Python needed). Launch-Desktop-App.bat starts the .exe, or falls back to Saree-ERP.bat. The old Launch-Electron-App.bat now just calls Launch-Desktop-App.bat.

---
# Saree Showroom ERP — v0.2.0 (Phase 2 + Electron + Google Drive backup)

## Install on your Windows showroom PC (Electron desktop app, no browser tab)
1. Download `Saree-ERP-Windows-v0.2.0.zip`, unzip it to a folder (for example `C:\SareeERP`).
2. Double-click **`Saree Showroom ERP.exe`**. That is the app — a desktop window, with its own icon. (Technically a .NET launcher + Chrome/Edge app window, not Electron.)
3. No Python or Node needed: the folder already contains a bundled embeddable Python that runs the ERP engine hidden behind the window. No system Python is required.
4. Your database lives in `data\saree.db` inside THIS app folder, NOT in %APPDATA%. So updating or reinstalling DOES risk your data if you unzip over the old folder — always run BACKUP-DATA.bat first and follow UPDATE-INSTRUCTIONS.txt.
5. To update later: DO NOT replace the app folder in place. Run BACKUP-DATA.bat, unzip the new version to a NEW folder, copy your old data\saree.db into the new folder's data\ folder, then start it. See UPDATE-INSTRUCTIONS.txt. The in-app auto-update (install_on_restart) still applies when a channel URL is set in Settings.

The separate real Electron build (122 MB, with Electron runtime and data in %APPDATA%) is a different package from this portable .NET-launcher package. Do not mix the two folders.

## Google Drive backup — so a damaged laptop does not lose your database
Yes, this is built in, and it works with your Gmail Drive account without the app
ever storing your Gmail password:
1. On the showroom PC, install **Google Drive for Desktop** and sign in with
   your Gmail (Google's own app, your own sign-in).
2. In the ERP, go to Settings → Google Drive backup, and set the backup folder
   to a folder inside your Google Drive folder, for example
   `C:\Users\YourName\Google Drive\Saree ERP Backups`.
3. Click **Backup to Google Drive now**, or tick auto-copy so every backup is
   also copied there. Google Drive for Desktop syncs that file to your Drive
   account in the background.
4. If the laptop is damaged: install the app + Google Drive for Desktop on the
   new PC, sign in with the same Gmail, let Drive sync, then in the ERP go to
   Settings → Show backups → type the backup file name → Stage restore → close
   and reopen the app. The current database is saved first as a pre-restore copy.
Local backups are still kept too, in the data folder's `backups/` subfolder, so
the app works fully even with no Drive folder set.

## What is new in v0.2.0 (tested 2026-11-04) - NOTE: the Electron claims below describe the separate 122 MB Electron build, NOT this portable .NET-launcher package.
- Phase 2 complete: purchase returns (stock + supplier payable fall, tax/GST
  reversed, double-return blocked), supplier payments (overpayment blocked),
  supplier ledger with running balance, stock movement history view.
- Electron desktop shell: single window, spawns the engine, kills it on quit,
  single-instance, data in user-data folder. Verified: Electron launched under
  Xvfb, spawned its server, served the dashboard, and created its database at
  `~/.config/saree-showroom-erp/data/saree.db` (the Linux equivalent of %APPDATA%).
- Google Drive backup + restore staging, tested against a fake Drive folder
  (local copy, Drive-folder copy byte-identical, missing-folder error is clear).
- All v0.1.0 scenario tests still pass; all journals still balance.

---

# Saree Showroom ERP — v0.1.0 notes (kept below)

Single-showroom, owner-controlled ERP. No multi-branch, no user roles, no logins —
exactly as specified. Employees/salespersons are names on transactions only.

## Run it (Windows showroom PC)
1. Install Python 3.8+ (python.org, tick "Add python to PATH").
2. Double-click `Saree-ERP.bat`. It opens http://127.0.0.1:8765 in your browser.
3. No npm, no Node, no PostgreSQL to install. Python standard library + SQLite only.

Why not the brief's React/Node/PostgreSQL stack? That stack needs a server and a
build step on the showroom PC, which is exactly what broke other installs here
before. This build gives the same sidebar/dashboard/POS behaviour in the browser
with zero build step, so it runs and auto-updates reliably. The database schema
(36 tables in `app/db.py`) is the PostgreSQL-ready design, ported to SQLite for
single-showroom use; the service layer (`app/services.py`) is stack-independent.

## What works in v0.1.0 (tested, not dummy)
- Dashboard: every card from real transactions (sales, cash/UPI/card, udhar,
  advances, purchases, expenses, stock values, balances). No hardcoded numbers.
- Product Master: all saree fields, 23 default categories, filters
  (name/SKU/category/colour/fabric/price/status), opening stock.
- Individual saree tracking: each piece gets a unique barcode (SR…); a sold
  barcode cannot be sold again; returns restore it only if saleable.
- Purchase: stock + barcodes created on save, supplier outstanding, GST
  (CGST+SGST intra-state, IGST inter-state, inclusive/exclusive modes).
- POS: scan barcode, split payment (Cash/UPI/Card/Bank), unpaid balance becomes
  Udhar automatically, advance adjustment, held-bill logic via request IDs
  (re-submitting the same bill never duplicates it).
- Customers & Khata: mobile-duplicate blocked, ledger with running balance,
  udhar collection, advance receipt (liability, never sales revenue).
- Returns: credit note linked to the original invoice, stock restored if saleable.
- Stock adjustment: reason mandatory, audited.
- Expenses, Day Book (cash/bank), Udhar ageing buckets (0-30/31-60/61-90/91-180/180+).
- Audit log on every mutation; every journal entry is checked balanced.
- Backup: Settings → "Backup database now" (also `backups/` folder).

## Tag customization — by tag SIZE (your 4 Nov addition)
Settings → Tag Studio, and Inventory → Barcode & Tag Studio.
- Saved sizes: 25×15, 38×25, 50×30, 50×25, 75×50, 100×60 mm.
- A small tag carries fewer fields (25×15: product, price, barcode only);
  a large hang tag carries showroom, SKU, category, colour, fabric, design,
  MRP, price and barcode. Font size scales with the template.
- Create any custom size: name + width mm + height mm + tick the fields for
  that size + layout (compact/standard/luxury).
- Preview is live; Print uses `@page { size: Wmm Hmm }`, so a 50×30 tag prints
  exactly 50×30 mm. Barcodes are Code39 (scannable) rendered in the browser.

## Auto-update — future updates install automatically (your 4 Nov instruction)
Default mode: `install_on_restart` (Settings → Company, GST & Auto-Update).
- On launch, the app checks the channel `version.json`, downloads a new version
  in the background, verifies its SHA-256, and stages it.
- It installs on the NEXT launch — never mid-sale, never by restarting itself.
- `data/` (your database) and `backups/` are never touched by an update.
- Before replacing code, the old code is backed up to `backups/code-before-<version>/`
  for rollback (`app/updater.py: rollback()`).
- A zip with a wrong SHA-256 is rejected and never installed (tested).
- Modes: `install_on_restart` (auto, default) / `notify_only` / `off`.
- To publish a new version: `python3 tools/publish_update.py --version 0.2.0
  --out channel/ --changelog "..."`, upload `channel/saree-erp-0.2.0.zip` +
  `channel/version.json` to your host, and put that version.json URL in Settings
  → Update channel. The zip never contains data/ or backups/.

## Test it
`python3 tests/test_scenarios.py` — runs the brief's scenarios 1, 2, 3, 4, 5
(variant), 6, 8, 9, 10 plus journal-balance, tag-size and auto-update checks.
All passed on 2026-11-04.

## Data
`data/saree.db` (SQLite). Keep this folder safe — it IS the business. Backups
go to `backups/`. Never delete `data/` when updating.

See `IMPLEMENTATION_CHECKLIST.md` for what is done and what remains (Phases 2–8).
