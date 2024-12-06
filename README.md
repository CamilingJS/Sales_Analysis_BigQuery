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

#### 3: Clothing from September 2015, cost at least 70
```
SELECT * 
FROM prework.sales
WHERE YEAR = 2015
  and month = "September"
  and cost >= 70
ORDER BY date;
```

#### 4: Product categories from 2014 to 2016 in France
```
SELECT DISTINCT Product_Category
FROM prework.sales
WHERE year BETWEEN 2014 AND 2016
  AND country = 'France';
```

#### 5: Within each product category and age group (combined), what is the average order quantity and total profit?
```
SELECT DISTINCT Product_Category, 
  Age_Group,
  Round(Avg(Order_Quantity), 2) as avg_order_qty_,
  Round(Sum(Profit), 2) as total_profit_
FROM prework.sales
group by 1,2;
```
