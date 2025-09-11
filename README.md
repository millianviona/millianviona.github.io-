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
##This query shows the top 10 cities with the biggest sales.
```

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
##This query shows the top 10 stores that sells or earns the most revenue. 
```

<img width="1404" height="513" alt="Screenshot 2025-09-09 114301" src="https://github.com/user-attachments/assets/99f813d4-acf2-4096-bd6b-74903f6e0174" />

**3. What are the top product categories by average revenue**

```
SELECT 
  category_name,
  ROUND(AVG(bottles_sold)) AS avg_bottles_sold,
  ROUND(AVG(sale_dollars)) AS avg_revenue
FROM `bigquery-public-data.iowa_liquor_sales.sales`
WHERE 
  sale_dollars > 0 AND
  bottles_sold > 0
GROUP BY category_name
ORDER BY avg_revenue DESC
LIMIT 10
##This query shows the top 10 product categories that has the most average bottles sold and average revenue.
##There are 50 product categories in total. I only include 10 to make it more compact.
```

<img width="1403" height="519" alt="Screenshot 2025-09-09 121045" src="https://github.com/user-attachments/assets/ced2de13-b780-49ad-bba7-679e8d5f9b47" />

**4. What are the total revenue, profit, and units sold??**

```
SELECT 
  ROUND(SUM(bottles_sold)) AS total_bottles_sold,
  ROUND(SUM(sale_dollars)) AS total_revenue,
  ROUND(SUM((state_bottle_retail-state_bottle_cost) * bottles_sold)) AS total_profit
FROM `bigquery-public-data.iowa_liquor_sales.sales`
WHERE 
  sale_dollars > 0 AND
  bottles_sold > 0 AND
  state_bottle_retail > 0 AND
  state_bottle_cost > 0
```

<img width="1402" height="189" alt="Screenshot 2025-09-09 125932" src="https://github.com/user-attachments/assets/913b3d3f-3836-4231-ac50-5d81f62dac1c" />

**5. What bottle sizes that are the most preferred?**

```
SELECT 
  bottle_volume_ml AS bottle_size_ml,
  SUM(bottles_sold) AS total_bottles_sold
FROM `bigquery-public-data.iowa_liquor_sales.sales`
WHERE 
  bottles_sold > 0
GROUP BY
  bottle_size_ml
ORDER BY
  total_bottles_sold DESC
LIMIT 10
```

<img width="1399" height="489" alt="Screenshot 2025-09-09 131922" src="https://github.com/user-attachments/assets/5b64f6d7-ca11-4dfa-939f-1ebd071265a0" />

**6. How are the revenues and the growth rate per year?**

```
WITH yearly_sales AS (
  SELECT 
    EXTRACT(YEAR FROM date) AS year,
    SUM(sale_dollars) AS total_revenue
  FROM 
    `bigquery-public-data.iowa_liquor_sales.sales`
  WHERE sale_dollars > 0
  GROUP BY year
)

SELECT 
  year,
  ROUND(total_revenue, 2) AS total_revenue,
  CONCAT(
    CAST(ROUND(
      (total_revenue - LAG(total_revenue) OVER (ORDER BY year)) 
      / LAG(total_revenue) OVER (ORDER BY year) * 100, 
      2
    ) AS STRING),
    '%'
  ) AS yoy_growth_percent
FROM yearly_sales
ORDER BY year
```

<img width="559" height="479" alt="Screenshot 2025-09-09 133638" src="https://github.com/user-attachments/assets/facb41d5-f9a2-466d-9f66-8087ef9613d1" />


### Project 2: the NYC Weather Analysis
---

The dataset used in this project was provided as part of the Google Data Analytics Professional Certificate on Coursera. The dataset **NYC Weather** has already been cleaned and prepared by the course provider. My work focuses on further analysis, visualization, and insights.

The NYC Weather data contains daily weather observations including date, station ID, temperature (fahrenheit), wind speed, and precipitation.

**1. How many days are there in the data?**

```
SELECT
  COUNT(DISTINCT(date)) AS num_days
FROM `semiotic-method-468605-k9.demos.nyc_weather`
##semiotic-method-468605-k9 is the project ID where the dataset lives, demos is the dataset name, and nyc_weather is the table name.
```

The query then returns a table with a column named num_days and returns the value '366', stating that the number of days recorded in the data are 366 days from January 1st, 2020 to December 31st, 2020.

**2. What was the lowest and highest temperature ever recorded and when was it?**

```
WITH weather_celcius AS (
  SELECT
    date,
    ROUND(((temperature - 32) *5/9), 2) AS temp_celcius
  FROM `semiotic-method-468605-k9.demos.nyc_weather`
  )
-- Min Temp
SELECT
  date,
  temp_celcius
FROM weather_celcius 
ORDER BY temp_celcius ASC
LIMIT 1;

-- Max Temp
SELECT
  date,
  temp_celcius
FROM weather_celcius 
ORDER BY temp_celcius DESC
LIMIT 1
```
or use the query with "qualify" clause in Bigquery.

```
WITH weather_celsius AS (
  SELECT
    date,
    ROUND(((temperature - 32) * 5/9), 2) AS temp_celsius
  FROM `semiotic-method-468605-k9.demos.nyc_weather`
)
SELECT date, temp_celsius
FROM weather_celsius
QUALIFY temp_celsius = MIN(temp_celsius) OVER()
   OR temp_celsius = MAX(temp_celsius) OVER()
```

Both queries return the minimum and the maximum temperature ever recorded in 2020. Since the temperature data is recorded in Fahrenheit, I converted it to Celcius to make it easier to comprehend. In the first query, the min and max function are queried separately, so it will only return one row or observation with the date. In the second query, it returns both lowest and highest temperatures with the dates. The lowest temperature ever recorded in 2020 is -6 Celcius on Feb 15th, 2020 and the highest is 32.22 Celcius on July 27th, 2020.
