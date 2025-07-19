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
