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

# Recovery Curve Analysis: Early Warning Metric

## Overview

This SQL script identifies the **first day of zero growth** in collections after the start of a harvesting period, segmented by `campus`, `grade level`, and `harvesting_period`. This metric can be used as an **early warning indicator** for stagnation in collection performance, potentially highlighting campuses or periods at risk of underperformance.

## Purpose

📌 **Goal:** Detect when the collection curve flattens out, helping to trigger alerts for intervention or closer monitoring.

🎯 **Use Cases:**
- Credit risk early warning systems
- Collection team performance tracking
- Financial health monitoring of educational institutions
- Operational dashboards and time-series KPIs

## Methodology

1. **Base Table:** Uses `mattiwarehouse.collection.dtm_collection`, filtered for active post-harvest days and harvesting periods from August 2024 onward.
2. **Daily Growth Logic:** 
   - Calculates variation in `paid_collection_rights_amount` vs. prior day.
   - Flags large variations (≥ 25%) to exclude days with abnormal volatility.
   - Identifies the **first day** where the difference between adjusted total collections and paid collections is zero and not affected by volatility.
3. **Output:** 
   - Average of the first day of zero growth (`avg_first_day_zero_growth`) per campus and grade level.
   - Segmented output suitable for aggregation by product type or geography.

## Output Columns

| Column                     | Description                                                 |
|----------------------------|-------------------------------------------------------------|
| `campus_id`               | Unique ID for the campus                                    |
| `campus_name`             | Name of the campus                                          |
| `country`                 | Country of operation                                        |
| `grade_level`             | Educational level (e.g. primary, secondary)                 |
| `harvesting_period`      | Period of collection grouping (vintage)                     |
| `avg_first_day_zero_growth` | Avg. day after harvest when collection growth stopped       |

## Notes

- Designed with PostgreSQL window functions and CTEs.
- Can be integrated into business intelligence tools or embedded in monitoring systems.
- Modular structure allows easy adaptation for different country-specific thresholds or business rules.

# Recovery Curve Analysis: First Day of Zero Growth

## Overview

This SQL script calculates the **First Day of Zero Growth (FDZG)** in collection performance across educational campuses. It is designed as an **early warning signal** to detect when a campus' collections stagnate after the harvesting period begins. This metric is useful for credit risk analysts, collection teams, and portfolio managers.

## Objective

Identify the **first day after harvest** (`days_after_harvest`) when a campus shows **no increase in adjusted collections**, while also **excluding noise** from high-variation days (e.g., >25% jump). The resulting average FDZG per `campus_id`, `grade_level`, and `harvesting_period` gives a comparable metric across cohorts and institutions.

## Methodology

The process involves the following steps:

1. **Source and Filter Data**  
   Extract collection metrics from `dtm_collection` where `days_after_harvest <> 0` and `harvesting_period >= 202408`.

2. **Calculate Growth Flags**
   - Compute the day-over-day growth in `paid_collection_rights_amount`.
   - Flag abnormal jumps (absolute variation ≥ 25%) to reduce noise.
   - Calculate the difference between `total_collection_w_adjustments` and `paid_collection_rights_amount`.

3. **Identify First Day of Zero Growth**
   - Filter days where `difference = 0` and no large variation is flagged.
   - Select the **first occurrence** for each `campus_id` and `harvesting_period`.

4. **Aggregate Results**
   - Compute the **average FDZG** by `campus`, `grade_level`, and `harvesting_period`.

## Output

| Column Name               | Description                                           |
|--------------------------|-------------------------------------------------------|
| campus_id                | Unique identifier for the campus                      |
| campus_name              | Name of the campus                                    |
| country                  | Country (e.g. Mexico)                                 |
| grade_level              | Educational grade level                               |
| harvesting_period        | Cohort or period identifier                           |
| avg_first_day_zero_growth| Average day post-harvest when collection stagnated    |

## Use Cases

- **Portfolio Monitoring**: Detect early signs of underperforming campuses.
- **Risk Segmentation**: Compare cohort performance by grade or segment.
- **Operational Efficiency**: Flag campuses requiring early collection actions.








