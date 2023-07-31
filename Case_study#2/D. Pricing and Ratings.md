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

SELECT SUM(order_count*price) total_sales
FROM #pizza_price;
```
   🪄 **Output:**

![c2_d1](https://github.com/Manyu-Ku/8_Week_SQL_Challenge/assets/122411152/48effac0-a6a5-48e7-9aa8-5e65b003f582)

<hr>

### 2. What was the most commonly added extra?
```sql
WITH extra_cte AS(
  SELECT 
    TRIM(value) AS extra_topping,
    COUNT(TRIM(value)) AS frequency
  FROM customer_orders
  CROSS APPLY STRING_SPLIT(extras, ',')
  GROUP BY TRIM(value)
	)

SELECT 
  topping_name,
  frequency
FROM extra_cte
JOIN pizza_toppings on extra_cte.extra_topping = pizza_toppings.topping_id
ORDER BY frequency DESC;
```
   🪄 **Output:**
   
<img width="160" alt="c2_c2" src="https://user-images.githubusercontent.com/122411152/218227906-af9f12ef-c1bf-4e3e-a00d-1649df921ba0.png">

<hr>

### 3. What was the most common exclusion?
```sql
WITH excluded_cte AS(
  SELECT 
    TRIM(value) AS excluded_topping,
    COUNT(TRIM(value)) AS frequency
  FROM customer_orders
  CROSS APPLY STRING_SPLIT(exclusions, ',')
  GROUP BY TRIM(value)
	)

SELECT 
  topping_name,
  frequency
FROM excluded_cte
JOIN pizza_toppings on excluded_cte.excluded_topping = pizza_toppings.topping_id
ORDER BY frequency DESC;
```
   🪄 **Output:**
   
<img width="160" alt="c2_c3" src="https://user-images.githubusercontent.com/122411152/218228645-ecce85d8-6bfe-4ea8-b3d4-3505458ec8d8.png">
