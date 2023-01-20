##  Case Study #2: Pizza Runner 
<h1 align='center'> 👥 B. Runner and Customer Experience </h1>
(MS SQL Server)

![](https://giphy.com/embed/z2Wd0Fvm75L2)

<hr>

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```sql
-- The weekday of 2021-01-01 is Friday

SET DATEFIRST 5

SELECT
  DATEPART(week, registration_date) AS week_no,
  COUNT(DATEPART(week, registration_date)) AS runners_registered
FROM runners
GROUP BY DATEPART(week, registration_date);
```
   🪄 **Output:**

<img width="170" alt="c2_b1" src="https://user-images.githubusercontent.com/122411152/213526095-a4cd2289-44af-41e1-a526-19c25105e668.png">

<hr>

### 2. How many unique customer orders were made?
```sql
SELECT
  COUNT(DISTINCT order_id) AS total_orders_count
FROM customer_orders;
```
   🪄 **Output:**
   


<hr>

### 3. How many successful orders were delivered by each runner?
```sql
SELECT
  runner_id,
  COUNT(order_id) AS delivered_orders
FROM runner_orders
WHERE cancellation IS NULL
GROUP BY runner_id;
```
   🪄 **Output:**
   


<hr>

### 4. How many of each type of pizza was delivered?
```sql
WITH delivered_cte AS(
  SELECT
    pizza_id,
    COUNT(pizza_id) AS delivered_count
  FROM customer_orders AS co
  JOIN runner_orders AS ro ON co.order_id = ro.order_id
  WHERE cancellation IS NULL
  GROUP BY pizza_id
	)

SELECT
  pizza_name,
  delivered_count
FROM delivered_cte AS d
JOIN pizza_names AS p ON d.pizza_id = p.pizza_id;
```
   🪄 **Output:**
   


<hr>

### 5. How many Vegetarian and Meatlovers were ordered by each customer?
```sql
WITH distribution_cte AS(
  SELECT
    customer_id,
    pizza_id,
    COUNT(pizza_id) AS order_count
  FROM customer_orders
  GROUP BY customer_id, pizza_id
  )

SELECT
  pizza_name,
  customer_id,
  order_count
FROM distribution_cte AS d
JOIN pizza_names AS p ON d.pizza_id = p.pizza_id;
```
   🪄 **Output:**
   


<hr>

### 6. What was the maximum number of pizzas delivered in a single order?
```sql
SELECT TOP 1
  COUNT(co.order_id) AS pizzas_count
FROM customer_orders AS co
JOIN runner_orders AS ro ON co.order_id = ro.order_id
WHERE cancellation IS NULL
GROUP BY co.order_id 
ORDER BY pizzas_count DESC;
```
   🪄 **Output:**
   


<hr>

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```sql
WITH changes_cte AS(
  SELECT
    customer_id,
    CASE
    	WHEN exclusions IS NULL AND extras IS NULL THEN 0
	ELSE 1
    END AS changes,
    CASE
    	WHEN exclusions IS NULL AND extras IS NULL THEN 1
	ELSE 0
    END AS no_changes
  FROM customer_orders AS co
  JOIN runner_orders AS ro ON co.order_id = ro.order_id
  WHERE cancellation IS NULL
  )

SELECT
  customer_id,
  SUM(changes) AS customized,
  SUM(no_changes) AS unchanged
FROM changes_cte
GROUP BY customer_id;
```
   🪄 **Output:**
   


<hr>

👉Click to see next section [C. Ingredient Optimisation ](Bonus_questions_solutions.md)
