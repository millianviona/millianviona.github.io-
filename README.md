# Google BigQuery Portfolio

Hello! Welcome to my Google BigQuery portfolio. Here you will find a collection of my projects in data analytics, including work with SQL, R, Tableau, Excel, and BigQuery.  
I’m passionate about turning data into insights and sharing my learning journey.

Visit me on [Kaggle](https://www.kaggle.com/vionamillian), [Tableau](https://public.tableau.com/app/profile/viona.millian/vizzes), and [Linkedin](https://www.linkedin.com/in/viona-millian/).

### Project 1: Iowa Liquor Sales Analysis
---

This project uses the **Iowa Liquor Sales** dataset from BigQuery Public Datasets.  
The analysis explores sales trends, store performance, and unusual data entries (such as negative bottles sold).

#### Dataset
- Source: [BigQuery Public Dataset](https://console.cloud.google.com/marketplace/details/iowa-department-of-commerce/iowa-liquor-sales)  
- Table: `bigquery-public-data.iowa_liquor_sales.sales`

#### Key Questions
- Which stores sell the most liquor?
- What are the most popular products?
- Are there any anomalies in the data (e.g., negative bottles sold)?

#### Assumption
I only account positive values. 

#### Queries
**1. Which cities sell the most liquor?**
   
```
SELECT 
  city,
  ROUND(SUM(sale_dollars)) AS total_revenue
FROM `bigquery-public-data.iowa_liquor_sales.sales`
WHERE sale_dollars > 0
GROUP BY city
ORDER BY total_revenue DESC
LIMIT 10
```
This query shows the top 10 cities with the biggest sales.

<img width="1406" height="478" alt="Screenshot 2025-09-09 113238" src="https://github.com/user-attachments/assets/be32292e-bae8-40b5-9c89-65d9c07420d0" />


**2. Which stores sell the most liquor?**

```
SELECT 
  store_name,
  city,
  ROUND(SUM(sale_dollars)) AS total_revenue
FROM `bigquery-public-data.iowa_liquor_sales.sales`
WHERE sale_dollars > 0
GROUP BY city, store_name
ORDER BY total_revenue DESC
LIMIT 10
```

This query shows the top 10 stores that sells or earns the most revenue. 

<img width="1404" height="513" alt="Screenshot 2025-09-09 114301" src="https://github.com/user-attachments/assets/99f813d4-acf2-4096-bd6b-74903f6e0174" />
