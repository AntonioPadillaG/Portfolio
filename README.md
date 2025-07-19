🔍 Objective
This SQL script calculates the cumulative recovery curve —defined as the proportion of collected payments over the adjusted purchase price— for factoring-based educational receivables. It segments campuses into purchase tiers (S1 to S4) and evaluates the performance by vintage (harvesting_period) and country.

This analysis supports:

Credit risk monitoring

Operational performance tracking

Segmentation-driven strategies for collections and pricing

⚙️ Methodology
Segmentation logic:
Campuses are classified based on their purchase_price_w_adjustments, using thresholds tailored to each country (Mexico, Colombia, Ecuador).

Data sources:

dtm_collection: master dataset for purchase and collection details.

dim_receipts, dim_invoices, dim_campuses: used to identify and sum paid membership transactions.

Key transformations:

Purchase and payment data are merged by campus and filtered by vintage.

Recovery % is calculated both per segment and overall, with rounding and zero-handling.

Final output:
Returns:

Total purchase price by segment

Total paid amount

Average recovery curve (per segment)

Global recovery percentage across all segments

🌎 Country-Specific Configuration
To run the script for other countries:

Change WHERE country = 'Mexico' to 'Colombia' or 'Ecuador'

Segment thresholds are already embedded by country in the CASE logic.

🧠 Skills demonstrated
Advanced SQL analytics using CTEs, CASE, and JOIN operations.

Segmentation and recovery modelling.

Financial metrics aggregation.

Readable and production-grade SQL structure with inline documentation.

✅ Use case examples
📉 Identify underperforming segments (e.g. S3 campuses with low recovery).

🔍 Benchmark recovery by vintage.

💼 Present data-driven insights to collections or credit risk teams.


📈 Recovery Curve Analysis by Campus Segment (Factoring Model)
Author: Antonio Padilla
Last updated: CURRENT_DATE

Overview
This SQL script calculates the daily recovery curve for a portfolio of educational institutions under a factoring model. It evaluates the ratio of collections to the expected purchase price (adjusted), segmented by vintage and campus size (S1–S4). The results support credit risk monitoring and performance tracking across different financing segments and geographies.

🔍 Objective
To produce a segmented recovery curve per day and per campus using:

Purchase price with adjustments (expected recovery)

Actual payments from memberships

Segment thresholds by purchase size

Filters by vintage (202409) and product type (factoring)

📊 Use Cases
Monitoring early performance of factoring vintages

Comparing recovery performance across segments (S1–S4)

Adapting to different countries (Mexico, Colombia, Ecuador)

Supporting dashboards or alert systems for underperforming cohorts

🧠 Logic Summary
Segmentation
Assigns each campus to a segment (S1 to S4) based on the total purchase price, with adjustable thresholds for each country.

Aggregation
Filters for a specific vintage (202409) and aggregates:

Expected recovery (purchase price)

Total actual recovery (collection)

Payment Mapping
Matches invoice payments (membership concept) from dim_receipts and dim_invoices with each campus.

Final Join
Combines expected and actual values to compute:

Curva_Acumulada_AlDia (cumulative recovery to date)

Curva_Acumulada_Diaria (daily updated recovery performance)

🧩 Key Fields
Field	Description
segment	Campus grouping by purchase price (S1–S4)
VPCosecha	Expected recovery (Purchase price adjusted)
PagadoDia	Total collected amount to date
Curva_Acumulada_AlDia	Cumulative recovery as of current date
Curva_Acumulada_Diaria	Ratio of payments to expected value (daily)
campus_product_type	Should be factoring

🌍 Country Adaptability
The segmentation logic is easily adaptable:

sql
Copiar
Editar
-- Mexico
WHEN purchase_price_w_adjustments >= 2,000,000 THEN 'S1'
-- Colombia
WHEN purchase_price_w_adjustments >= 424,000,000 THEN 'S1'
-- Ecuador
WHEN purchase_price_w_adjustments >= 101,364 THEN 'S1'
Simply uncomment the relevant CASE block to regionalise the logic.

🛠️ Prerequisites
This script assumes access to the following tables:

mattiwarehouse.collection.dtm_collection

mattiwarehouse.default.dim_receipts

mattiwarehouse.default.dim_invoices

mattiwarehouse.default.dim_campuses

Ensure date fields (CURRENT_DATE, paid_date, cutoff_date) are properly aligned with your ETL.

✅ Recommended Enhancements
Parameterise the vintage (202409) for flexibility

Save results to a reporting table

Add visualisation layer (e.g. Power BI, QuickSight)

Track historical curves over time (not just CURRENT_DATE)


