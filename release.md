# Rider Manager — Release Notes

---

## v2.4 — 10 June 2026

### 📱 Phone Number in Settings
- Users can now enter their WhatsApp/phone number in Settings page
- Stored in `profiles.phone` column
- Shown in admin panel (Users tab) and Subscriptions table alongside email

### 📧 Automated Email Reminders (Supabase pg_cron)
- Daily cron job runs at 9am MYT (1am UTC)
- Sends expiry reminder emails via Resend (`no-reply@redbearstudio.my`)
- Smart send schedule — sends on day N, day 3, and day 1 before expiry:
  - Reminder days = 7 → sends on day 7, day 3, day 1
  - Reminder days = 5 → sends on day 5, day 3, day 1
  - Reminder days = 3 → sends on day 3, day 1
- Deduplication via `reminder_sent_dates` jsonb column — never sends same reminder twice
- Auto-clears `reminder_sent_dates` when user renews (trigger on profiles table)
- Email includes green **💬 WhatsApp to Renew** button with pre-filled message: *"Hi admin, I want to renew my subscription, my email is xxx, my name is xxx"*

### 🗄️ Supabase Changes
- `profiles.phone` — user's WhatsApp/phone number
- `profiles.reminder_sent_dates` — tracks which reminders sent per expiry cycle
- `send_expiry_reminders()` — PL/pgSQL function called by cron
- `clear_reminder_on_renew()` — trigger that resets reminder tracking on renewal
- `pg_cron` + `http` extensions enabled
- Cron scheduled: `0 1 * * *` (daily 1am UTC = 9am MYT)

---

## v2.3 — 10 June 2026

### 📋 User Subscription Section in Settings
- Regular users now see a **Subscription** section in their Settings page
- Shows current status badge (Active / Trial / Expired / Blocked)
- Shows expiry date + days remaining or days overdue
- Shows full payment history: date, months, amount, notes
- Hidden for admin account

### 🎨 Admin Panel UI Improvements
- **Billing tabs** (Subscriptions / Payment History / Users) redesigned as clear button-style tabs — outlined when inactive, solid teal when active
- **Action buttons** in Subscriptions table now have clear hierarchy: 💰 Renew (purple, primary) / ♾ Forever (green) / Expire (outlined grey, secondary)

---

## v2.2 — 9 June 2026

### 💳 Billing Tabs in Admin Panel
Admin panel now has 3 tabs: **Subscriptions / Payment History / Users**

**Subscriptions tab** (default)
- Table view: Name, Email, Status badge, Expiry Date, Days Left
- 3 action buttons per row: **Renew** / **♾ Forever** / **Expire**
- Sorted: Active → Trial → Expired → Blocked
- Forever shows ♾, days overdue shown in red

**Payment History tab**
- All payments across all users, newest first
- Shows: name, email, payment date, months, amount, notes

**Users tab**
- Existing user management with batch actions
- Added **⏱ Expire** button per user card

### 💰 Record Payment Modal
- Opens on Renew — fields: Payment Date, Months, Amount (RM), Notes
- Live preview of new expiry date as months are typed
- **Custom date toggle** — override calculated expiry with a specific calendar date

### ♾ Mark Active Forever
- Sets expiry 100 years in the future
- Available in Subscriptions table and Users tab

### ⏱ Mark as Expired
- Sets expiry to yesterday for easy testing of expired state
- Available in Subscriptions table and Users tab

### 🔧 Startup Fix
- Fixed double `initApp` call on page load (was causing infinite loading)
- Added 8-second timeout fallback — if Supabase doesn't respond, shows auth screen

---

## v2.1 — 9 June 2026

### 📅 Subscription Expiry Dates
- Subscriptions now have explicit expiry dates (`subscription_expiry_date` column)
- Status derived automatically: Active (future expiry) / Expired (past expiry) / Trial (no expiry set) / Blocked (manual override)
- Warning banner shown N days before expiry (configurable in admin panel)

### 🛠️ Admin Panel — Complete Overhaul
- **Filter tabs**: All / Active / Trial / Expired / Blocked with user counts
- **Stats row**: Total / Active / Expiring Soon
- **Configurable reminder days** setting alongside price setting
- **Per-user renew options**: +1M / +3M / +6M / +12M / ♾ Forever / Custom months input
- **Checkboxes** for batch selection + Select All
- **Batch action bar** (appears when users selected):
  - Quick renew: +1M / +3M / +6M / +12M / ♾ Forever
  - Custom months input
  - Set specific expiry date for all selected
  - Block all selected
- Expiry date + days remaining/overdue shown per user card
- Newest users sorted to top; NEW badge for users joined in last 24h

### ♾ Active Forever
- Setting a user to Forever sets expiry 100 years in the future
- Available both per-user and in batch mode

---

## v2.0 — 8 June 2026

### 👤 Admin Panel — Email Display
- User email now shown instead of UUID
- Email stored in `profiles` on signup/login automatically
- Backfills email for existing users on next login
- SQL backfill available: `UPDATE profiles p SET email = u.email FROM auth.users u WHERE p.id = u.id`

### 📱 Admin — Mobile Bottom Nav
- Admin tab now appears in mobile bottom nav (hidden for non-admin users)
- Red dot badge on Admin tab when new users signed up in last 24h

### 🔔 New User Notifications
- On login, admin sees red dot badge on Admin nav if new users joined in last 24h
- New users (last 24h) highlighted with purple border and NEW badge in admin panel
- Newest users sorted to top of list

### 🔒 Read-Only Mode (replaces hard block)
- Blocked/expired users now see the app in read-only mode instead of a hard block screen
- Red banner at top: *"Your subscription has ended. You're in view-only mode."*
- All action buttons hidden: no FAB, no Edit/Delete/Complete/Mark Paid/Cancel on trips
- Green WhatsApp button in banner: *"Whatsapp to subscribe → RM 30/month"*
- Pre-filled WhatsApp message includes user's name and email

---

## v1.9 — 8 June 2026

### 🔐 Subscription System
- **Free trial** — new users automatically get a 7-day free trial on first login
- **Trial banner** — amber sticky banner at top of content showing "Free trial: X days remaining"
- **Blocked screen** — shown when trial expires or account is blocked; displays subscription price, admin name, and WhatsApp contact button
- **Admin panel** — hidden nav item (visible only to `redbear.my@gmail.com`):
  - Lists all users with name, join date, trial status and days remaining
  - Approve / Reset Trial / Block buttons per user
  - Configurable subscription price (saved to `app_settings` table)
- Admin always has full access regardless of subscription status

### 🗄️ Supabase Changes
- `profiles` table: added `trial_start_date`, `is_approved`, `subscription_status` columns
- New `app_settings` table: `subscription_price`, `admin_whatsapp`, `admin_name`, `trial_days`
- RLS policies: admin can read/update all profiles; users can read app_settings

---

## v1.8 — 8 June 2026

### ⚙️ Settings — Button Labels & Icons
- "Export trips to CSV" → "Export trip data as Excel"
- "Export expenses to CSV" → "Export expenses data"
- "Import trips from CSV" → "Import trip from Excel"
- Export buttons now use ↑ upload arrow icon; Import button keeps ↓ download arrow
- Removed CSV columns hint text

### 📥 Import Trips — Excel Format
- Import now reads `.xlsx` files using SheetJS (previously CSV)
- Columns match exactly what the export produces: Trip ID, Date, Time, Guest Name, Guest Contact, Pickup/Dropoff Locations, Adults, Kids, Small/Medium/Big Luggage, Charge (RM), Status, Completed, Cancelled, Cancellation Reason, Paid, Notes
- Handles time format `06:00:00` and `06:00` correctly
- Smart dedup: compares incoming rows against existing data before importing
- Toast shows accurate breakdown: `X new, X updated, X unchanged, X invalid`

### 📤 Export Trips — Export All option
- Trip export modal now has an **Export all trips** checkbox
- When checked, hides month/date range fields and exports entire dataset
- Filename: `Username_trips_all.xlsx`

---

## v1.7 — 7 June 2026

### 🔁 Import Trips — Duplicate Prevention
- Import now uses **upsert** instead of plain insert
- If a trip with the same `trip_id` already exists → record is **updated**
- If `trip_id` is new → **inserted** as normal
- Trips without a `trip_id` → always inserted fresh
- Safe to re-import the same file multiple times without creating duplicates
- **Pre-requisite:** unique index added on `trips(trip_id)` in Supabase; 87 pre-existing duplicate rows cleaned up

---

## v1.6 — 7 June 2026

### 🐛 Fix: Expense Export Modal Not Opening
- Root cause: `setExportTab()` and `getExportDateRange()` used wrong element ID prefix (`expense-` instead of `exp-`), causing silent crash before modal could open
- Fixed both functions to consistently use short prefix `exp-` / `trip-`

### 📊 Expense Export — Smarter UX
- Export modal no longer asks you to reselect dates — reads From/To directly from the expense page filters and shows the range as a summary
- Hint text guides user to update filters on the Expenses page if needed

### 📝 Excel Export — Username & Report Headers
- Filename now prefixed with username e.g. `Derred_expenses_2026-06-01_to_2026-06-30.xlsx`
- Excel file starts with header rows: Name → "Expenses Report" → date range → blank row → column headers

---

## v1.5 — 6 June 2026

### 📅 Expense Filter — Month Picker
- Added a **Month** input to the expense filter bar, to the left of the From/To date fields
- Selecting a month auto-fills From and To to the first and last day of that month
- Manually changing From/To clears the month picker
- Clear ✕ button resets all three fields at once

---

## v1.4 — 6 June 2026

### 📊 Excel Export for Expenses & Trips
- Export Expenses now generates `.xlsx` instead of CSV
- Export Trips now generates `.xlsx` instead of CSV
- Uses SheetJS for proper Excel formatting with auto column widths
- Amount/Charge columns stored as numbers for Excel SUM formula support

---

## v1.3 — 6 June 2026

### 💸 Expense Page — Export Button in Filter Section
- Added a green **Export Expenses** button at the bottom of the filter bar
- Tapping it opens the Export modal (select month or date range)
- More accessible than going to Settings to export

---

## v1.2 — 5 June 2026

### 📤 Export Expenses — Month or Date Range
New export modal for expenses (accessible from Settings → Export Expenses to CSV):
- **By Month** tab — pick any month from a month picker; defaults to current month
- **Date Range** tab — pick a custom From / To date
- Exports filtered rows only with columns: Date, Type, Amount (RM), Location, Notes
- Filename includes the selected period (e.g. `expenses_2026-05.csv` or `expenses_2026-04-01_to_2026-04-30.csv`)
- Shows toast with count of exported rows

### 📤 Export Trips — Month or Date Range
New export modal for trips (replaces the old one-click export in Settings):
- **By Month** tab — pick any month; defaults to current month
- **Date Range** tab — pick a custom From / To date
- Exports same columns as before but filtered to the selected period
- Filename includes the selected period (e.g. `trips_2026-05.csv`)

---

## v1.1 — 26 May 2026

### 🧾 Create Trip Form — Layout Reorder
Reorganised the field order in the Create/Edit Trip form for a more natural booking flow:
- **Guest Name & Contact** moved up to directly after Date & Time
- **Charge (RM) & Notes** moved up to directly after Dropoff Locations
- Passengers and Luggage sections remain below, keeping data-entry priority on the essentials first

### 🎛️ Passengers & Luggage — Stepper UI
Replaced plain number inputs with a grouped card stepper layout:
- Passengers (Adults / Kids) grouped in one card with dividers
- Luggage (Big / Medium / Small) grouped in a second card
- Each row has a grey `−` button, bold count, and teal `+` button
- Matches the look and feel of the existing mobile app

### 💸 Expenses — Search & Filter
Added filtering tools to the Expenses page:
- **Date range picker** — From / To date inputs; list filters instantly on change
- **Category chips** — pill buttons auto-generated from actual expense types (All, Petrol, Toll, Meal, Maintenance, Others, plus any custom categories); active chip highlighted in teal
- **Result summary** — shows count and subtotal (e.g. `5 results · RM 180.00`) when filters are active
- **Clear ✕ button** — resets both date range and category in one tap
- Filters work in combination (e.g. April + Petrol)

---

## v1.0 — Initial Release

- Trip management (CRUD) with dynamic pickup/dropoff location fields
- WhatsApp deep links with Malaysian +60 number normalisation
- Waze deep links per location
- Calendar with busy time management and full-day toggle
- Expense tracking with custom categories (persisted in localStorage)
- Expense export to CSV and PDF (browser print) with grouping by category or date
- Reports page with period-based earnings summaries (This Week / This Month / Custom)
- Settings: JSON and CSV import/export with smart upsert logic
- Google OAuth authentication via Supabase
- Hosted as single HTML file on Netlify


---

## v1.1 — 26 May 2026

### 🧾 Create Trip Form — Layout Reorder
Reorganised the field order in the Create/Edit Trip form for a more natural booking flow:
- **Guest Name & Contact** moved up to directly after Date & Time
- **Charge (RM) & Notes** moved up to directly after Dropoff Locations
- Passengers and Luggage sections remain below, keeping data-entry priority on the essentials first

### 🎛️ Passengers & Luggage — Stepper UI
Replaced plain number inputs with a grouped card stepper layout:
- Passengers (Adults / Kids) grouped in one card with dividers
- Luggage (Big / Medium / Small) grouped in a second card
- Each row has a grey `−` button, bold count, and teal `+` button
- Matches the look and feel of the existing mobile app

### 💸 Expenses — Search & Filter
Added filtering tools to the Expenses page:
- **Date range picker** — From / To date inputs; list filters instantly on change
- **Category chips** — pill buttons auto-generated from actual expense types (All, Petrol, Toll, Meal, Maintenance, Others, plus any custom categories); active chip highlighted in teal
- **Result summary** — shows count and subtotal (e.g. `5 results · RM 180.00`) when filters are active
- **Clear ✕ button** — resets both date range and category in one tap
- Filters work in combination (e.g. April + Petrol)

---

## v1.0 — Initial Release

- Trip management (CRUD) with dynamic pickup/dropoff location fields
- WhatsApp deep links with Malaysian +60 number normalisation
- Waze deep links per location
- Calendar with busy time management and full-day toggle
- Expense tracking with custom categories (persisted in localStorage)
- Expense export to CSV and PDF (browser print) with grouping by category or date
- Reports page with period-based earnings summaries (This Week / This Month / Custom)
- Settings: JSON and CSV import/export with smart upsert logic
- Google OAuth authentication via Supabase
- Hosted as single HTML file on Netlify
