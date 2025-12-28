

# Superstore Sales Analysis - SQL Project

A profitability analysis of the **Sample Superstore** dataset using **MySQL**.
### Project Overview
- **Dataset**: Sample Superstore (Kaggle) 
- **Tool**: MySQL Workbench
- **Objective**: Identify drivers of profit and loss, analyze performance by category, sub-category, region, and discount levels.

### Key Insights
- **Technology** is the most profitable category with high profit margins (up to 37%).
- **Furniture** category overall loses money, especially **Tables** (-$17,725 loss) and **Bookcases** (-$3,472 loss).
- **Supplies** sub-category also generates consistent losses.
- Higher average discounts are strongly linked to negative profitability in loss-making sub-categories.
- **Copiers**, **Phones**, **Accessories**, and **Paper** are the top profit generators.

### Full SQL Queries

-- 1. Overall Summary
SELECT 
    COUNT(*) AS total_orders,
    ROUND(SUM(Sales), 2) AS total_sales,
    ROUND(SUM(Profit), 2) AS total_profit,
    ROUND(AVG(Discount), 3) AS avg_discount,
    ROUND(SUM(Profit)/SUM(Sales) * 100, 2) AS overall_profit_margin_pct
FROM samplesuperstore;

-- 2. Performance by Category & Sub-Category (Main Analysis)
SELECT 
    Category,
    `Sub-Category` AS Sub_Category,
    ROUND(SUM(Sales), 2) AS total_sales,
    ROUND(SUM(Profit), 2) AS total_profit,
    ROUND(SUM(Profit)/SUM(Sales) * 100, 2) AS profit_margin_pct,
    ROUND(AVG(Discount), 3) AS avg_discount,
    COUNT(*) AS order_count
FROM samplesuperstore
GROUP BY Category, `Sub-Category`
ORDER BY total_profit ASC;

-- 3. Loss-Making Sub-Categories Only
SELECT 
    `Sub-Category` AS Sub_Category,
    ROUND(SUM(Profit), 2) AS total_profit
FROM samplesuperstore
GROUP BY `Sub-Category`
HAVING total_profit < 0
ORDER BY total_profit ASC;

-- 4. Regional Performance
SELECT 
    Region,
    ROUND(SUM(Sales), 2) AS total_sales,
    ROUND(SUM(Profit), 2) AS total_profit,
    ROUND(SUM(Profit)/SUM(Sales) * 100, 2) AS profit_margin_pct
FROM samplesuperstore
GROUP BY Region
ORDER BY total_profit DESC;

-- 5. Performance by Customer Segment
SELECT 
    Segment,
    ROUND(SUM(Sales), 2) AS total_sales,
    ROUND(SUM(Profit), 2) AS total_profit,
    ROUND(SUM(Profit)/SUM(Sales) * 100, 2) AS profit_margin_pct
FROM samplesuperstore
GROUP BY Segment
ORDER BY total_profit DESC;

-- 6. Impact of Discount on Profit
SELECT 
    CASE 
        WHEN Discount = 0 THEN 'No Discount'
        WHEN Discount <= 0.1 THEN 'Low (0-10%)'
        WHEN Discount <= 0.2 THEN 'Medium (11-20%)'
        WHEN Discount <= 0.3 THEN 'High (21-30%)'
        ELSE 'Very High (30%+)'
    END AS discount_bucket,
    COUNT(*) AS orders,
    ROUND(AVG(Profit), 2) AS avg_profit_per_order,
    ROUND(SUM(Profit), 2) AS total_profit
FROM samplesuperstore
GROUP BY discount_bucket
ORDER BY total_profit DESC;
