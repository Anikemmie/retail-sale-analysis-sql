# Retail Sales Analysis (SQL Project)

## Project Overview

**Project Title:** Retail Sales Analysis  
**Level:** Portfolio-Focused SQL Case Study  
**SQL Engine:** PostgreSQL

This project presents an **end-to-end retail analytics case study using SQL**, designed to demonstrate how raw transactional data can be transformed into **business-relevant insights**.

The primary goal of this project is to showcase practical SQL skills that are directly applicable in real-world analyst roles, with a strong emphasis on **revenue performance, profitability, customer value, and time-based trends**—the metrics most relevant to hiring managers, recruiters, and business stakeholders.

The dataset covers retail transactions from **2022 to 2023**, containing **1,987 cleaned records** after data quality checks.

---

## Project Objectives

* Build a structured retail sales database using PostgreSQL  
* Perform data quality checks and clean incomplete records  
* Explore customer, product, and transaction-level data  
* Answer high-impact business questions using SQL  
* Demonstrate analytical thinking aligned with real business decision-making

---

## Tools & Technologies

* **PostgreSQL**  
* SQL (CTEs, aggregations, window functions, subqueries)  
* Relational database design

---

## Dataset Overview

* **Source:** Public GitHub dataset (adapted from an open-source SQL retail sales project)  
* **Time Range:** 2022 – 2023  
* **Total Records:** 2,000 (raw) → **1,987 after cleaning**

The dataset contains transactional retail sales data with the following fields:

* `transactions_id`: Unique transaction identifier  
* `sale_date`: Date of transaction  
* `sale_time`: Time of transaction  
* `customer_id`: Unique customer identifier  
* `gender`: Customer gender  
* `age`: Customer age  
* `category`: Product category  
* `quantity`: Number of units purchased  
* `price_per_unit`: Price per item  
* `cogs`: Cost of goods sold  
* `total_sale`: Total transaction value

---

## Project Structure

Retail-Sales-Analysis-SQL/
│
├── README.md
├── schema.sql -- Database & table creation
├── data_cleaning.sql -- Data exploration & cleaning queries
├── analysis.sql -- Business-focused analysis queries


---


---

## Data Cleaning & Preparation

Data quality validation was performed before analysis to ensure accuracy and reliability:

* Verified record counts and unique customer coverage:

```sql
SELECT COUNT(*) 
FROM retail_sales;
Identified and removed rows with missing critical values (date, customer, product, or pricing fields):
  
SELECT * 
FROM retail_sales
WHERE transactions_id IS NULL OR sale_date IS NULL OR
      sale_time IS NULL OR customer_id IS NULL OR  
      gender IS NULL OR age IS NULL OR
      category IS NULL OR quantity IS NULL OR 
      price_per_unit IS NULL OR cogs IS NULL OR total_sale IS NULL;					

DELETE FROM retail_sales
WHERE transactions_id IS NULL OR sale_date IS NULL OR
      sale_time IS NULL OR customer_id IS NULL OR  
      gender IS NULL OR age IS NULL OR
      category IS NULL OR quantity IS NULL OR 
      price_per_unit IS NULL OR cogs IS NULL OR total_sale IS NULL;

After cleaning, 1,987 valid transaction records remained and were used for analysis.

Key Business Questions & SQL Analysis

The analysis focuses on business-driven questions commonly addressed by data analysts in retail and commercial environments.

1. Revenue and Order Volume by Product Category

Question: Which product categories contribute the most to overall revenue and order volume?

SELECT category,
       SUM(total_sale) AS Total_sales,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY 1;

This analysis identifies top-performing categories and supports inventory planning and merchandising decisions.

2. Profitability by Product Category

Question: Which categories generate the highest profit after accounting for cost of goods sold?
SELECT category, 
       ROUND(SUM(total_sale - cogs)::numeric) AS profit,
       SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY 1;

This moves beyond revenue analysis to evaluate true business performance.

3. Best-Selling Month per Year

Question: Which month recorded the highest average sales in each year?

SELECT year, month, avg_sale
FROM (
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER(
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) t1
WHERE rank = 1;

A window function (RANK()) was used to identify seasonal peak periods across years.

4. Monthly Sales Trend Analysis

Question: How do total sales evolve over time?

SELECT 
    EXTRACT(YEAR FROM sale_date) AS year,
    EXTRACT(MONTH FROM sale_date) AS month,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY year, month
ORDER BY year, month;


This time-series analysis highlights trends, seasonality, and potential growth or decline patterns.

5. Top 5 Customers by Lifetime Spend

Question: Who are the highest-value customers based on total historical spend?

SELECT customer_id, 
       SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;


This supports customer retention and high-value customer targeting strategies.

6. Best-Performing Category per Year

Question: Which product category leads sales performance in each year?

SELECT year, category, total_sales
FROM (
    SELECT  
        category,
        EXTRACT(YEAR FROM sale_date) AS year,
        SUM(total_sale) AS total_sales,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY SUM(total_sale) DESC
        ) AS rnk
    FROM retail_sales
    GROUP BY category, year
) t
WHERE rnk = 1;


Ranking logic was used to enable year-over-year category comparison.

7. Sales Distribution by Gender

Question: How is revenue distributed across customer genders?

SELECT gender, 
       SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY gender;


This provides high-level demographic insight useful for marketing and product positioning.

8. Peak Sales Hour Analysis

Question: At what time of day does transaction volume peak?

SELECT EXTRACT(HOUR FROM sale_time) AS hour,
       COUNT(*) AS total_orders
FROM retail_sales
GROUP BY hour
ORDER BY total_orders DESC;


Understanding peak sales hours supports staffing optimization and promotional timing.

9. Customer Lifetime Value (Simplified)

Question: What is the total revenue contribution of each customer over time?

SELECT customer_id,
       SUM(total_sale) AS lifetime_value
FROM retail_sales
GROUP BY customer_id
ORDER BY lifetime_value DESC;


A simplified CLV metric was calculated to assess long-term customer value.

Key Insights (Summary)

Top Customers: The top 10 customers account for a significant share of total revenue, highlighting a core group driving sales.

Product Performance: Electronics, Clothing, and Beauty are the most profitable and highest-revenue categories, with strong margins.

Seasonality: Sales peak mid-year (August–September) and dip in early months, showing predictable seasonal demand.

Peak Hours: Transaction volume is highest in the evening (17:00–22:00), suggesting optimal staffing and promotional timing.

Customer Behavior: Revenue comes from both high-value and smaller transactions, indicating a mix of frequent and occasional buyers.

Conclusion

This project demonstrates how SQL can be used to deliver actionable retail business insights from transactional data. By focusing on revenue, profitability, customer value, and temporal patterns, the analysis reflects real-world SQL-driven analytical workflows expected in professional data roles.

As a next step, this dataset will be extended into an interactive dashboard using Power BI or Tableau, allowing stakeholders to explore trends and insights visually.

This repository is intended as a portfolio-ready SQL case study and a foundation for more advanced analytics and visualization work.

As a next step, this dataset will be extended into an **interactive dashboard using Power BI or Tableau**, allowing stakeholders to explore trends and insights visually.
