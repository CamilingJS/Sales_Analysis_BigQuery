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

#### What is the total number of photos shared by users who are either under 18 years old or over 50 years old and who are not from the United States during the third quarter of 2024? This measure will inform us if there are significant differences in usage across these age and geographic segments.
#### Tables
#### fct_photo_sharing(photo_id, user_id, shared_date)
#### dim_user(user_id, age, country)
```
SELECT
count(fps.photo_id)
FROM fct_photo_sharing fps
INNER JOIN dim_user du 
ON fps.user_id = du.user_id 
WHERE fps.shared_date BETWEEN '2024-07-01' AND '2024-09-30'
AND du.country != 'United States'
AND (du.age < 18 OR du.age > 50)
```

#### For the first quarter of 2024, which merchant categories recorded a transaction success rate below 90%? This insight will guide our prioritization of security enhancements to improve payment reliability.
#### Tables
#### fct_transactions(transaction_id, merchant_category, transaction_status, transaction_date)
```
SELECT 
    merchant_category,
    COUNT(*) AS total_txn,
    SUM(CASE WHEN transaction_status = 'Success' THEN 1 ELSE 0 END) AS successful_txn,
    ROUND(100.0 * SUM(CASE WHEN transaction_status = 'Success' THEN 1 ELSE 0 END) / COUNT(*), 2) AS success_rate
FROM fct_transactions
WHERE transaction_date >= '2024-01-01'
  AND transaction_date < '2024-04-01'
GROUP BY merchant_category
HAVING (SUM(CASE WHEN transaction_status = 'Success' THEN 1 ELSE 0 END) * 100.0 / COUNT(*)) < 90
ORDER BY success_rate ASC;
```

#### From January 1st to March 31st, 2024, can you generate a list of merchant categories with their concatenated counts for successful and failed transactions? Then, rank the categories by total transaction volume. This ranking will support our assessment of areas where mixed transaction outcomes may affect user experience.
#### Tables
#### fct_transactions(transaction_id, merchant_category, transaction_status, transaction_date)
```
WITH txn_summary AS (
  SELECT
    merchant_category,
    COUNT(*) FILTER (WHERE transaction_status = 'Success') AS success_count,
    COUNT(*) FILTER (WHERE transaction_status = 'Failed') AS failed_count,
    COUNT(*) AS total_count
  FROM fct_transactions
  WHERE transaction_date BETWEEN '2024-01-01' AND '2024-03-31'
  GROUP BY merchant_category
)
  
SELECT
  merchant_category,
  success_count || ' Success / ' || failed_count || ' Failed' AS transaction_summary,
  total_count,
  RANK() OVER (ORDER BY total_count DESC) AS category_rank
FROM txn_summary
ORDER BY category_rank; 
```

#### How many files were shared with names that start with the same prefix as the organization name, concatenated with a hyphen, in February 2024?
#### Tables
#### fct_file_sharing(file_id, file_name, organization_id, shared_date, co_editing_user_id)
#### dim_organization(organization_id, organization_name, segment)
```
SELECT
count(*) AS file_count
FROM fct_file_sharing ffs JOIN dim_organization dimo 
ON ffs.organization_id = dimo.organization_id
WHERE ffs.shared_date BETWEEN '2024-02-01' AND '2024-02-29'
AND ffs.file_name LIKE dimo.organization_name || '-%'
```

#### Identify the top 3 organizational segments with the highest number of files shared where the co-editing user is NULL, indicating a potential security risk, during the first quarter of 2024.
#### Tables
#### fct_file_sharing(file_id, file_name, organization_id, shared_date, co_editing_user_id)
#### dim_organization(organization_id, organization_name, segment)
```
SELECT
segment,
COUNT(file_id) AS file_count
FROM fct_file_sharing ffs JOIN dim_organization dimo 
ON ffs.organization_id = dimo.organization_id
WHERE ffs.shared_date BETWEEN '2024-01-01' AND '2024-03-31'
AND ffs.co_editing_user_id IS NULL
GROUP BY 1
ORDER BY 2 DESC 
LIMIT 3
```

#### Based on the top 50% of listings by earnings in July 2024, what percentage of these listings have ‘ocean view’ as an amenity? For this analysis, look at bookings that were made in July 2024.
#### Tables
#### fct_bookings(booking_id, listing_id, booking_date, nightly_price, cleaning_fee, booked_nights)
#### dim_listings(listing_id, amenities, location)
```
WITH earnings AS (
  SELECT
    b.listing_id,
    SUM( (b.nightly_price * b.booked_nights) + COALESCE(b.cleaning_fee, 0) ) AS total_earnings
  FROM fct_bookings b 
  WHERE b.booking_date BETWEEN '2024-07-01' AND '2024-07-31'
  GROUP BY b.listing_id
),
top_earnings AS (
  SELECT listing_id 
  FROM earnings 
  WHERE total_earnings > (SELECT percentile_cont(0.5) WITHIN GROUP (ORDER BY total_earnings) FROM earnings)
)
SELECT 
  (SUM(CASE WHEN l.amenities LIKE '%ocean view%' THEN 1 ELSE 0 END) * 100.0 / COUNT(*)) AS ocean_view_percentage
FROM dim_listings l
JOIN top_earnings te ON l.listing_id = te.listing_id; 
```

#### What is the average checkout wait time in minutes for each Walmart store during July 2024? Include the store name from the dim_stores table to identify location-specific impacts. This metric will help determine which stores have longer customer wait times.
#### Tables
#### fct_checkout_times(store_id, transaction_id, checkout_start_time, checkout_end_time)
#### dim_stores(store_id, store_name, location)
```
SELECT
ds.store_name,
avg(EXTRACT(EPOCH FROM (fct.checkout_end_time - fct.checkout_start_time)) / 60) AS avg_minutes
FROM fct_checkout_times fct JOIN dim_stores ds
ON fct.store_id = ds.store_id
WHERE EXTRACT(YEAR FROM fct.checkout_end_time) = 2024
AND EXTRACT(MONTH FROM fct.checkout_end_time) = 7
GROUP BY ds.store_name
```

#### For the stores with an average checkout wait time exceeding 10 minutes in July 2024, what are the average checkout wait times in minutes broken down by each hour of the day? Use the store information from dim_stores to ensure proper identification of each store. This detail will help pinpoint specific hours when wait times are particularly long.
#### Tables
#### fct_checkout_times(store_id, transaction_id, checkout_start_time, checkout_end_time)
#### dim_stores(store_id, store_name, location)
```
WITH avg_min_per_store AS (
  SELECT
    fct.store_id,
    AVG(EXTRACT(EPOCH FROM (fct.checkout_end_time - fct.checkout_start_time)) / 60) AS avg_minutes
  FROM fct_checkout_times fct
  WHERE fct.checkout_end_time >= '2024-07-01'
    AND fct.checkout_end_time < '2024-08-01'
  GROUP BY fct.store_id
  HAVING AVG(EXTRACT(EPOCH FROM (fct.checkout_end_time - fct.checkout_start_time)) / 60) > 10
)

SELECT
  ds.store_name,
  ds.location,
  EXTRACT(HOUR FROM fct.checkout_start_time) AS checkout_hour, 
  AVG(EXTRACT(EPOCH FROM (fct.checkout_end_time - fct.checkout_start_time)) / 60) AS avg_wait_time_minutes
FROM fct_checkout_times fct
JOIN avg_min_per_store amps ON fct.store_id = amps.store_id
JOIN dim_stores ds ON fct.store_id = ds.store_id
WHERE fct.checkout_end_time >= '2024-07-01'
  AND fct.checkout_end_time < '2024-08-01'
GROUP BY ds.store_name, ds.location, EXTRACT(HOUR FROM fct.checkout_start_time)
ORDER BY ds.store_name, checkout_hour;

```

#### Identify the seller segment with the highest payout success rate in July 2024 by comparing successful and failed payouts.
#### Tables
#### fct_payouts(payout_id, seller_id, payout_status, payout_date)
#### dim_sellers(seller_id, seller_segment)
```
WITH payout_summary as (
SELECT
  ds.seller_segment,
  COUNT(*) FILTER (WHERE fp.payout_status = 'successful') AS num_successful,
  COUNT(*) AS total_payouts
FROM fct_payouts fp 
JOIN dim_sellers ds ON fp.seller_id = ds.seller_id
WHERE fp.payout_date BETWEEN '2024-07-01' AND '2024-07-31'
GROUP BY 1
)

SELECT 
 seller_segment,
 ROUND(num_successful::numeric / total_payouts * 100, 0) AS success_ratio
FROM payout_summary
ORDER BY 2 DESC
```

#### What percentage of payouts were successful versus failed for each seller segment in July 2024, and how can this be used to recommend targeted improvements?
#### Tables
#### fct_payouts(payout_id, seller_id, payout_status, payout_date)
#### dim_sellers(seller_id, seller_segment)
```
WITH payout_summary as (
SELECT
  ds.seller_segment,
  COUNT(*) FILTER (WHERE fp.payout_status = 'successful') AS num_successful,
  COUNT(*) FILTER (WHERE fp.payout_status = 'failed') AS num_failed, 
  COUNT(*) AS total_payouts
FROM fct_payouts fp 
JOIN dim_sellers ds ON fp.seller_id = ds.seller_id
WHERE fp.payout_date BETWEEN '2024-07-01' AND '2024-07-31'
GROUP BY 1
)

SELECT 
 seller_segment,
 ROUND(num_successful::numeric / total_payouts * 100, 0) AS success_ratio,
 ROUND(num_failed::numeric / total_payouts * 100, 0) AS failed_ratio
FROM payout_summary
ORDER BY 2 DESC
```


#### The Grinch has brainstormed a ton of pranks for Whoville, but he only wants to keep the top prank per target, with the highest evilness score. Return the most evil prank for each target. If two pranks have the same evilness, the more recently brainstormed wins.
#### Tables
#### grinch_prank_ideas(prank_id, target_name, prank_description, evilness_score, created_at)
```
SELECT *
FROM (
  SELECT
    prank_id,
    target_name,
    evilness_score,
    ROW_NUMBER() OVER (
      PARTITION BY target_name
      ORDER BY evilness_score DESC, created_at DESC
    ) AS prank_rank 
  FROM grinch_prank_ideas
)
WHERE prank_rank = 1; 
```

#### Buddy is planning a winter getaway and wants to rank ski resorts by annual snowfall. Can you help him bucket these ski resorts into quartiles?
#### Tables
#### resort_monthly_snowfall(resort_id, resort_name, snow_month, snowfall_inches)
```
WITH annual_snowfall AS (
  SELECT 
    resort_id,
    resort_name,
    SUM(snowfall_inches) AS total_annual_snowfall
  FROM resort_monthly_snowfall
  GROUP BY resort_id, resort_name
)

SELECT
  resort_id,
  resort_name,
  total_annual_snowfall,
  NTILE(4) OVER (ORDER BY total_annual_snowfall DESC) AS snowfall_quartile
FROM annual_snowfall
```
