# 🥢Case Study #1: Danny's Diner - Questions and Solutions
(MS SQL Server)

### 1.	What is the total amount each customer spent at the restaurant?
```sql
SELECT
  customer_id,
  SUM(price) total_amount_spent
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id;
```
 ### - Output:
 <img scr="images/c1_q1.png" />
