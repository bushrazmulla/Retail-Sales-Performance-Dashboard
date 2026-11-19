# Retail Sales Analysis Using SQL

## Project Overview

**Project Name:** Retail Sales Analysis  
**Project Level:** Beginner  
**Database:** `SQL_Project`  
**SQL Language:** MySQL  

### About the Project

The Retail Sales Analysis project focuses on exploring and analyzing retail transaction data using SQL to extract meaningful business insights.

The project demonstrates fundamental and intermediate SQL concepts commonly used in data analytics, including database creation, data cleaning, exploratory data analysis (EDA), aggregate functions, filtering, grouping, subqueries, Common Table Expressions (CTEs), and window functions.

By analyzing retail sales transactions, the project aims to understand customer purchasing behavior, evaluate product category performance, identify high-value transactions, and uncover monthly and time-based sales trends.

The objective is to transform raw sales data into meaningful insights that can support business decision-making.

---

## Project Objectives

The main objectives of this project are:

1. **Database Setup:** Create a relational database and design a structured table to store retail transaction data.
2. **Data Cleaning:** Identify missing or null values and remove incomplete records to maintain data quality.
3. **Exploratory Data Analysis (EDA):** Examine the dataset to understand its structure, size, customer distribution, and product categories.
4. **Sales Analysis:** Calculate total sales, average sales, and category-wise revenue.
5. **Customer Analysis:** Identify high-value customers and analyze customer purchasing patterns.
6. **Time-Based Analysis:** Examine monthly sales performance and identify peak sales periods.
7. **Business Insights:** Translate SQL query results into actionable insights that can help businesses make informed decisions.

---

## Dataset Description

The dataset contains retail transaction records with information about customers, products, transaction dates, quantities, and sales amounts.

### Database Schema

| Column Name | Data Type | Description |
|---|---|---|
| transactions_id | INT | Unique identifier for each transaction |
| sale_date | DATE | Date of the transaction |
| sale_time | TIME | Time of the transaction |
| customer_id | INT | Unique customer identifier |
| gender | VARCHAR(10) | Gender of the customer |
| age | INT | Age of the customer |
| category | VARCHAR(35) | Product category |
| quantity | INT | Number of units purchased |
| price_per_unit | FLOAT | Price per unit of the product |
| cogs | FLOAT | Cost of goods sold |
| total_sale | FLOAT | Total transaction amount |

---

## 1. Database Setup

The first step involves creating a database named `SQL_Project` and a table named `retail_sales` to store the retail transaction records.

### Creating the Database

```sql
CREATE DATABASE SQL_Project;

USE SQL_Project;
```

### Creating the Retail Sales Table

```sql
CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);
```

The table structure ensures that each transaction is uniquely identified using `transactions_id`, while the remaining columns capture relevant customer and sales information.

---

## 2. Data Exploration and Cleaning

Before performing business analysis, the dataset was explored to understand its structure and identify potential data quality issues.

### 2.1 Total Number of Records

Determine the total number of transactions available in the dataset.

```sql
SELECT COUNT(*) AS total_records
FROM retail_sales;
```

### 2.2 Unique Customers

Calculate the number of distinct customers present in the dataset.

```sql
SELECT COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales;
```

### 2.3 Unique Product Categories

Identify all distinct product categories available in the dataset.

```sql
SELECT DISTINCT category
FROM retail_sales;
```

### 2.4 Identifying Missing Values

Check for records containing NULL values in important columns.

```sql
SELECT *
FROM retail_sales
WHERE sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;
```

### 2.5 Removing Incomplete Records

Remove transactions containing missing values in the required columns.

```sql
DELETE FROM retail_sales
WHERE sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;
```

**Data Cleaning Note:** The deletion query is intended to remove incomplete records. In a production environment, it is preferable to preserve a raw copy of the dataset and validate the impact of deleting records before modifying the original data.

---

## 3. SQL Analysis and Business Questions

After completing the initial data exploration and cleaning, SQL queries were developed to answer business-related questions and uncover insights from the retail sales dataset.

### Q1. Retrieve All Sales Transactions Made on November 5, 2022

**Business Objective:** Examine all transactions recorded on a specific date.

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

**SQL Concepts:** SELECT, WHERE, Date Filtering.

### Q2. Retrieve Clothing Transactions Where Quantity Sold Is More Than 4 During November 2022

**Business Objective:** Identify clothing transactions involving larger purchase quantities during November 2022.

```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND quantity > 4
  AND sale_date >= '2022-11-01'
  AND sale_date < '2022-12-01';
```

**SQL Concepts:** WHERE, AND, Comparison Operators, Date Filtering.

### Q3. Calculate Total Sales for Each Product Category

**Business Objective:** Understand which product categories contribute the most to overall revenue.

```sql
SELECT
    category,
    SUM(total_sale) AS total_revenue
FROM retail_sales
GROUP BY category
ORDER BY total_revenue DESC;
```

**SQL Concepts:** SUM(), GROUP BY, ORDER BY.

### Q4. Calculate the Average Age of Customers Purchasing Beauty Products

**Business Objective:** Understand the average age profile of customers purchasing products from the Beauty category.

```sql
SELECT
    ROUND(AVG(age), 2) AS average_customer_age
FROM retail_sales
WHERE category = 'Beauty';
```

**SQL Concepts:** AVG(), ROUND(), WHERE.

### Q5. Identify the Number of High-Value Transactions

**Business Objective:** Determine how many transactions generated sales greater than 1,000.

```sql
SELECT
    COUNT(*) AS high_value_transactions
FROM retail_sales
WHERE total_sale > 1000;
```

**SQL Concepts:** COUNT(), WHERE, Comparison Operators.

### Q6. Calculate the Number of Transactions by Gender Within Each Product Category

**Business Objective:** Understand how purchasing activity is distributed across customer genders and product categories.

```sql
SELECT
    category,
    gender,
    COUNT(transactions_id) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category, gender;
```

**SQL Concepts:** COUNT(), GROUP BY, ORDER BY.

### Q7. Calculate the Average Monthly Sales and Identify the Best-Selling Month for Each Year

**Business Objective:** Analyze monthly sales performance and identify the month with the highest average transaction value in each year.

```sql
SELECT *
FROM
(
    SELECT
        YEAR(sale_date) AS sales_year,
        MONTH(sale_date) AS sales_month,
        ROUND(AVG(total_sale), 2) AS average_sales,

        RANK() OVER
        (
            PARTITION BY YEAR(sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS sales_rank

    FROM retail_sales
    GROUP BY
        YEAR(sale_date),
        MONTH(sale_date)
) AS monthly_analysis

WHERE sales_rank = 1
ORDER BY sales_year;
```

**SQL Concepts:** YEAR(), MONTH(), AVG(), ROUND(), GROUP BY, Subqueries, RANK(), Window Functions.

**Analysis:** The query calculates average transaction value for each month and ranks the months separately within each year. It returns the highest-ranking month for every year, including ties.

### Q8. Identify the Top 5 Customers Based on Total Spending

**Business Objective:** Identify the five customers who generated the highest overall sales revenue.

```sql
SELECT
    customer_id,
    SUM(total_sale) AS total_spending
FROM retail_sales
GROUP BY customer_id
ORDER BY total_spending DESC
LIMIT 5;
```

**SQL Concepts:** SUM(), GROUP BY, ORDER BY, LIMIT.

### Q9. Calculate the Number of Unique Customers in Each Product Category

**Business Objective:** Identify how many distinct customers purchased products from each category.

```sql
SELECT
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category
ORDER BY unique_customers DESC;
```

**SQL Concepts:** COUNT(DISTINCT), GROUP BY, ORDER BY.

### Q10. Categorize Sales Transactions into Different Shifts

**Business Objective:** Analyze customer purchasing activity based on transaction time and determine which shift receives the highest number of orders.

The transactions are categorized into three shifts:

- Morning: Before 12 PM.
- Afternoon: 12 PM to 5 PM.
- Evening: After 5 PM.

```sql
WITH hourly_sales AS
(
    SELECT
        transactions_id,
        CASE
            WHEN HOUR(sale_time) < 12
                THEN 'Morning'

            WHEN HOUR(sale_time) BETWEEN 12 AND 17
                THEN 'Afternoon'

            ELSE 'Evening'
        END AS shift

    FROM retail_sales
)

SELECT
    shift,
    COUNT(transactions_id) AS total_orders
FROM hourly_sales
GROUP BY shift
ORDER BY total_orders DESC;
```

**SQL Concepts:** CTE, WITH, CASE WHEN, HOUR(), COUNT(), GROUP BY, ORDER BY.

---

## 4. Key Findings and Business Insights

The SQL analysis provides a foundation for understanding retail sales performance and customer purchasing behavior.

The following are the major areas of analysis covered by the project:

### 1. Customer Demographics
- Examined customer distribution across different age groups.
- Analyzed the average age of customers purchasing Beauty products.
- Explored purchasing patterns across customer genders.

### 2. Product Category Performance
- Calculated total revenue generated by each product category.
- Identified the number of unique customers purchasing from each category.
- Compared transaction volumes across product categories and genders.

### 3. High-Value Transactions
- Identified transactions with a total sale amount greater than 1,000.
- Analyzed the highest-spending customers based on their cumulative transaction values.

### 4. Monthly Sales Trends
- Calculated average transaction values for each month.
- Identified the best-performing month within each year.
- Used window functions to rank monthly sales performance.

### 5. Time-Based Sales Analysis
- Classified transactions into Morning, Afternoon, and Evening shifts.
- Calculated the total number of orders in each shift.
- Provided a basis for understanding customer shopping patterns throughout the day.

---

## 5. Skills and SQL Concepts Demonstrated

This project demonstrates practical knowledge of the following SQL concepts:

| Category | Skills and Concepts |
|---|---|
| Database Management | CREATE DATABASE, CREATE TABLE, USE |
| Data Retrieval | SELECT, DISTINCT |
| Data Cleaning | IS NULL, DELETE |
| Filtering | WHERE, AND, Comparison Operators |
| Aggregation | COUNT(), SUM(), AVG(), ROUND() |
| Grouping | GROUP BY, HAVING |
| Sorting | ORDER BY |
| Date Functions | YEAR(), MONTH(), Date Filtering |
| Time Functions | HOUR() |
| Conditional Logic | CASE WHEN |
| Advanced SQL | Subqueries, CTEs |
| Window Functions | RANK(), PARTITION BY |
| Data Analysis | Customer Analysis, Revenue Analysis, Trend Analysis |

---

## 6. Tools and Technologies

- **MySQL:** Database creation, data manipulation, and analytical queries.
- **SQL:** Data extraction, cleaning, transformation, and business analysis.
- **GitHub:** Project documentation, version control, and portfolio presentation.

---

## 7. Project Reports and Deliverables

The project covers the following analytical outputs:

- **Sales Summary:** Category-wise revenue and high-value transaction analysis.
- **Customer Analysis:** Customer demographics, spending patterns, and unique customer counts.
- **Monthly Trend Analysis:** Monthly average sales and identification of peak-performing months.
- **Time-Based Analysis:** Transaction distribution across different shopping shifts.
- **Business Insights:** SQL-based findings that help understand customer behavior and sales performance.

---

## 8. Conclusion

The Retail Sales Analysis project provided practical experience in using SQL to explore, clean, and analyze structured retail transaction data.

Through this project, I applied SQL concepts ranging from basic data retrieval and filtering to more advanced techniques such as Common Table Expressions, subqueries, and window functions.

The analysis helped examine different aspects of retail business performance, including customer demographics, product category revenue, high-value transactions, monthly sales trends, and time-based purchasing behavior.

Most importantly, this project strengthened my ability to approach business problems analytically, translate business requirements into SQL queries, and extract meaningful information from raw datasets.

It demonstrates how SQL can be used as an effective tool for data analysis and data-driven decision-making.

---

## 9. Future Improvements

The project can be extended further by:

- Performing deeper customer segmentation based on purchasing behavior.
- Calculating profit and profit margins using sales and cost data.
- Identifying repeat customers and customer retention patterns.
- Analyzing revenue contribution by gender and age group.
- Integrating the SQL database with Power BI to build an interactive retail sales dashboard.
- Exploring sales trends and seasonal purchasing patterns over longer periods.

---

**Author:** Bushra Z M  
**Project:** Retail Sales Analysis Using SQL  
**Domain:** Data Analytics  
**Database:** MySQL
