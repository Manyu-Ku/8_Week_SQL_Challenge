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

### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
```sql
Meat Lovers
Meat Lovers - Exclude Beef
Meat Lovers - Extra Bacon
Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers
```
Using 💎**Temp Tables, 💎STRING_SPLIT, 💎STRING_AGG, 💎UNION**, and 💎**CONCAT** to achieve the outputs.
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
  CONCAT(pizza_name, ' ', final_toppings) AS details
FROM customer_orders
JOIN pizza_names ON customer_orders.pizza_id = pizza_names.pizza_id
LEFT JOIN #toppings ON customer_orders.record = #toppings.record;
```
   🪄 **Output:**
   
<img width="400" alt="c2_c4" src="https://user-images.githubusercontent.com/122411152/219932596-bb1bf88a-01cf-4a61-ae14-38db4de2c592.png">

<hr>

### 5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients?
```sql
--Create temp table #original including original topping list

DROP TABLE IF EXISTS #original

SELECT
  record,
  TRIM(value) AS recipe
INTO #original
FROM customer_orders
JOIN pizza_recipes ON customer_orders.pizza_id = pizza_recipes.pizza_id
CROSS APPLY STRING_SPLIT(CAST(toppings AS VARCHAR), ',')
ORDER BY record;

--Create temp table #omit including excluded topping list

DROP TABLE IF EXISTS #omit

SELECT
  record,
  TRIM(value) AS excluded
INTO #omit
FROM customer_orders
CROSS APPLY STRING_SPLIT(exclusions, ',');

--Create temp table #add including additional topping list

DROP TABLE IF EXISTS #add

SELECT
  record,
  TRIM(value) AS added
INTO #add
FROM customer_orders
CROSS APPLY STRING_SPLIT(extras, ',');

--Exclude exclusions from original list and store in #templist

WITH omit_cte AS(
  SELECT * FROM #original
  UNION ALL
  SELECT * FROM #omit
  )

SELECT
  record,
  recipe
INTO #templist
FROM (SELECT
	record,
	recipe,
	COUNT(recipe) AS recipe_count
      FROM omit_cte
      GROUP BY record, recipe) AS pre
WHERE recipe_count =1
ORDER BY record;

--Add extra toppings to #templist and store in #final_list

WITH add_cte AS(
  SELECT * FROM #templist
  UNION ALL
  SELECT * FROM #add
  )

SELECT
  *,
  COUNT(recipe) AS doubled
INTO #final_list
FROM add_cte
GROUP BY record, recipe;

--Transform topping_id into toppings name

WITH double_cte AS(
  SELECT
    record,
    CASE
      WHEN doubled = 1 THEN topping_name
      ELSE '2x '+ CAST(topping_name AS VARCHAR)
    END AS ingredients
  FROM #final_list
  JOIN pizza_toppings ON pizza_toppings.topping_id = #final_list.recipe
  )

SELECT 
  double_cte.record,
  pizza_id,
  STRING_AGG(CAST(ingredients AS VARCHAR), ', ') AS list
INTO #all_toppings
FROM double_cte
JOIN customer_orders ON customer_orders.record = double_cte.record
GROUP BY double_cte.record, pizza_id;

--Concat pizza names with topping list

SELECT
  record,
  CONCAT(pizza_name, ': ', list) topping_list
FROM #all_toppings
JOIN pizza_names ON pizza_names.pizza_id = #all_toppings.pizza_id
ORDER BY record;
```
   🪄 **Output:**
   
<img width="450" alt="c2_c5" src="https://user-images.githubusercontent.com/122411152/219932668-5086af50-b712-4f29-9c0c-a19f5b5770d3.png">

<hr>


👉Click to see next section [C. Ingredient Optimisation ](Bonus_questions_solutions.md)
