# Ironman Customer Analytics: RFM & Return Rate Segmentation

## Project Overview
This project transforms raw transactional data into actionable business intelligence using a hybrid Python-SQL approach. By engineering custom metrics and applying K-Means clustering with **Log-Transformation**, we identified distinct customer segments that help optimize marketing spend and operational focus.

# Feature Engineering: The RFM-R Metrics
To capture the true value and cost of each customer, a custom feature set was built:

Metric,Meaning,Business Value
Recency,Days since the last purchase.,Identifies active vs. churned customers.
Frequency,Total count of unique orders.,Measures customer loyalty and habit.
Monetary,Total net revenue (Price * Qty).,Measures the total economic value.
Return Rate,% of items returned vs. purchased.,"Identifies high-cost ""Headache"" customers."

# Machine Learning & The "Log-Transformation" Breakthrough 
Initially, extreme outliers (e.g., customers with >20,000% return rates) skewed the clustering, forcing 95% of customers into a single group.

The Solution: a Log Transformation was applied to all metrics. This "squashed" the distance between outliers and typical customers, allowing the K-Means algorithm to find these balanced segments:

- New/Occasional: Recently acquired or low-frequency shoppers.
- Steady Customers: The reliable backbone of the business.
- VIP Whales: High-frequency, high-value brand advocates.
- Churned/Inactive: Customers who haven't purchased in a significant period.
- High Returners: Extreme outliers that require operational flagging.

# SQL Integration & Reporting 
The ML-derived segments were pushed to a MySQL database to bridge the gap between Data Science and Business Operations.
Human-Readable Segment View: a CASE statement was implemented to map technical cluster IDs to business names based on the identified population counts.
Business Intelligence Report: This report uses the ML segments to analyze real-world revenue and average order values.

# Key Conclusions

- Normalization is Essential: Without log-normalization, extreme returners "blinded" the model. Transformation was the key to identifying the VIP segment and distinguishing them from average shoppers.
- Profitability vs. Volume: "High Returners" may show significant revenue but often result in net losses due to high logistics and processing costs.
- Hybrid Workflow: By pushing ML labels to SQL, non-technical stakeholders can now run their own "Whale" or "At-Risk" reports without needing Python.

## Solutions for the Dataset Issues

To ensure the integrity of the segmentation and the reliability of the business reports, the following technical solutions were implemented:

* **Handling Guest Checkouts**: 
    Transactions associated with `CustomerID = 0` (Guest accounts) were filtered out before clustering. This prevents "noise" in the model, as guest IDs aggregate unrelated users into a single entity, which would distort the behavioral patterns of registered customers.

* **Net Revenue Calculation**: 
    The `Monetary` value was calculated by summing the product of `Quantity` and `UnitPrice`. This approach naturally accounts for returns (negative quantities), ensuring the final metric reflects the actual "Net" profitability of the customer rather than just gross sales.

* **Outlier Management via Log-Transformation**: 
    The dataset contained extreme outliers, such as customers with return rates exceeding 20,000% or massive bulk purchases. These outliers "blinded" the K-Means algorithm. By applying a **Log Transformation** (`np.log1p`), we compressed the variance, allowing the model to recognize distinct segments within the majority of the population while still accounting for the presence of extreme values.
    

* **RFM-R Feature Engineering**: 
    Beyond standard RFM (Recency, Frequency, Monetary), we engineered a custom **Return Rate** feature. This allows the business to distinguish between "High-Value" customers who keep their items and "High-Value" customers who are operationally expensive due to constant returns.

* **Database Synchronization**: 
    To make these insights accessible, the processed Python segments were pushed to a MySQL database using `SQLAlchemy`. This ensures that the advanced analytical "Brain" of the project can be used directly within standard SQL business reports and BI tools.


# Next steps
- Predictive Churn: Build a model to predict which "Steady Customers" are moving toward the "Inactive" segment based on temporal trends.

- Geographic Analysis: Identify which countries have the highest concentration of "High Returners" to investigate potential localized logistics issues.

- Automated Flagging: Integrate the SQL view with order fulfillment software to flag high-risk orders from the "High Returner" segment for manual review.
