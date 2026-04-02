# Broadway Touring Dashboard

**Live URL:** https://rnunley901.github.io/broadway-touring-dashboard

Executive sales intelligence dashboard for Broadway touring productions. Built for The Bushnell Center for the Performing Arts.

---

## Current State

Hosted on GitHub Pages as a temporary measure while Azure Static Web Apps credentials are being provisioned. The dashboard is a single self-contained HTML file with no external dependencies beyond CDN-loaded libraries.

**Data source:** Embedded seed data + manual weekly import via drag-and-drop  
**Target data source:** Power Automate HTTP endpoint → SharePoint List (see Migration section)

---

## Setup (First Time)

### 1. Enable GitHub Pages

1. Go to **Settings → Pages** in this repo
2. Under **Source**, select **GitHub Actions**
3. Push any commit to `main` — the workflow deploys automatically

The site will be live at:
```
https://rnunley901.github.io/broadway-touring-dashboard
```

### 2. SharePoint iframe

Add a **Embed** web part to your SharePoint page with this code:

```html
<iframe
  src="https://rnunley901.github.io/broadway-touring-dashboard"
  width="100%"
  height="920"
  frameborder="0"
  style="border:none;">
</iframe>
```

Adjust `height` to fit your SharePoint page layout. A full-width section works best.

---

## Connecting Live Data (Power Automate)

When IT has the Power Automate HTTP trigger flow ready:

1. Open `index.html`
2. Find this line near the top of the `<script>` block:
   ```js
   const DATA_ENDPOINT = null;
   ```
3. Replace `null` with the HTTP trigger URL:
   ```js
   const DATA_ENDPOINT = 'https://prod-xx.eastus.logic.azure.com/...';
   ```
4. Commit and push — GitHub Actions redeploys in ~30 seconds

The dashboard will fetch live data from SharePoint on every page load. The manual import button and seed data remain as fallback if the endpoint is unreachable.

### Expected JSON format from Power Automate

The flow should return an array of objects matching this shape:

```json
[
  {
    "report_date": "2025-10-26",
    "tier": "Primary",
    "show": "Hamilton (Angelica)",
    "theatre": "Opera House",
    "city": "Boston",
    "ticket_range": "$8.00-$194.00",
    "top_paid_price": 294,
    "num_performances": 8,
    "gross_gross": 2507893,
    "gross_potential": 2395920,
    "gg_pct_gp": 104.7,
    "paid_tickets": 20700,
    "total_tickets": 20712,
    "total_capacity": 20720,
    "pct_capacity_paid": 99.9,
    "pct_capacity_total": 100,
    "on_subscription": 0,
    "avg_paid_admission": 121.08
  }
]
```

If SharePoint List column names differ, a field mapping block in `index.html` handles the translation — coordinate with the developer before going live.

---

## Migration to Azure Static Web Apps

When Azure credentials are available:

1. In the Azure Portal, create a **Static Web App**
2. Connect it to this GitHub repo (`main` branch, root folder `/`)
3. Azure auto-generates a new Actions workflow — delete the existing `deploy.yml` or let Azure replace it
4. Update the SharePoint iframe `src` to the new Azure URL
5. If a custom domain is configured (e.g. `touring.bushnell.org`), update the iframe `src` accordingly

No changes to `index.html` are required for the migration.

---

## File Structure

```
broadway-touring-dashboard/
├── index.html                  ← complete dashboard (single file)
├── README.md                   ← this file
└── .github/
    └── workflows/
        └── deploy.yml          ← GitHub Actions → GitHub Pages
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Dashboard | HTML / CSS / Vanilla JS |
| Charts | Chart.js 4.4.1 (cdnjs) |
| Excel parsing | SheetJS / xlsx 0.18.5 (cdnjs) |
| Fonts | Libre Baskerville, IBM Plex Sans/Mono (Google Fonts) |
| Hosting (temp) | GitHub Pages |
| Hosting (target) | Azure Static Web Apps |
| Data pipeline | Power Automate → SharePoint List |

---

*Sponsored by Stephanie Fried, COO — The Bushnell Center for the Performing Arts*
