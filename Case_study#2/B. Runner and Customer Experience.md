##  Case Study #2: Pizza Runner 
<h1 align='center'> 👥 B. Runner and Customer Experience </h1>
(MS SQL Server)

<hr>

### 1. How many pizzas were ordered?
```sql
SELECT
  COUNT(order_id) AS total_pizzas_count
FROM customer_orders;
```
   🪄 **Output:**

<img width="150" alt="c2_a1" src="https://user-images.githubusercontent.com/122411152/213336959-7280c9ee-f285-47cb-bf55-235918c18f3c.png">

<hr>

### 2. How many unique customer orders were made?
```sql
SELECT
  COUNT(DISTINCT order_id) AS total_orders_count
FROM customer_orders;
```
   🪄 **Output:**
   
<img width="150" alt="c2_a2" src="https://user-images.githubusercontent.com/122411152/213337201-32442632-7af6-4030-8d6e-a3ceedeac0f2.png">

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
   
<img width="200" alt="c2_a3" src="https://user-images.githubusercontent.com/122411152/213337337-f3a599f6-433a-4149-aa54-804dd34156b5.png">

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
   
<img width="220" alt="c2_a4" src="https://user-images.githubusercontent.com/122411152/213337481-42ed48ba-3527-42f9-b322-d17059d44769.png">

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
   
<img width="250" alt="c2_a5" src="https://user-images.githubusercontent.com/122411152/213337687-ec8155ba-d271-41fd-b198-d3cfb07a4ccf.png">

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
   
<img width="120" alt="c2_a6" src="https://user-images.githubusercontent.com/122411152/213337971-23b44972-bc99-4445-9921-807cbb2c5a62.png">

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
   
<img width="250" alt="c2_a7" src="https://user-images.githubusercontent.com/122411152/213338163-67503a42-21ca-44e8-a623-06ec371c7036.png">

<hr>

👉Click to see next section [C. Ingredient Optimisation ](Bonus_questions_solutions.md)
