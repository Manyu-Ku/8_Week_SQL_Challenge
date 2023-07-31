##  Case Study #2: Pizza Runner 
<h1 align='center'> :label: D. Pricing and Ratings </h1>
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
