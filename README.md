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



