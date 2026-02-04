README for Sales Analytics Project
Overview

This project demonstrates importing, cleaning, and analyzing sales data using PostgreSQL. It covers importing data from CSV, cleaning data, and generating intermediate to advanced-level sales reports.

- Table of Contents

- Data Import

- Data Cleaning

- Sales Reports

- Monthly Sales Report

- Sales by Country

- Sales by Product

- Advanced Rankings and Growth

- Performance Optimization

- Conclusion

> Data Import

Staging Table Approach

Before importing data into the final table, we create a staging table. This ensures that we can clean and transform the data without affecting the final table.

Creating the Staging Table
CREATE TABLE sales_staging (
    sales_person   TEXT,
    country         TEXT,
    product         TEXT,
    sale_date       TEXT,
    amount          TEXT,
    boxes_shipped   TEXT
);

> Importing Data

Use pgAdmin to import the CSV into a staging table, then clean and transfer data into the final sales table.

>Data Cleaning

We handle data cleaning by removing unwanted characters, converting date formats, and ensuring numeric fields are properly formatted.
Creating the Table

Then, we create the sales table with appropriate data types:

CREATE TABLE sales (
    sales_person   VARCHAR(100),
    country         VARCHAR(50),
    product         VARCHAR(100),
    sale_date       DATE,
    amount          NUMERIC(12,2),
    boxes_shipped   INTEGER
);


> Sales Reports
Monthly Sales Report
SELECT
    DATE_TRUNC('month', sale_date) AS month,
    COUNT(*) AS total_orders,
    SUM(amount) AS total_revenue,
    SUM(boxes_shipped) AS total_boxes
FROM sales
GROUP BY DATE_TRUNC('month', sale_date)
ORDER BY month;

Sales by Country
SELECT
    country,
    COUNT(*) AS orders,
    SUM(amount) AS total_revenue,
    AVG(amount) AS avg_order_value,
    SUM(boxes_shipped) AS boxes_shipped
FROM sales
GROUP BY country
ORDER BY total_revenue DESC;

Sales by Product
SELECT
    product,
    COUNT(*) AS orders,
    SUM(amount) AS revenue,
    SUM(boxes_shipped) AS total_boxes
FROM sales
GROUP BY product
ORDER BY revenue DESC;

Sales by Product (continued)
Top-Selling Product by Revenue
SELECT
    product,
    SUM(amount) AS revenue
FROM sales
GROUP BY product
ORDER BY revenue DESC
LIMIT 1;

> Product Performance Overview
SELECT
    product,
    COUNT(*) AS orders,
    SUM(amount) AS revenue,
    SUM(boxes_shipped) AS total_boxes
FROM sales
GROUP BY product
ORDER BY revenue DESC;

> Advanced Reports
Rank Products by Revenue
SELECT
    product,
    SUM(amount) AS revenue,
    RANK() OVER (ORDER BY SUM(amount) DESC) AS revenue_rank
FROM sales
GROUP BY product;

Top Product per Country
SELECT *
FROM (
    SELECT
        country,
        product,
        SUM(amount) AS revenue,
        RANK() OVER (
            PARTITION BY country
            ORDER BY SUM(amount) DESC
        ) AS rnk
    FROM sales
    GROUP BY country, product
) ranked
WHERE rnk = 1;

> Month-Over-Month Growth
SELECT
    month,
    revenue,
    revenue - LAG(revenue) OVER (ORDER BY month) AS growth,
    ROUND(
        (revenue - LAG(revenue) OVER (ORDER BY month)) * 100.0 / LAG(revenue) OVER (ORDER BY month),
        2
    ) AS growth_percent
FROM (
    SELECT
        DATE_TRUNC('month', sale_date) AS month,
        SUM(amount) AS revenue
    FROM sales
    GROUP BY month
) m;

> Sales Person Performance
SELECT
    sales_person,
    COUNT(*) AS deals_closed,
    SUM(amount) AS revenue,
    AVG(amount) AS avg_deal_size
FROM sales
GROUP BY sales_person
ORDER BY revenue DESC;

> Performance Optimization

To enhance query performance, we add indexes:

CREATE INDEX idx_sales_date ON sales(sale_date);
CREATE INDEX idx_sales_country ON sales(country);
CREATE INDEX idx_sales_product ON sales(product);

Conclusion

This project covers the entire workflow from importing and cleaning data to generating advanced analytics reports. It demonstrates best practices in data engineering and analytics using PostgreSQL.

📬 Connect

Feel free to connect with me on:

LinkedIn

Twitter
