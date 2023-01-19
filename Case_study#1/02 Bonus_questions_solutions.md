# Case Study #1: 🥢Danny's Diner - Bonus Questions and Solutions
(MS SQL Server)

### Entity Diagram

<img width="400" alt="diagram" src="https://user-images.githubusercontent.com/122411152/213344062-34680331-9967-4eeb-8058-17994b4790c1.png">

<hr>

### 1. Join all the things
The following questions are related to creating basic data tables that Danny and his team can use to quickly derive insights without needing to join the underlying tables using SQL. Table should include following columns: **customer_id, order_date, product_name, price, member.**

```sql
SELECT
  sales.customer_id,
  order_date,
  product_name,
  price,
  CASE
    WHEN join_date IS NULL THEN 'N'
    WHEN order_date < join_date THEN 'N'
    ELSE 'Y'
  END AS member
FROM sales
JOIN menu ON sales.product_id = menu.product_id
LEFT JOIN members ON sales.customer_id = members.customer_id;
```
   🪄 **Output:**

<img width="300" alt="c1_b1" src="https://user-images.githubusercontent.com/122411152/213519376-ca786237-f090-4bf4-a2e0-de30d10e7151.png">

<hr>

### 2. Rank all the things
Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.

```sql
-- Step 1: Create a cte based on query 1 (Join all the things)

WITH merged_table_cte AS(
  SELECT
    sales.customer_id,
    order_date,
    product_name,
    price,
    CASE
      WHEN join_date IS NULL THEN 'N'
      WHEN order_date < join_date THEN 'N'
      ELSE 'Y'
    END AS member
  FROM sales
  JOIN menu ON sales.product_id = menu.product_id
  LEFT JOIN members ON sales.customer_id = members.customer_id
  )

-- Step 2: Add column 'ranking' to show the order of items being purchased by each customer 
--         after becoming members

SELECT
  customer_id,
  order_date,
  product_name,
  price,
  member,
  CASE
    WHEN member = 'N' THEN NULL
    ELSE RANK() OVER(PARTITION BY customer_id, member ORDER BY order_date)
  END AS rankng
FROM merged_table_cte;
```
   🪄 **Output:**

<img width="350" alt="c1_b2" src="https://user-images.githubusercontent.com/122411152/213519471-d56e319d-b783-456b-9471-31af63cb4e32.png">

<hr>
