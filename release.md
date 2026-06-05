# Rider Manager — Release Notes

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
