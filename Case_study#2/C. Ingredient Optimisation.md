##  Case Study #2: Pizza Runner 
<h1 align='center'> 🧀 C. Ingredient Optimisation </h1>
(MS SQL Server)

<hr>

### 1. What are the standard ingredients for each pizza?
```sql
WITH topping_split_cte AS(
  SELECT 
    pizza_id, 
    TRIM(value) AS topping_id
  FROM pizza_recipes
  CROSS APPLY STRING_SPLIT(CAST(toppings AS VARCHAR), ',')
	)

SELECT 
  pizza_id,
  STRING_AGG(CAST(topping_name AS VARCHAR), ', ') AS ingredients
INTO recipes
FROM topping_split_cte AS split
JOIN pizza_toppings AS topping ON split.topping_id = topping.topping_id
GROUP BY pizza_id;

SELECT 
  pizza_name,
  ingredients
FROM pizza_names
JOIN recipes ON pizza_names.pizza_id = recipes.pizza_id;
```
   🪄 **Output:**

<img width="430" alt="c2_c1" src="https://user-images.githubusercontent.com/122411152/218226258-9f17647a-6e3a-4f43-af3b-0cc52af93173.png">

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

👉Click to see next section [C. Ingredient Optimisation ](Bonus_questions_solutions.md)
