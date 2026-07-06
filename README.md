# eCommerce Customer Experience Dashboard — Power BI

Analyzing 100K+ real orders from a Brazilian marketplace to figure out where the customer experience actually breaks — and what you'd do about it.

---

## Why I built this

Most dashboards tell you what happened. Revenue went up, orders went down, here's a chart. Fine, but it doesn't help anyone decide anything. I wanted to build one that answers a harder question:

**Where in the customer journey does the experience fall apart, and what's causing it?**

So that's the thread the whole thing hangs on. Every page is there to answer a piece of it, and if a chart didn't earn its place against that question, it didn't make the cut.

---

## The data

I used the Brazilian E-Commerce Public Dataset from Olist — about 100,000 real orders between 2016 and 2018 from an actual marketplace, covering customers, orders, items, payments, reviews, products and sellers.

I picked it over the usual clean tutorial datasets on purpose. This one is messy in the way real data is messy: late deliveries, cancellations, angry one-star reviews, orders that never arrive. And every order comes with its full timeline — when it was bought, approved, shipped, delivered, reviewed. That timeline is what makes the journey analysis possible.

---

## What I found

**Late deliveries wreck satisfaction. That is the whole story in one line.**
When an order shows up on time, the average review is 4.2. When it is more than 8 days late, it drops to 1.7. I went in treating that as a guess and let the data settle it — and it is about as clean a relationship as you will ever see. On-time delivery is not a driver of satisfaction, it is the driver. A late order loses more than half its rating.

**And the slowdown is in shipping, not in the office.**
When I broke the journey into stages, the picture got obvious fast. Out of roughly 12 days from purchase to doorstep, 9.3 of them are just the package sitting in transit. Order approval? Half a day. So if you are trying to fix the experience, you are fixing logistics — the internal side is already fast.

**The bad stuff clusters.**
It is not spread evenly across the catalogue. A few categories — home_comfort_2, furniture_mattress, a couple of others — manage to have both the worst delivery times and the worst reviews at once. Which is actually good news if you are the one fixing it: you do not boil the ocean, you go after the handful of segments doing most of the damage.

**But honestly, the baseline is solid.**
Olist delivers 12 days faster than it promises, on average. So the problem was never speed. It is consistency. Most orders are fine — it is the tail that is brutal, and that tail is what customers remember.

---

## How it is put together

I built it to read like an argument, not a wall of charts — start with the health of the business, walk through the journey, zoom into where it hurts, then say what to do.

**Page 1 — Sales Overview.** The headline numbers (revenue, orders, customers, average order value), the revenue trend, what is actually driving revenue by category, how people pay, and the first hint of the delivery-vs-reviews story.

**Page 2 — Customer Journey.** The whole journey as a funnel — purchased through approved, shipped, delivered, reviewed — with the drop-off at each step, where orders end up, how long each leg takes, and whether some regions complete more cleanly than others.

**Page 3 — Customer Satisfaction.** Categories and sellers ranked by how often they get low reviews and late deliveries, so the specific troublemakers surface instead of hiding in the average. There is a ranking table with conditional formatting so the worst offenders jump out.

**Page 4 — Key Findings.** Six takeaways, each written as the insight plus what it means — the page you would actually read first if someone handed you this.

---

## The technical side

The model is a star schema — orders in the middle as the fact table, with customers, products, sellers, payments and reviews around it, plus a category-translation table and a proper Calendar table for anything time-based.

A few decisions that actually mattered:

- I count unique customers on customer_unique_id, not customer_id. In this dataset customer_id is unique per order, so if you count it you massively overstate how many customers you have. Easy trap, and a lot of Olist analyses fall into it.
- Revenue comes from order_items[price] so it slices properly by product and category.
- I set the cross-filter direction on the orders / order_items relationship deliberately, so category and product filters actually reach the order- and review-level measures instead of dead-ending.

And one war story, because it is the useful part: about halfway through, revenue came out roughly 100 times too big. Turned out the price column was being read under a locale that treated the decimal point as a thousands separator — so 58.90 was being loaded as 5890. I only caught it because the total looked absurd next to what I knew the number should roughly be. Forced the right locale on import and it snapped back into place. It is a tiny thing that silently ruins an entire analysis if nobody is paying attention, which is kind of the whole point.

There are 25 DAX measures in total, covering revenue, orders, delivery performance, review sentiment, the journey stages, and the time spent on each leg.

---

## Files

- Olist.Project.pbix — the full report, open it in Power BI Desktop
- /screenshots — the pages, if you just want to look

## Built with

Power BI Desktop · DAX · Power Query · star-schema modeling
