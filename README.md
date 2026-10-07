# Inventory-Analysis | Power-BI Dashboard
Getting insights into the best items for renewing or increasing inventory in a company.

## Overview
You act as a freelance business data analyst hired by WarmeHands Inc., a retail company that sells hand warmers and seasonal outdoor gear. The company has recently faced operational inefficiencies, inventory bottlenecks, and the critical risk of losing key product providers due to poor stock management and unpredictable purchasing patterns.
This project presents key insights and discusses potential solutions for renewing or increasing inventory while also having an idea of the influence of the categories and countries.

Key questions explored encompass metrics such as turnover rate, order breakdowns, and temporal variations

The project focuses on solving three main issues:

• Overstocking: Capital tied up in slow-moving items that take up valuable warehouse space.

• Stockouts: Missing out on revenue because high-demand items are out of stock.

• Supplier Risk: Identifying which vendors are unreliable or critical to business survival.

The analysis is based on sales data from the year 2020 and 2021, extracted from an online database.

Power BI has been employed to execute this analysis, leveraging DAX (Data Analysis Expressions) to create calculated columns and measures for comprehensive data exploration.

## Table of Contents
- [Overview](#overview)
- [Datasets](#datasets)
- [Methodology](#methodology)
- [Executive Summary](#executivesummary)
- [Recommendations](#recommendations)
- [Limitations](#limitations)

## Datasets
The data was sourced from the online database of DataCamps’ website.
The dataset comprises four different Excel files:

## Methodology
The methodology employed for this project comprised the following steps: defining the requirements, data collection, data cleaning and transformation, data modeling, visualization design, testing and validation, and ultimately publishing and sharing insights.

The first phase established a robust foundation by transforming raw operational data into a clean, relational data model to support complex analytical calculations. Power Query was utilized to inspect data distributions, handle missing values, and validate data types across transactional and master datasets. An optimized Star Schema was designed to ensure high performance and intuitive filter context behavior. One-to-many (1:*) relationships was established between the central fact table and peripheral dimension tables.
- The Costs table is connected to the Stock table through a many-to-one relationship.

- The Orders table is connected to the Stock table through a many-to-one relationship.

- The Categories table has a one-to-one relationship with the Stock table.

- The Stock table is linked to the ABC table through a one-to-one relationship.

- The ABC table is connected to the Turnover table through a one-to-one relationship.

###  Operational Efficiency (Inventory Turnover Analysis)
DAX measures were written to dynamically compute the components of the standard efficiency formula. First, I calculate the Total Cost of Goods Sold (COGS) by summing up the cost value of all items sold from the sales.
```
Total COGS = 
SUMX(
    Fact_Sales, 
    Fact_Sales[Quantity] * Fact_Sales[UnitCost]
)
```

To measure how effectively capital is deployed in physical stock, the Inventory Turnover Ratio (ITR) was calculated for the fiscal year 2021. This metric gauges how many times a company's average inventory is sold and replaced over a period.
```
Avg_inventory = ( Turnover[2021_Start_stock] + Turnover[2021Endstock] )* Turnover[COGS] / 2
```
Divide COGS by the Average Inventory. We use DIVIDE to safely handle any potential division-by-zero errors.

```
Inventory_turnover = Turnover[COGS]*Turnover[Quantity_2021]/Turnover[Avg_inventory]
```
### ABC Analysis Classification
To help management prioritize warehouse space, capital allocation, and tracking efforts, a cumulative revenue-driven ABC Classification model was implemented.
Categorized products into three strict classifications based on mathematical thresholds:

• Class A (Top 70%–80% of revenue): Critical items representing the business's core financial drivers; requires aggressive monitoring and optimal safety stock.
  
• Class B (Next 15%–20% of revenue): Moderate value items requiring standard control.
  
• Class C (Bottom 5% of revenue): Bulk items contributing low financial value; candidates for reduced reorder frequencies.
  
```
CP_revenue = CALCULATE(
    SUM(ABC[percent_revenue_2021]),
    FILTER(ABC,
    ABC[percent_revenue_2021]>=EARLIER(ABC[percent_revenue_2021])))
```

```
ABC_class = IF(ABC[CP_revenue]<=70,"A [High Value]", IF(ABC[CP_revenue]<=90,"B [Medium Value]", "C [Low Value]" ))
```

Once all the calculations had been completed, I built an interactive dashboard to highlight key business metrics and insights.
When creating the report, I ensured it was designed with simplicity in mind, while providing at the same time the most effective metrics and KPIs.
Consistency was taken into account, making sure the fonts, the sizes, and all the other graphic elements were harmonized throughout the report.

## Executive Summary
Below are the major insights that emerged from the analysis:
- The dashboard visually highlights that a small minority of products (Class A) generate roughly 80% of total revenue. These high-value items are the lifeblood of the company and require constant monitoring, while low-value items (Class C) can be managed with minimal effort.
- The chart tracking the Inventory Turnover Ratio (ITR) exposes specific product categories where stock sits unsold for too long (low ITR). This represents frozen cash that could be used elsewhere.
- Conversely, core fast-moving items show dangerously high turnover ratios. When combined with slow delivery times, these items are at immediate risk of running out of stock during peak season.
- The most important visual on the dashboard is the intersection of product value and vendor risk. It flags instances where Class A (high-revenue) products are reliant on High-Risk Suppliers (vendors with long delays or high defect rates). A single delay from these specific suppliers could instantly halt the company’s primary revenue streams.

## Recommendations
Based on the analysis, the following recommendations are proposed:
- Implement Tiered Inventory Controls. Transition warehouse management to match the ABC framework. Allocate optimal safety stock thresholds and run weekly cycles counts for Class A items, while shifting Class C items to loose, low-touch boundary controls.
- Execute Supplier Diversification. Immediately source alternative, secondary vendors for any Class A product currently dependent on a high-risk supplier to insulate against provider loss.
- Establish a S&OP Dashboard. Embed the newly developed Power BI data model into weekly Sales and Operations Planning (S&OP) meetings to proactively manage the balance between Cost of Goods Sold (COGS) and Stock-on-Hand.

## Limitations
While the analysis provides valuable insights, there are a few limitations to consider that might impact the effectiveness of the recommendations.
- Macroeconomic and Climate Volatility. Seasonal retail is inherently dependent on unpredictable weather patterns. A historically mild winter will completely invalidate demand forecasting, instantly turning highly optimized "Class A" safety stock into dead, overstocked inventory regardless of how accurate the DAX models are.
- Capacity Constraints in Supplier Diversification. The recommendation to source alternative suppliers assumes that secondary vendors are readily available, have open manufacturing capacity, and can meet WarmeHands’ quality standards. In reality, niche outdoor gear manufacturing often suffers from high industry consolidation, leaving few alternative options.
- Data Silos and Pipeline Latency. The Power BI dashboard is only as effective as its data refresh frequency. If the underlying ERP or warehouse management system only updates batched data weekly or monthly, the procurement team will still be making decisions based on stale information, missing critical real-time stockout windows.

