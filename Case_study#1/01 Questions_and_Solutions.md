# Case Study #1: 🥢Danny's Diner - Questions and Solutions
(MS SQL Server)

### Entity Diagram

<img width="400" alt="diagram" src="https://user-images.githubusercontent.com/122411152/213344062-34680331-9967-4eeb-8058-17994b4790c1.png">

<hr>

### 1.	What is the total amount each customer spent at the restaurant?
```sql
SELECT
  customer_id,
  SUM(price) AS total_amount_spent
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id;
```
   🪄 **Output:**

<img width="200" alt="c1_q1" src="https://user-images.githubusercontent.com/122411152/213344515-5c9dd6fa-b5c5-4603-bf9b-cc992947e57d.png">

<hr>

### 2. 	How many days has each customer visited the restaurant?
```sql
SELECT
  customer_id,
  COUNT(DISTINCT order_date) AS total_visited_days
FROM sales
GROUP BY customer_id;
```
   🪄 **Output:**
   
<img width="200" alt="c1_q2" src="https://user-images.githubusercontent.com/122411152/213344634-b4dcae73-ba2d-439d-91d0-aec0bc926976.png">

<hr>

### 3. What was the first item from the menu purchased by each customer?
There are two possible outputs using different functions as follow. The first output shows only one item that was first recorded in the system, and the latter shows all items purchased on the first purchasing date.

**(1) Only shows the first item purchased by each customer, even they bought more than one item on the first purchase date**
```sql
--I'm using ROW_NUMBER() function to get only the first item purchased by each customer

WITH item_ranked_cte AS(
  SELECT
    customer_id,
    order_date,
    product_id,
    ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY order_date) AS ranking
  FROM sales
  )

SELECT
  customer_id,
  product_name
FROM item_ranked_cte
JOIN menu ON item_ranked_cte.product_id = menu.product_id
WHERE ranking = 1;
```
   🪄 **Output 1:**
   
<img width="180" alt="c1_q3_1" src="https://user-images.githubusercontent.com/122411152/213344710-30e7e775-b955-453c-8cc1-3f88d8332e8d.png">

**(2) Shows all the items purchased by each customer on their first purchase date**
```sql
--I'm using RANK() function to get all items purchased by each customer on their first purchase date

WITH item_ranked_cte AS(
  SELECT
    customer_id,
    order_date,
    product_id,
    RANK() OVER(PARTITION BY customer_id ORDER BY order_date) AS ranking
  FROM sales
  )

SELECT
  customer_id,
  product_name
FROM item_ranked_cte
JOIN menu ON item_ranked_cte.product_id = menu.product_id
WHERE ranking = 1;
```
   🪄 **Output 2:**
   
<img width="180" alt="c1_q3_2" src="https://user-images.githubusercontent.com/122411152/213344902-fd91ccd4-c201-4888-9a4f-4ff8be732bda.png">

<hr>

### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
```sql
SELECT TOP 1
  product_name, 
  COUNT(product_name) AS item_count
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY product_name
ORDER BY product_name DESC;
```
   🪄 **Output:**
   
<img width="180" alt="c1_q4" src="https://user-images.githubusercontent.com/122411152/213345008-45e3375e-61c4-48cd-b516-59eac0704686.png">

<hr>

### 5. Which item was the most popular for each customer?
```sql
WITH popular_item_cte AS(
  SELECT
    customer_id,
    product_id,
    COUNT(product_id) AS item_count,
    RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(product_id) DESC) AS ranking
  FROM sales
  GROUP BY customer_id, product_id
  )

SELECT
  customer_id,
  product_name,
  item_count
FROM popular_item_cte
JOIN menu ON popular_item_cte.product_id = menu.product_id
WHERE ranking = 1;
```
   🪄 **Output:**
   
<img width="250" alt="c1_q5" src="https://user-images.githubusercontent.com/122411152/213345140-c2a1da86-e6de-4b5b-bc53-9957ed9c3c10.png">

<hr>

### 6. Which item was purchased first by the customer after they became a member?
```sql
WITH member_orders_cte AS(
  SELECT
    sales.customer_id,
    order_date,
    join_date,
    product_id, 
    RANK() OVER(PARTITION BY sales.customer_id ORDER BY order_date) AS ranking
  FROM sales
  JOIN members ON sales.customer_id = members.customer_id
  WHERE order_date >= join_date
  )

SELECT
  customer_id,
  product_name,
  order_date,
  join_date
FROM member_orders_cte
JOIN menu ON member_orders_cte.product_id = menu.product_id
WHERE ranking = 1;
```
   🪄 **Output:**
   
<img width="280" alt="c1_q6" src="https://user-images.githubusercontent.com/122411152/213347845-f2d69f9e-d0c4-420c-a3b7-462f41a4bf31.png">

<hr>

### 7. Which item was purchased just before the customer became a member?
```sql
WITH nonmember_cte AS(
  SELECT
    sales.customer_id,
    order_date,
    join_date,
    product_id, 
    RANK() OVER(PARTITION BY sales.customer_id ORDER BY order_date DESC) AS ranking
  FROM sales
  JOIN members ON sales.customer_id = members.customer_id
  WHERE order_date < join_date
  )

SELECT
  customer_id,
  product_name,
  order_date,
  join_date
FROM nonmember_cte
JOIN menu ON nonmember_cte.product_id = menu.product_id
WHERE ranking = 1;
```
   🪄 **Output:**
   
<img width="280" alt="c1_q7" src="https://user-images.githubusercontent.com/122411152/213348048-8d52ae6d-cf74-49da-bf89-2c575e720b38.png">

<hr>

### 8. What are the total items and amount spent for each member before they became a member?
I provided two outputs, one output shows the results of customers who have become members, and the other one shows the results of all customers.
```sql
--Following is a query that only includes customers who are in the member list

SELECT
  sales.customer_id,
  COUNT(DISTINCT sales.product_id) AS item_count,
  SUM(price) AS total_amount
FROM sales
JOIN menu ON sales.product_id = menu.product_id
JOIN members ON sales.customer_id = members.customer_id
WHERE order_date < join_date
GROUP BY sales.customer_id;
```
   🪄 **Output 1:**
   
<img src="images/c1_q8_1.png" width="250">

```sql
--Following is a query that also includes customers who are not in the member list

SELECT
  sales.customer_id,
  COUNT(DISTINCT sales.product_id) AS item_count,
  SUM(price) AS total_amount
FROM sales
JOIN menu ON sales.product_id = menu.product_id
LEFT JOIN members ON sales.customer_id = members.customer_id
WHERE order_date < join_date OR join_date IS NULL
GROUP BY sales.customer_id;
```
   🪄 **Output 2:**
   
<img src="images/c1_q8_2.png" width="250">

<hr>

### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
WITH points_cte AS(
  SELECT
    customer_id,
    CASE
      WHEN product_name = 'sushi' THEN price*20
      ELSE price*10
    END AS points
  FROM sales
  JOIN menu ON sales.product_id = menu.product_id
  )

SELECT
  customer_id,
  SUM(points) AS total_points
FROM points_cte
GROUP BY customer_id;
```
   🪄 **Output:**
   
<img src="images/c1_q9.png" width="200">

<hr>

### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customers A and B have at the end of January?
```sql
WITH member_points_cte AS(
  SELECT
    sales.customer_id,
    CASE
      WHEN product_name = 'sushi' THEN price*20
      WHEN order_date BETWEEN join_date AND DATEADD(day, 6, join_date) THEN price*20
      ELSE price*10
    END AS points
  FROM sales
  JOIN menu ON sales.product_id = menu.product_id
  JOIN members ON sales.customer_id = members.customer_id
  WHERE order_date < '2021-02-01'
  )

SELECT
  customer_id,
  SUM(points) AS total_points
FROM member_points_cte
GROUP BY customer_id;
```
   🪄 **Output:**
   
<img src="images/c1_q10.png" width="200">

<hr>

👉[Click to see bonus questions and solutions](02%20Bonus_questions_solutions.md)
