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

#### Queries
1. Which stores sell the most liquor?
   
``
SELECT

     city,  
  
     ROUND(SUM(sale_dollars)) AS total_revenue  
  
FROM `bigquery-public-data.iowa_liquor_sales.sales`  

      WHERE sale_dollars > 0  

GROUP BY city  

ORDER BY total_revenue DESC  

LIMIT 10  

``
