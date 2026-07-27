# Dutchie Product Creation Tool

A Windows desktop app for Sonoran Roots that automates bulk product creation in the Dutchie backoffice — from CSV, from a manual form, from an internal template, or straight out of a vendor invoice PDF.

**[⬇ Download the latest installer](../../releases/latest)**

This repo hosts the installers and auto-update metadata only. The source code lives in a separate private repo.

---

## Install

1. Download the `.exe` installer from the [latest release](../../releases/latest).
2. Run it. You can choose the install location; a desktop shortcut and Start Menu entry are created.
3. No Node.js or other prerequisites. Automation uses **Microsoft Edge**, which is already on Windows 11 — nothing extra to download.

The app checks for updates on launch and downloads them in the background. When one is ready, go to **Settings → About** and click **Restart & Install**. You can also check manually with **Check for Updates**.

---

## What it does

| Feature | What it's for |
|---|---|
| **Bulk product creation** | Queue products, then let the tool fill and save each Dutchie form for you |
| **Invoice Parser** | Drop a vendor invoice PDF and get queue-ready products, named to the naming guide |
| **Create Strain** | Bulk-create Dutchie strains, which have no deep-link of their own |
| **Internal products** | Store an in-house product *format* once, then generate a SKU per strain |
| **Image library** | A persistent folder of product photos with fast thumbnails and a searchable picker |
| **Run history** | Every product run logged, searchable and filterable by status |
| **Light & dark themes** | Toggle in the sidebar |

---

## Creating products (Dashboard)

### 1. Launch the browser session

Click **Launch Browser**. An Edge window opens to the Dutchie login page. Log in yourself, then click **I'm logged in — Continue** in the app. The status indicator turns green.

If the session drops at any point, click **Re-launch** and log in again.

### 2. Add product images (optional)

Drop image files onto the **Product Images** card, or click **Open Folder** and copy files in directly. The folder is a **permanent library** — images stay put between sessions.

The folder is flat, so uploading a file with an existing name **replaces** it for every product already using it. The app warns you when that's about to happen.

### 3. Add products to the queue

**Import CSV** — select a file matching `product_template.csv`. Every row is validated against the current lookups and bad values are flagged inline, per field.

**Add Product** — fill in the form by hand. Large fields (Category, Strain, Vendor, Brand…) are type-ahead searchable, and the image field opens a thumbnail gallery.

You can edit or delete anything in the queue before running.

### 4. Run

Tick the products you want and click **Run Selected**. Each row moves Pending → Running → Success / Failed, and you can expand any row to see the step-by-step log. Failed rows stay in the queue with the error attached — fix the data and run them again.

---

## While a run is going

**The run always pauses when a product fails.** The Dutchie tab is left open so you can look at the form, and you choose:

- **Skip** — close that tab and carry on with the rest
- **Stop run** — end the run and leave the page open for inspection

**Manual save** (a checkbox before you run) fills every form but does **not** click Save, so you can eyeball each one and save it in the browser yourself. The run waits for you, then you click **Continue** to move on — or **Stop run** to finish early.

---

## Invoice Parser

1. Go to **Invoice** and drop a vendor invoice PDF onto the page.
2. **Confirm the brand.** The app detects it from the line items, the letterhead, or the filename. If it guessed wrong, or couldn't guess, pick the right one. Some vendors bill under a different name than the catalog uses; those are translated automatically and shown as `Invoiced → Catalog`.
3. **Confirm the vendor.** A detected vendor is shown at the invoice level and applied to every product. If several vendors look equally likely, quick-pick chips appear.
4. **Review the products.** Each becomes a card with its proposed name, category, strain, and price. Cards the app is confident about are pre-approved; anything uncertain is held for review.
   - Where a strain maps to more than one dominance variant, you must pick one before the card can be approved.
   - Same for an ambiguous category.
5. **Add the approved products to the queue**, then run them from the Dashboard as usual.

Re-uploading an invoice you've already processed raises a **duplicate invoice** warning, which you can override with **Process Anyway**. Products already in the Dutchie catalog are detected and skipped rather than duplicated.

---

## CSV format

Use `product_template.csv` as your starting point — headers must match exactly. All 32 columns:

| Column | Required | Notes |
|---|---|---|
| `name` | **Yes** | Product name |
| `sku` | No | Blank or `AUTO` to auto-generate |
| `category` | **Yes** | Must match a known category |
| `regulatory_category` | No | Free text |
| `type` | **Yes** | e.g. `Flower`, `Concentrate`, `Edible`, `Weight` |
| `default_unit` | No | e.g. `Gram`, `Each` |
| `cannabis_product` | No | `Yes` or `No` |
| `thc_content` | No | Numeric |
| `thc_unit` | No | `%`, `mg/g`, or `mg` |
| `cbd_content` | No | Numeric |
| `cbd_unit` | No | `%`, `mg/g`, or `mg` |
| `grams_concentration` | No | Numeric |
| `net_weight` | No | Numeric |
| `net_weight_unit` | No | `g`, `mg`, `oz`, or `lb` |
| `flower_equivalent` | No | Numeric |
| `cost` | No | Numeric |
| `recreational_price` | **Yes** | Numeric |
| `medical_price` | **Yes** | Numeric |
| `tax_category` | No | Must match a known tax category |
| `flavor` | No | Free text |
| `dosage` | No | e.g. `5mg`, `10mg` |
| `strain` | No | Must match a known strain, including its dominance tag — e.g. `Caddy Rainbow (H)` |
| `vendor` | No | Must match a known vendor |
| `brand` | No | Must match a known brand |
| `size` | No | Must match a known size |
| `alternate_name` | No | Free text, 100 characters max |
| `max_quantity_per_transaction` | No | Numeric |
| `ingredients` | No | Free text |
| `online_title` | No | Free text |
| `online_description` | No | Free text |
| `image_filename` | No | Must exactly match a file in the image library |
| `available_online` | No | `Yes` (default) or `No`. `No` disables the product's online availability — used for sample products |

---

## Settings

| Section | What's in it |
|---|---|
| **Companies & Module** | Company profiles, each with its own Dutchie URL. Optional pre-run check that the browser is pointed at the expected Dutchie module, so a run can't fire against the wrong location |
| **Lookup Data** | Row counts and last-refresh time per lookup, a refresh, and **Open Folder** for the data CSVs |
| **API Keys** | Anthropic API key, used by the Invoice Parser |
| **Snowflake** | Connection and table mappings for the naming guide, catalog dedup, and the strain / vendor / category / brand syncs, with per-table test buttons |
| **Invoice Parser** | Customer names to exclude from brand detection, and brand aliases (`Invoiced => Catalog`, one per line) |
| **Admin Password** | Optional password gate over the API Keys and Snowflake sections |
| **About** | App version and update controls |

### Where lookup values come from

Categories, strains, vendors, and brands are pulled live from Snowflake and cached locally — so the lists track what's actually active in Dutchie, and retired strains correctly fail validation. Sizes and tax categories come from CSV files in the `data/` folder next to the app.

If Snowflake isn't configured, everything falls back to those CSVs. Settings shows a row count and refresh time per lookup, so a silent fallback isn't mistaken for live data. To edit a CSV-backed list: **Open Folder**, edit, save, then click **Reload** — no restart needed.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Browser won't launch | Make sure Microsoft Edge is installed and up to date |
| "Browser not open" during a run | Click **Re-launch** and log in again |
| Run stops immediately with a module mismatch | The browser is on a different Dutchie location than the active company expects — switch location, or fix **Expected Module** in Settings |
| A dropdown value isn't found mid-run | The value is missing from the lookups. Refresh in Settings → Lookup Data, or add it to the relevant CSV and click **Reload** |
| Image not applied | The filename must match a file in the image library exactly, including extension |
| Lookup lists look stale or empty | Settings → Lookup Data shows the source and last refresh; use the per-table test buttons under Snowflake to check the connection |
| Invoice product has no strain in its title | The naming guide has no strain-agnostic entry for that product line — the strain still lands in the Strain field, and you can edit the title before adding it |
| Can't type in a form field | Alt-tab away from the app and back |
| Port 3000 in use | Close whatever else is using port 3000 and restart the app |

---

## Reporting a problem

Open an [issue](../../issues) with the app version from **Settings → About**, what you were doing, and — for invoice problems — the invoice PDF if you can share it.
