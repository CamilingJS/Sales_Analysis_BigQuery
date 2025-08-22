# Sales Analysis using SQL in Big Query

#### 1: Earliest year of purchase
```
SELECT Year
FROM prework.sales
ORDER BY Year ASC
LIMIT 1;
```
```
SELECT MIN(Year) as earliest_year_
FROM prework.sales; 
```
![image](https://github.com/user-attachments/assets/8f600094-826a-4f61-b3c2-7bbaa21410d8)

#### 2: Average customer age per year
```
SELECT year,
  ROUND(Avg(Customer_Age), 1) as customer_age_
FROM prework.sales
group by year
order by year;
```

```
SELECT year, 
  Avg(Customer_Age) as customer_age_
FROM prework.sales
group by 1
order by 1;
```
![image](https://github.com/user-attachments/assets/1bbbd5af-d5fc-4583-9844-cef36fee399e)


#### 3: Clothing from September 2015, cost at least 70
```
SELECT * 
FROM prework.sales
WHERE YEAR = 2015
  and month = "September"
  and cost >= 70
ORDER BY date;
```
```
SELECT Product, Cost, Month, Year
FROM prework.sales
WHERE YEAR = 2015
  and month = "September"
  and cost >= 70
ORDER BY date
LIMIT 16;
```
![image](https://github.com/user-attachments/assets/c911a7e5-a96f-4da4-86b7-c944b6cb5cfc)


#### 4: Product categories from 2014 to 2016 in France
```
SELECT DISTINCT Product_Category
FROM prework.sales
WHERE year BETWEEN 2014 AND 2016
  AND country = 'France';
```
![image](https://github.com/user-attachments/assets/e6efd300-b9b8-483a-8404-c7b313eefe90)


#### 5: Within each product category and age group (combined), what is the average order quantity and total profit?
```
SELECT DISTINCT Product_Category, 
  Age_Group,
  Round(Avg(Order_Quantity), 2) as avg_order_qty_,
  Round(Sum(Profit), 2) as total_profit_
FROM prework.sales
group by 1,2;
```
![image](https://github.com/user-attachments/assets/527c11d9-015b-4cdb-be8b-abcdd630421c)


#### 6: Which product category has the highest number of orders among 31-year olds? Return only the top product category.
```
SELECT DISTINCT Product_Category, 
  SUM(Order_Quantity) as total_qty
FROM prework.sales
WHERE Customer_Age = 31
group by 1
ORDER BY 2 DESC 
LIMIT 1;
```
![image](https://github.com/user-attachments/assets/7c6dbdb2-22f2-4d4b-879c-ae8a43653e4a)

#### 7: Of female customers in the U.S. who purchased bike-related products in 2015, what was the average revenue?
```
WITH revs_ AS (
select Revenue from prework.sales
WHERE Customer_Gender = 'F'
AND Product_Category = 'Bikes'
AND Country = 'United States'
AND Year = 2015
)
select 'United States' AS Country, Avg(Revenue) as avg_us_female_bikes_rev
from revs_;

```
![image](https://github.com/user-attachments/assets/50e95353-4004-4f02-a18c-c6c6b79a1b04)

```
select avg(revenue) as avg_revenue
from prework.sales
where customer_gender = 'F' 
  and product_category like '%Bike%'
	and country = 'United States' 
  and year = 2015;
```
![image](https://github.com/user-attachments/assets/efd60550-4b16-4283-a487-b353e4ee9949)



#### 8: Categorize all purchases into bike vs. non-bike related purchases. How many purchases were there in each group among male customers in 2016?
```
SELECT 
  SUM(CASE WHEN Product_Category LIKE '%Bikes%' THEN 1 ELSE 0 END) AS isBike,
  SUM(CASE WHEN Product_Category NOT LIKE '%Bikes%' THEN 1 ELSE 0 END) AS isNonBike,
  COUNT(*) AS total
FROM `elegant-matrix-315405.prework.sales`
WHERE Customer_Gender = 'M';

```
<img width="332" alt="image" src="https://github.com/user-attachments/assets/6cb6bf71-6202-4862-9d86-1d625209a92b" />


#### 9: Among people who purchased socks or caps (use sub_category), what was the average profit earned per country per year, ordered by highest average profit to lowest average profit?
```
select Country, Year, round(avg(Profit), 2) as avg_profit_
from prework.sales
where Sub_Category like '%Sock%' or Sub_Category like '%Cap%'
group by Country, Year
order by avg_profit_ desc; 
```
![image](https://github.com/user-attachments/assets/56b10251-b6c6-4d01-94f9-17147a08ea34)

```
select country
year,
avg(profit) as avg_profit
from prework.sales
where sub_category in ('Caps', 'Socks')
group by 1,2
order by 3 desc;
```
<img width="260" alt="image" src="https://github.com/user-attachments/assets/cc991880-8e42-4160-89af-ce2956fe3e8c" />




#### 10: For male customers who purchased the AWC Logo Cap (use product), use a window function to order the purchase dates from oldest to most recent within each gender.
```
select *
row_number() over (partition by Customer_Gender order by date asc) as date_rank
from prework.sales
where Customer_Gender = 'M' and Product = 'AWC Logo Cap'; 
```
<img width="751" alt="image" src="https://github.com/user-attachments/assets/48b81764-a056-4f35-9ec6-30fc784ddb5c" />

#### 11: The team wants to identify the total usage duration of the services for each device type by extracting the primary device category from the device name for the period from July 1, 2024 to September 30, 2024. The primary device category is derived from the first word of the device name.
#### Tables
#### fct_device_usage(usage_id, device_id, service_id, usage_duration_minutes, usage_date)
#### dim_device(device_id, device_name)
#### dim_service(service_id, service_name)
```
SELECT
SUBSTRING(dd.device_name FROM 1 FOR POSITION(' ' IN dd.device_name) - 1),
SUM(fdu.usage_duration_minutes) AS total_usage_minutes
FROM dim_device dd
JOIN fct_device_usage fdu
ON dd.device_id = fdu.device_id 
WHERE fdu.usage_date BETWEEN '2024-07-01' AND '2024-09-30'
GROUP BY 1
```

#### The team is considering bundling the Prime Video and Amazon Music subscription. They want to understand what percentage of total usage time comes from Prime Video and Amazon Music services respectively. Please use data from July 1, 2024 to September 30, 2024.
#### Tables
#### fct_device_usage(usage_id, device_id, service_id, usage_duration_minutes, usage_date)
#### dim_device(device_id, device_name)
#### dim_service(service_id, service_name)
```
WITH totals_prime_and_amazon AS (
    SELECT
        SUM(fdu.usage_duration_minutes) AS total_prime_and_amazon_usage_min
    FROM fct_device_usage fdu 
    JOIN dim_service ds
    ON fdu.service_id = ds.service_id
    WHERE fdu.usage_date BETWEEN '2024-07-01' AND '2024-09-30'
    AND (ds.service_name = 'Prime Video' OR ds.service_name = 'Amazon Music')
),
totals AS (
    SELECT
        SUM(usage_duration_minutes) AS total_usage_min
    FROM fct_device_usage 
    WHERE usage_date BETWEEN '2024-07-01' AND '2024-09-30'
)

SELECT 
    (total_prime_and_amazon_usage_min::NUMERIC / total_usage_min::NUMERIC) * 100 AS percentage_usage
FROM totals_prime_and_amazon, totals;
```
####
```
SELECT
    s.service_name,
    SUM(u.usage_duration_minutes) AS total_usage_minutes,
    ROUND(
        100.0 * SUM(u.usage_duration_minutes) 
        / SUM(SUM(u.usage_duration_minutes)) OVER (),
        2
    ) AS pct_of_total_usage
FROM fct_device_usage u
JOIN dim_service s 
    ON u.service_id = s.service_id
WHERE u.usage_date BETWEEN '2024-07-01' AND '2024-09-30'
  AND s.service_name IN ('Prime Video', 'Amazon Music')
GROUP BY s.service_name
ORDER BY s.service_name;
```


#### To better understand customer preferences, the team needs to know the details of customers who reorder specific products. Can you retrieve the customer information along with their reordered product code(s) for Q4 2024?
#### Tables
#### fct_orders(order_id, customer_id, product_id, reorder_flag, order_date)
#### dim_products(product_id, product_code, category)
#### dim_customers(customer_id, customer_name)
```
SELECT
  dc.customer_id,
  dp.product_code
FROM
  dim_products dp
  INNER JOIN fct_orders fo ON dp.product_id = fo.product_id
  INNER JOIN dim_customers dc ON fo.customer_id = dc.customer_id
WHERE
  fo.order_date BETWEEN '2024-10-01' AND '2024-12-31'
  AND fo.reorder_flag = 1
```

#### When calculating the average reorder frequency, it's important to handle cases where reorder counts may be missing or zero. Can you compute the average reorder frequency across the product categories, ensuring that any missing or null values are appropriately managed for Q4 2024?
#### Tables
#### fct_orders(order_id, customer_id, product_id, reorder_flag, order_date)
#### dim_products(product_id, product_code, category)
#### dim_customers(customer_id, customer_name)
```
SELECT 
    dp.category,
    AVG(COALESCE(fo.reorder_flag, 0)) AS avg_reorder_frequency
FROM dim_products dp
LEFT JOIN fct_orders fo
    ON dp.product_id = fo.product_id
   AND fo.order_date BETWEEN '2024-10-01' AND '2024-12-31'
GROUP BY dp.category
ORDER BY avg_reorder_frequency DESC;
```

#### As a Product Analyst on the Mac software team, you are investigating exceptional daily usage patterns in September 2024. For each day, determine the distinct user count and the total hours spent using multimedia tools. Which days have both metrics above the respective average daily values for September 2024?
#### Tables
#### fct_multimedia_usage(user_id, usage_date, hours_spent)
```
WITH user_count_total_hour_per_day AS (
SELECT
usage_date,
COUNT(DISTINCT user_id) AS user_count,
sum(hours_spent) AS total_hours
FROM fct_multimedia_usage
WHERE usage_date BETWEEN '2024-09-01' AND '2024-09-30'
GROUP BY 1
),
averages_table AS (
  SELECT
  round(AVG(user_count), 2) AS avg_user_count,
  round(AVG(total_hours), 2) AS avg_total_hours
  FROM user_count_total_hour_per_day
)

SELECT ucthpd.usage_date
FROM user_count_total_hour_per_day ucthpd
CROSS JOIN averages_table avgtab
WHERE ucthpd.user_count > avgtab.avg_user_count 
AND ucthpd.total_hours > avgtab.avg_total_hours
```



