# eCommerce Customer Experience Dashboard — Power BI

A Power BI analysis of 100K+ real orders from a Brazilian marketplace, examining where customer experience degrades across the order journey and what drives it.

---

## The question

The project is built around one question: where in the customer journey does experience degrade, and what causes it? Each page answers part of that, and every visual is there because it contributes to the answer.

---

## The data

Brazilian E-Commerce Public Dataset by Olist (Kaggle) — roughly 100,000 real orders from 2016 to 2018, covering customers, orders, items, payments, reviews, products and sellers.

I chose it over cleaner tutorial datasets because it reflects real operational conditions: late deliveries, cancellations, low-star reviews, and orders that never complete. Each order carries a full timeline (purchase, approval, shipping, delivery, review), which is what makes the journey analysis possible.

---

## Findings

**Late deliveries are the main driver of low review scores.**
On-time orders average a 4.2 review score; orders more than 8 days late average 1.7. I treated this as a hypothesis and tested it against the data — the relationship is clean and monotonic. On-time delivery is the strongest predictor of satisfaction in the dataset.

**The delay originates in shipping, not internal processing.**
Broken into stages, roughly 9.3 of the ~12 total delivery days are spent in carrier transit, against 0.5 days for order approval. Operational improvement should target logistics, not internal workflow.

**Poor performance is concentrated in specific segments.**
A small number of categories (home_comfort_2, furniture_mattress, and a few others) show both the worst delivery times and the worst reviews simultaneously — a clear set of targets rather than a system-wide problem.

**The baseline is strong.**
Olist delivers 12 days faster than promised on average. The issue is consistency, not speed: most orders perform well, and the late tail accounts for the dissatisfaction.

---

## Report structure

The report follows a deliberate order: overall business health, then the journey, then the problem areas, then recommendations.

**Page 1 — Sales Overview.** Headline KPIs (revenue, orders, customers, average order value), revenue trend, revenue by category, payment mix, and an initial view of the delivery-to-reviews relationship.

**Page 2 — Customer Journey.** The journey as a funnel (purchased, approved, shipped, delivered, reviewed) with drop-off at each stage, order-status breakdown, time spent per stage, and completion rate by region.

**Page 3 — Customer Satisfaction.** Categories and sellers ranked by low-review and late-delivery rates, with a conditional-formatted ranking table to surface the worst performers.

**Page 4 — Key Findings.** Six findings, each stated as an insight and its business implication.

---

## Technical approach

The model is a star schema with orders as the fact table, surrounded by customers, products, sellers, payments and reviews, plus a category-translation table and a dedicated Calendar table for time intelligence.

Key modeling decisions:

- Unique customers are counted on customer_unique_id rather than customer_id, which in this dataset is unique per order and would otherwise overstate the customer base.
- Revenue is derived from order_items[price] so it slices correctly by product and category.
- Cross-filter direction on the orders / order_items relationship is set so category and product filters reach the order- and review-level measures.

Data-quality note: early in the build, revenue came out roughly 100x too high. The price column was being parsed under a locale that read the decimal point as a thousands separator (58.90 loaded as 5890). It was caught by comparing the total against the expected order of magnitude and fixed by setting the correct locale at import.

The model includes 25 DAX measures covering revenue, orders, delivery performance, review sentiment, journey stages, and stage-by-stage duration.

---

## Files

- Olist.Project.pbix — the full report (Power BI Desktop)

## Built with

Power BI Desktop · DAX · Power Query · star-schema modeling
