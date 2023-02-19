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

### 4. Generate an order item for each record in the customers_orders table in the format of text:
```sql
--Add IDENTITY row as record to make reference easily

ALTER TABLE customer_orders
ADD record INT IDENTITY(1,1);

--Change extra toppings into string and create temp table #extras

DROP TABLE IF EXISTS #extras;

WITH add_cte AS(
  SELECT
    record,
    TRIM(value) AS added
  FROM customer_orders
  CROSS APPLY STRING_SPLIT(extras, ',')
  )

SELECT 
  record,
  '- Extra ' + STRING_AGG(CAST(topping_name AS VARCHAR),', ') AS change
INTO #extras
FROM  add_cte
JOIN pizza_toppings ON add_cte.added = pizza_toppings.topping_id
GROUP BY record;

--Change exclude toppings into string and create temp table #exclusions

DROP TABLE IF EXISTS #exclusions;

WITH excluded_cte AS(
  SELECT
    record,
    TRIM(value) AS excluded
  FROM customer_orders
  CROSS APPLY STRING_SPLIT(exclusions, ',')
  )

SELECT 
  record,
  '- Exclude ' + STRING_AGG(CAST(topping_name AS VARCHAR),', ') AS change
INTO #exclusions
FROM  excluded_cte
JOIN pizza_toppings ON excluded_cte.excluded = pizza_toppings.topping_id
GROUP BY record;

--Integrate #extras and #exclusions into #toppings

WITH change_cte AS(
  SELECT * FROM #extras
  UNION
  SELECT * FROM #exclusions
  )

SELECT
  record,
  STRING_AGG(change,' ') AS final_toppings
INTO #toppings
FROM change_cte
GROUP BY record;

--Join pizza names with final toppings list

SELECT 
  customer_orders.record,
  CONCAT(pizza_name, final_toppings, ' ') AS details
FROM customer_orders
JOIN pizza_names ON customer_orders.pizza_id = pizza_names.pizza_id
LEFT JOIN #toppings ON customer_orders.record = #toppings.record;
```
   🪄 **Output:**
   
<img width="400" alt="c2_c4" src="https://user-images.githubusercontent.com/122411152/219909293-bceb43ad-233b-48ca-8b15-eedf17843a3e.png">

<hr>

### 5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients?
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
