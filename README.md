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


### Project 2: The NYC Weather Analysis
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
WITH weather_celsius AS (
  SELECT
    date,
    ROUND(((temperature - 32) *5/9), 2) AS temp_celsius
  FROM `semiotic-method-468605-k9.demos.nyc_weather`
  )
-- Min Temp
SELECT
  date,
  temp_celsius
FROM weather_celsius 
ORDER BY temp_celsius ASC
LIMIT 1;

-- Max Temp
SELECT
  date,
  temp_celsius
FROM weather_celsius 
ORDER BY temp_celsius DESC
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

Both queries return the minimum and the maximum temperature ever recorded in 2020. Since the temperature data is recorded in Fahrenheit, I converted it to Celsius to make it easier to comprehend in a temporary table called weather_celcius that contains date and temp_celcius. In the first query, the min and max function are queried separately, so it will only return one row or observation with the date. In the second query, it returns both lowest and highest temperatures with the dates. The lowest temperature ever recorded in 2020 is -6 Celsius on Feb 15th, 2020 and the highest is 32.22 Celsius on July 27th, 2020.

**3. How many days each year experienced "extreme cold" and "extreme hot"?**

```
WITH weather_celsius AS (
  SELECT
    date,
    ROUND(((temperature - 32) * 5/9), 2) AS temp_celsius
  FROM `semiotic-method-468605-k9.demos.nyc_weather`
)
SELECT
  CASE 
    WHEN temp_celsius < 0 THEN "Extreme Cold"
    WHEN temp_celsius >= 32 THEN "Extreme Heat"
  END AS temp_category,
  COUNT(DISTINCT date) AS days_count
FROM weather_celsius
WHERE temp_celsius < 0
   OR temp_celsius >= 32
GROUP BY temp_category
##Firstly, categorize the temparatures below 0 as "extreme old" and temperatures above 32 as "extreme heat". Otherwise, the query will return each distinct temperature with number of days (meaning it will not be aggregated properly).
```
The query returns two categories, Extreme Cold and Extreme Heat. The query also displays the number of days in a year that experienced extreme cold and extreme heat. Over the span of 2020, there are 15 days of extreme cold and 2 days of extreme hot.

**4. What is the average wind speed in 2020?**

```
SELECT
  ROUND(AVG(wind_speed), 2) AS avg_wind_speed
FROM `semiotic-method-468605-k9.demos.nyc_weather`
```

The average wind speed in 2020 is  9.56 mph.

**5. How many days in a year that the wind speed exceeds 20 mph?**

```
SELECT
  CASE 
    WHEN wind_speed >= 20 THEN "High Wind Speed"
  END AS wind_speed_category,
  COUNT(DISTINCT date) as count_days
FROM `semiotic-method-468605-k9.demos.nyc_weather`
WHERE wind_speed >= 20
GROUP BY wind_speed_category
```

The query returns that the number of days of when the wind speed is high (more than or equal to 20 mph) are 7 days.

**6. Which month experienced "extreme weather" the most?**

```
WITH weather_celsius AS (
  SELECT
    DATE(date) AS date,
    EXTRACT(MONTH FROM date) AS month,
    ROUND(((temperature - 32) * 5/9), 2) AS temp_celsius,
    wind_speed
  FROM `semiotic-method-468605-k9.demos.nyc_weather`
)
SELECT
  month,
  COUNT(DISTINCT date) AS extreme_days
FROM weather_celsius
WHERE temp_celsius >= 32
   OR temp_celsius <= 0
  AND wind_speed >= 20
GROUP BY month
ORDER BY month
```

The query returns 1 day of extreme weather in month 1 (January) and there are 2 days of extreme weather in month 7 (July).

***7. In each month, what are the percentages of extreme weather in 2020?**

```
WITH weather_celsius AS (
  SELECT
    DATE(date) AS date,
    EXTRACT(MONTH FROM date) AS month,
    ROUND(((temperature - 32) * 5/9), 2) AS temp_celsius,
    wind_speed
  FROM `semiotic-method-468605-k9.demos.nyc_weather`
),
monthly_days AS (
  SELECT
    month,
    COUNT(DISTINCT date) AS total_days
  FROM weather_celsius
  GROUP BY month
),
extreme_days AS (
  SELECT
    month,
    COUNT(DISTINCT date) AS extreme_days
  FROM weather_celsius
  WHERE temp_celsius >= 32
     OR temp_celsius <= 0
     OR wind_speed >= 20
  GROUP BY month
)
SELECT
  monthly_days.month,
  extreme_days.extreme_days,
  monthly_days.total_days,
  CONCAT(ROUND((extreme_days.extreme_days / monthly_days.total_days) * 100, 2), '%') AS extreme_percentage
FROM monthly_days
LEFT JOIN extreme_days
  ON monthly_days.month = extreme_days.month
ORDER BY monthly_days.month;

```
<img width="443" height="276" alt="Screenshot 2025-09-11 113457" src="https://github.com/user-attachments/assets/528c83df-790a-4391-8ec4-952bcd86b06b" />

There are some blanks in the cells. That is because there are some null values in the data. From the query, we can see that month 1 (January) and 12 (December) have the highest percentage of being in extreme weather.
