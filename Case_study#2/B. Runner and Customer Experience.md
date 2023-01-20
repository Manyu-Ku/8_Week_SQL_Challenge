##  Case Study #2: Pizza Runner 
<h1 align='center'> 👥 B. Runner and Customer Experience </h1>
(MS SQL Server)

<hr>

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```sql
-- The weekday of 2021-01-01 is Friday, so we have to set the startday on Friday(5)

SET DATEFIRST 5

SELECT
  DATEPART(week, registration_date) AS week_no,
  COUNT(DATEPART(week, registration_date)) AS runners_registered
FROM runners
GROUP BY DATEPART(week, registration_date);
```
   🪄 **Output:**

<img width="180" alt="c2_b1" src="https://user-images.githubusercontent.com/122411152/213526095-a4cd2289-44af-41e1-a526-19c25105e668.png">

<hr>

### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pick up the order?
```sql
SELECT
  runner_id,
  AVG(DATEDIFF(minute, order_time, pickup_time)) AS avg_pickup_min
FROM customer_orders AS co
JOIN runner_orders AS ro ON co.order_id = ro.order_id
GROUP BY runner_id;
```
   🪄 **Output:**
   
<img width="180" alt="c2_b2" src="https://user-images.githubusercontent.com/122411152/213607353-3036e2f0-ef9d-4f4f-91d3-cd4a7d51ebfc.png">

<hr>

### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
```sql
WITH prep_cte AS(
  SELECT
    COUNT(co.order_id) AS pizzas_count,
	DATEDIFF(minute, order_time, pickup_time) AS prep_min
  FROM customer_orders AS co
  JOIN runner_orders AS ro ON co.order_id = ro.order_id
  WHERE cancellation IS NULL
  GROUP BY co.order_id, order_time, pickup_time
  )

SELECT
  pizzas_count,
  AVG(prep_min) AS avg_prep_min
FROM prep_cte
GROUP BY pizzas_count;
```
   🪄 **Output:**
   
<img width="180" alt="c2_b3" src="https://user-images.githubusercontent.com/122411152/213609445-5909e0c7-f848-4678-b460-ec9db8b6637e.png">

<hr>

### 4. What was the average distance traveled for each customer?
```sql
SELECT
  customer_id,
  ROUND(AVG(CAST(distance AS float)), 1) AS avg_distance
FROM customer_orders AS co
JOIN runner_orders AS ro ON co.order_id = ro.order_id
GROUP BY customer_id;
```
   🪄 **Output:**
   
<img width="180" alt="c2_b4" src="https://user-images.githubusercontent.com/122411152/213611299-503caeb2-805b-45fb-85cd-905b819b0a06.png">

<hr>

### 5. What was the difference between the longest and shortest delivery times for all orders?
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
