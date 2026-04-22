# hw-inventory-sync
Compile or audit Shopify &amp; Inventory exports
# Harper Wilde — Inventory Sync App

A browser-based tool for merging inventory data into Shopify exports and auditing existing Shopify product data. No installation required — just open `index.html` in any browser.

---

## What it does

The app has two modes:

**Compile** — Takes a Shopify product export (with blank SKUs) and fills in SKU, UPC, weight, and inventory quantity by matching products to your inventory file(s). Use this when setting up new products in Shopify.

**Audit** — Takes a Shopify product export (with existing SKUs) and compares it against your inventory file(s) to flag mismatches in UPC, color, size, weight, and quantity. Use this for ongoing data quality checks.

---

## How to use

### 1. Select a mode
Choose **Compile** or **Audit** depending on what you're doing.

### 2. Upload files
- **Shopify export** — Export your products from Shopify admin as a CSV. Rows can have blank or existing SKUs depending on the mode.
- **Inventory file(s)** — Drop one or more inventory XLSX or CSV files. Multiple files are merged automatically.

Both CSV and XLSX formats are supported for all uploads.

### 3. Review name mappings (optional)
If a product name differs between Shopify and your inventory system (e.g. "Sunday Pima Cami" vs "Pima Sleep Cami"), add a mapping so the app can match them correctly. Products in the mapping list will not trigger name mismatch warnings.

### 4. Run
Click **Run Compile** or **Run Audit**. Results appear in a color-coded table.

### 5. Download report
Export results as a color-coded XLSX file for sharing or record-keeping.

---

## Result categories

### Compile mode
| Result | Meaning |
|---|---|
| **Updated** | SKU, UPC, weight, and qty were successfully populated from inventory |
| **Warning** | Data was populated but something needs attention (e.g. color option mismatch in Shopify) |
| **Missing in Inv** | Shopify product could not be matched to any inventory row |
| **Missing in Shpfy** | Inventory item has no corresponding Shopify product |

### Audit mode
| Result | Meaning |
|---|---|
| **Clean** | All fields match between Shopify and inventory |
| **Issues** | One or more fields have a mismatch (UPC, color, size, weight, or qty) |
| **Missing in Inv** | SKU exists in Shopify but not in inventory |
| **Missing in Shpfy** | SKU exists in inventory but not in Shopify |

---

## Matching logic

The app matches Shopify products to inventory rows using a combination of:
- **Product name** — Extracted from the Shopify title (e.g. "Sunday Pima Palazzo Pant" from "Sunday Pima Palazzo Pant - Mirage"), with support for name mappings
- **Color** — Tries the title color suffix first (e.g. "Mirage"), then falls back to the Shopify Option1 value
- **Size** — Combines Option2 and Option3 for bra sizing (e.g. "34" + "D" → "34D"); translates between inventory size codes (LRG, MED, SML) and Shopify sizes (L, M, S)

In **Audit** mode, matching is done by SKU.

---

## Name mappings

Products that have different names between Shopify and your inventory system need a mapping entry to match correctly. Pre-loaded mappings:

| Shopify Title Name | Inventory Style Name |
|---|---|
| Bliss Low Rise Thong | Bliss Banded Thong |
| Bliss Low Rise Bikini | Bliss Banded Bikini |
| Sunday Pima Cami | Pima Sleep Cami |
| Perfect Pima Pajama Pants | Perfect Pima Pant (Piping) |
| Perfect Pima Pajama Tee | Perfect Pima Tee (Piping) |
| Perfect Pima Pajama Shorts | Perfect Pima Short (Piping) |
| Perfect Pima Pajama Shirt | Perfect Pima Short Sleeve Button Up (Piping) |
| Perfect Pima Long Sleeve Pajama Shirt | Perfect Pima Long Sleeve Button Up (Piping) |

To add new mappings permanently, update the `nameMappings` array near the top of `index.html`. You can also add them temporarily via the Name Mappings section in the app UI.

---

## Common issues

**"Missing in Inv" with a title format warning** — The Shopify product title has a formatting issue (e.g. `"Shirt -Blue"` instead of `"Shirt - Blue"`). Fix the title in Shopify admin so the color can be correctly extracted.

**Color option mismatch warning** — The Shopify Option1 color value doesn't match the inventory color. This is a Shopify setup issue — update the product's color option in Shopify admin.

**Duplicate files on drag and drop** — Make sure to drop all files at once in a single drag. Re-dropping will replace the previous upload, not add to it.

---

## File format reference

### Shopify export columns used
`Title`, `Option1 Value` (color), `Option2 Value` (size/band), `Option3 Value` (cup for bras), `Variant SKU`, `Variant Barcode`, `Variant Weight`, `Variant Inventory Qty`

### Inventory file columns used
`CustomerSKU`, `UPC`, `Style`, `Color`, `Size`, `WeightInGrams`, `TotalAvailable`
