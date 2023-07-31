##  Case Study #2: Pizza Runner 
<h1 align='center'> :label: D. Pricing and Ratings </h1>
(MS SQL Server)

<hr>


### 1. If a Meat Lovers pizza costs $12 and Vegetarian costs $10 and there were no charges for changes - how much money has Pizza Runner made so far if there are no delivery fees??
```sql
SELECT 
  pizza_id,
  COUNT(pizza_id) order_count,
  CASE 
    WHEN pizza_id = 1 THEN 12
    ELSE 10
  END AS price
INTO #pizza_price
FROM customer_orders c
JOIN runner_orders r ON c.order_id = r.order_id
WHERE cancellation IS NULL
GROUP BY pizza_id;

SELECT 
  SUM(order_count*price) AS total_sales
FROM #pizza_price;
```
   🪄 **Output:**

![c2_d1](https://github.com/Manyu-Ku/8_Week_SQL_Challenge/assets/122411152/48effac0-a6a5-48e7-9aa8-5e65b003f582)

<hr>

### 2. What if there was an additional $1 charge for any pizza extras? Add cheese is $1 extra ($2 for extra cheese)--topping_id of cheese = 4
```sql
SELECT 
  CASE 
    WHEN pizza_id = 1 THEN 12
	  ELSE 10
  END AS pizza_price,
  CASE 
    WHEN LEN(extras) = 1 AND extras <> '4' THEN 1
	  WHEN LEN(extras) = 1 AND extras = '4' THEN 2
	  WHEN LEN(extras) > 1 AND extras LIKE '%4%' THEN LEN(extras)/3 + 2
	  WHEN LEN(extras) > 1 AND extras NOT LIKE '%4%' THEN LEN(extras)/3 +1
	  ELSE 0
  END AS extra_charge
INTO #total_charge
FROM customer_orders c
JOIN runner_orders r ON c.order_id = r.order_id
WHERE cancellation IS NULL;

SELECT 
  SUM(pizza_price) + SUM(extra_charge) AS sales_with_charges
FROM #total_charge;
```
   🪄 **Output:**
   
![c2_d2](https://github.com/Manyu-Ku/8_Week_SQL_Challenge/assets/122411152/a10cd344-61ff-45bf-99b8-8d869071546f)

<hr>

