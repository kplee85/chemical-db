# Chemical DB — Farm OS

A field-ready data entry tool for building a pesticide and fungicide database. Part of the Farm OS ecosystem.

**Live tool:** `https://kplee85.github.io/chemical-db`

---

## What it does

- Snap or upload a photo of a pesticide/fungicide label → AI extracts all fields automatically
- Manual entry fallback for any field the OCR misses
- Saves records to Google Sheets (`Chemical_DB`) via Apps Script
- Local browser cache as backup if Sheets is unavailable
- Search and review all saved records
- Export full database as CSV

---

## Fields captured

| Field | Source |
|---|---|
| Product Brand Name | Label / Manual |
| Active Ingredient | Label / Manual |
| Formulation Type | Label / Manual |
| Type (Fungicide / Insecticide / etc.) | Label / Manual |
| IRAC Group | Label / Manual |
| FRAC Group | Label / Manual |
| Mode of Action | Label / Manual |
| PHI — Pre-Harvest Interval (days) | Label / Manual |
| REI — Re-Entry Interval (hours) | Label / Manual |
| Dosage Rate | Label / Manual |
| Registered Crop | Label / Manual |
| Manufacturer / Distributor | Label / Manual |
| Toxicity Class (WHO) | Label / Manual |
| Registration No. (BPPH) | Label / Manual |
| Notes | Manual |

---

## Setup

### 1. Deploy to GitHub Pages

```
1. Create a new GitHub repo (e.g. chemical-db)
2. Upload index.html as the only file
3. Settings → Pages → Branch: main → Save
4. Tool is live at: https://kplee85.github.io/chemical-db
```

### 2. Connect Google Sheets (Apps Script)

The tool writes to `Chemical_DB` Google Sheet via an Apps Script Web App. No credentials are stored in the frontend.

**Sheet ID:** `11x4hmB3AuTDSVK7shNrXRnNzK55XhnpN-bcVYd-bl0s`

Steps:

```
1. Open Chemical_DB Google Sheet
2. Extensions → Apps Script
3. Delete existing code, paste the script from the Setup tab in the tool
4. Deploy → New deployment
   - Type: Web app
   - Execute as: Me
   - Access: Anyone
5. Copy the Web App URL
6. Paste into Setup tab → Apps Script URL field
7. Click Test to verify
```

The Apps Script auto-creates headers on first write. Column order:

```
Timestamp | Brand Name | Active Ingredient | Formulation | Type |
IRAC Group | FRAC Group | Mode of Action | PHI (days) | REI (hours) |
Dosage Rate | Registered Crop | Manufacturer | Toxicity Class |
Registration No. | Notes
```

### 3. Enable AI Label Extraction (OCR)

The photo extraction uses Claude Haiku via the Anthropic API.

```
1. Get an API key from console.anthropic.com
2. Open tool → Setup tab → paste key into Anthropic API Key field
3. Key is stored in browser localStorage only — never transmitted except to api.anthropic.com
```

Cost: approximately $0.001 per label scan using Haiku model. Negligible for 20–30 products.

---

## Usage

### Adding a new product

1. Open tool → **Data Entry** tab
2. **Option A — Photo:** Tap the upload zone, snap or select label photo → AI fills fields (highlighted green) → review and correct anything wrong → Save
3. **Option B — Manual:** Fill fields directly → Save
4. Record saves to Google Sheets and local browser cache

### Reviewing records

- **Records** tab → searchable by brand name, active ingredient, or type
- Badges show Type, IRAC, FRAC, PHI, REI at a glance
- **Export CSV** button → downloads full database as `Chemical_DB_YYYY-MM-DD.csv`

---

## Data flow

```
Label photo / Manual input
        ↓
AI extraction (Claude Haiku via Anthropic API)
        ↓
Editable form — always review before saving
        ↓
POST → Apps Script Web App
        ↓
Google Sheets (Chemical_DB) + Browser localStorage (backup)
        ↓
Future: migrate to Supabase
```

---

## Tech stack

| Component | Technology |
|---|---|
| Frontend | Single-file HTML/CSS/JS — no framework, no build step |
| Hosting | GitHub Pages |
| OCR / AI | Anthropic Claude Haiku (`claude-haiku-4-5-20251001`) |
| Database | Google Sheets (staging) → Supabase (planned) |
| Sheets middleware | Google Apps Script Web App |
| Offline cache | Browser localStorage |

---

## Files

```
chemical-db/
└── index.html    — entire tool, single file
```

---

## Deployment workflow

Same as AgriConvert — edit directly on GitHub, upload only changed files. No build process.

If service worker is added in future, bump `CACHE_NAME` on every update.

---

## Planned next steps

- [ ] Migrate storage from Google Sheets to Supabase
- [ ] Link chemical records to spray logs in Farm OS Notion
- [ ] Resistance rotation checker (flag if same IRAC/FRAC group used consecutively)
- [ ] PHI countdown based on last spray date
- [ ] PDF label import (MSDS / registration documents)
- [ ] BPPH registry real-time lookup by registration number

---

## Part of Farm OS

| Tool | URL |
|---|---|
| AgriConvert Pro | `kplee85.github.io/agriconvert` |
| Chemical DB | `kplee85.github.io/chemical-db` |

Farm OS stack: Python · Notion · Obsidian · Telegram · Discord · FNNAS
