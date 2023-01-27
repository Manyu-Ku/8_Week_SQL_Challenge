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
SELECT
  MAX(duration) - MIN(duration) AS min_difference
FROM customer_orders AS co
JOIN runner_orders AS ro ON co.order_id = ro.order_id
WHERE cancellation IS NULL;
```
   🪄 **Output:**
   
<img width="120" alt="c2_b5" src="https://user-images.githubusercontent.com/122411152/213875734-c9803115-8b1e-4d8f-9a31-b5eb6db954d3.png">

<hr>

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
```sql
-- speed(km/hr) = distance(km) / duration(min) * 60

-- average speed of each order
SELECT
  runner_id,
  ro.order_id,
  ROUND(CAST(distance AS FLOAT)/duration*60, 2) AS speed
FROM customer_orders AS co
JOIN runner_orders AS ro ON co.order_id = ro.order_id
WHERE cancellation IS NULL
GROUP BY runner_id, ro.order_id, distance, duration;

-- average speed of each runner
SELECT
  runner_id,
  ROUND(AVG(CAST(distance AS FLOAT)/duration*60), 2) AS avg_speed
FROM customer_orders AS co
JOIN runner_orders AS ro ON co.order_id = ro.order_id
WHERE cancellation IS NULL
GROUP BY runner_id;  
```
   🪄 **Output:**
   
| Speed per Oder | Avg Speed per Runner |
| --- | ---|
| <img width="170" alt="c2_b6_1" src="https://user-images.githubusercontent.com/122411152/213879137-12fe019f-367f-410d-b7dd-840d5923f631.png"> | <img width="140" alt="c2_b6_2" src="https://user-images.githubusercontent.com/122411152/213879188-c7817d0d-6d5d-4e13-883d-187b70647c07.png"> |

Runners' speeds seem to increase with experience.

<hr>

### 7. What is the successful delivery percentage for each runner?
```sql
SELECT
  runner_id,
  CAST(SUM(CASE 
             WHEN cancellation IS NULL THEN 1
             ELSE 0
           END) AS FLOAT)/COUNT(order_id) AS  successful_percentage
FROM runner_orders
GROUP BY runner_id;
```
   🪄 **Output:**
   
<img width="200" alt="c2_b7" src="https://user-images.githubusercontent.com/122411152/213878898-e708e7f5-4e0b-4501-89fa-fa987ec0d726.png">

<hr>

👉Click to see next section [C. Ingredient Optimisation ](C.%20Ingredient%20Optimisation.md)
