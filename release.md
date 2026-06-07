# Rider Manager — Release Notes

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
