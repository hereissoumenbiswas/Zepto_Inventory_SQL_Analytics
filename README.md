# 🛒 Zepto Sales & Inventory Analysis — SQL Project

A SQL-based analysis of a quick-commerce (Zepto-style) product catalog, covering data cleaning, inventory KPIs, and category/pricing analysis using **PostgreSQL**.

## 📌 Project Overview

This project analyzes a 3,732 quick-commerce catalog products to answer real operational and business questions — inventory value, out-of-stock risk, discount patterns, category performance, and value-for-money pricing.

The goal was to get practice with a full SQL analytics workflow — exploring raw data, cleaning it, defining core KPIs, then going deeper into category- and product-level analysis — while being honest about what the data can and can’t reliably tell you.

## 🗂️ Dataset

| Column | Description |
|---|---|
| sku_id | Unique product ID (Primary Key) |
| category | Product category (14 total) |
| name | Product name |
| mrp | Maximum Retail Price |
| discountpercent | Discount % applied |
| availablequantity | Units currently in stock |
| discountedsellingprice | Actual selling price after discount |
| weightingms | Product weight in grams |
| outofstock | Boolean stock status |
| quantity | Pack size / unit count |

*Raw data file is in the [`/data`](./data) folder.*

## 🛠️ Tools Used

- **PostgreSQL** — database & query engine
- **pgAdmin 4** — schema creation, CSV import, and query execution
- **SQL** — data cleaning, KPI calculation, and business-question analysis

## ✅ What I Did

1. **Created the schema** — a single `zepto` table holding product, pricing, stock, and weight data.
2. **Imported the CSV** using pgAdmin's GUI-based Import/Export tool.
3. **Explored the data** — checked row count, distinct categories, and stock-status split before touching anything.
4. **Cleaned the data**:
   - Checked all columns for NULLs
   - Checked for fully duplicate rows
   - Checked for duplicate product names appearing as separate SKUs
   - Found and removed 1 row with `mrp = 0` / `discountedsellingprice = 0`
   - Converted `mrp` and `discountedsellingprice` from paise to rupees (divided by 100)
5. **Defined 5 core KPIs** — inventory value, OOS rate, revenue-at-risk, average discount, total stock units.
6. **Answered 8 deeper business questions** — top discounts, high-value OOS items, category revenue, premium/under-discounted items, best value-for-money products, and weight-based segmentation.
7. **Cross-checked results against each other** instead of treating each query in isolation — this is what surfaced the duplicate-category finding below.

## 🧩 Challenges Faced & How I Solved Them

### 1. Prices stored in paise, not rupees
**Challenge:** Initial price values looked unrealistically high (e.g. `4500` for a product that should cost ₹45).

**Solution:** Identified this was paise, not rupees, and applied a one-time conversion (`price / 100.0`) to both `mrp` and `discountedsellingprice` before any analysis — done *after* cleaning, so the fix wouldn't get lost in later steps.

### 2. A product with price = 0
**Challenge:** One row had `mrp = 0` — clearly invalid, since no real product is priced at zero.

**Solution:** Investigated the row first (`SELECT * WHERE mrp = 0`) rather than deleting blindly, confirmed it was a single, clearly broken record (not a valid free-item case), and removed it (`sku_id = 3607`).

### 3. Confusing column aliasing
**Challenge:** An early query used `select distinct category name, ...` — valid SQL, but the missing comma made it read like a typo, and `DISTINCT` was redundant once `GROUP BY category` was already collapsing rows.

**Solution:** Rewrote it as `select category as category_name, ...` and removed the unnecessary `DISTINCT` — same result, clearer to read.

### 4. Two unrelated categories showing identical numbers
**Challenge:** While cross-checking revenue (Q3) against inventory weight (Q8), I noticed **"Munchies" and "Cooking Essentials"** — two completely unrelated categories — had identical product counts (514), identical total revenue (₹337,369), and identical total weight (1,404.65 kg). This looked like a possible query bug at first.

**Solution:** Re-ran both queries independently against the raw data to rule out a JOIN or GROUP BY error. The numbers held up — meaning this is a genuine characteristic of the dataset (likely duplicated/mirrored category data during generation), not a mistake in the SQL. Documented it as a data-quality finding rather than silently ignoring it.

## 💡 What I Found (Key Insights)

| Query | Finding |
|---|---|
| KPI Q1 | Total inventory value: **₹22,43,092.00** |
| KPI Q2 | **12.14%** of SKUs are out of stock (453 of 3,732) |
| KPI Q3 | Revenue-at-risk from OOS items: ₹39,018.50 *(proxy metric — see caveat above)* |
| KPI Q4 | Average discount across the catalog: **7.62%** — fairly conservative |
| KPI Q5 | **14,959 total units** available across the catalog |
| Analysis Q1 | Highest discounts (50–51%) cluster around snacks — Dukes Waffy Wafers, Chef's Basket Pasta |
| Analysis Q2 | Highest-value OOS items: **Patanjali Cow's Ghee (₹565)**, MamyPoko Diapers (₹399) — real revenue sitting idle |
| Analysis Q3 | Top revenue categories: **Munchies & Cooking Essentials (₹337,369 each)**, then Personal Care (₹270,849) |
| Analysis Q4 | **82 products** are priced above ₹500 but discounted less than 10% — under-promoted premium items |
| Analysis Q5 | **Fruits & Vegetables has the highest average discount (15.46%)** — more than double most other categories, likely due to perishability |
| Analysis Q6 | Best value-for-money: **Onion & Tata Salt, both at ₹0.02/gram** |
| Analysis Q7 | **86% of products (3,221 of 3,732)** fall in the "low weight" category (≤500g) |
| Analysis Q8 | Highest inventory weight: **Munchies & Cooking Essentials, tied at 1,404.65 kg each** |

### ⚠️ Most important finding
**"Munchies" and "Cooking Essentials" are statistically identical** across product count, revenue, and weight — strongly suggesting duplicated category data rather than a coincidence. It was only revealed by comparing multiple queries against each other, not from any individual query on its own — a reminder that verifying results across questions is as important as writing the queries themselves.


## 📁 Repository Structure

```
Zepto-Sales-Analysis/
├── README.md
├── data/
│   └── Zepto_dataset.csv
├── docs/
│   └── (Zepto_key_insights)
├── screenshots/
│   └── (pgAdmin query + result screenshots)
└── script/
    └── Zepto_analysis.sql
```

## 🔗 Connect

**Author:** Soumen Biswas
*([LinkedIn](https://www.linkedin.com/in/hereissoumenbiswas/))*

*A practice project to strengthen SQL skills — data cleaning, KPI design, and business-question analysis — as part of my data analytics learning journey.*
