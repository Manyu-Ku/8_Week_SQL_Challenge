# Case Study 2 - Pizza Runner

<img src="https://user-images.githubusercontent.com/122411152/213340568-f4c531de-1d97-4bff-8eb8-530cc7100c02.png" width="450" />

## Business Problem
Danny had started a new business called Pizza Runner, which is an idea of uberized pizza business! He was very aware that data collection was going to be critical for his business’ growth. He has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.

## Entity Diagram
[Click to see schema creation](00%20SchemaCreation.md)

<img width="563" alt="diagram_1" src="https://user-images.githubusercontent.com/122411152/213341480-0a4f0bd3-6761-4f87-a17e-042f2bee922f.png">

## Case Study Questions:
Throughout this case study, you will find sections on:

**[Data Cleaning](01%20Data%20Cleaning.md)**

**[A. Pizza Metrics](A.%20Pizza%20Metrics.md)**
<details><summary>Click to expand questions</summary>
      
      1. How many pizzas were ordered?
     
      2. How many unique customer orders were made?
     
      3. How many successful orders were delivered by each runner?
     
      4. How many of each type of pizza was delivered?
     
      5. How many Vegetarian and Meatlovers were ordered by each customer?
     
      6. What was the maximum number of pizzas delivered in a single order?
     
      7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
     
      8. How many pizzas were delivered that had both exclusions and extras?
     
      9. What was the total volume of pizzas ordered for each hour of the day?
     
      10. What was the volume of orders for each day of the week?
</details>
     
**[B. Runner and Customer Experience](B.%20Runner%20and%20Customer%20Experience.md)**
<details><summary>Click to expand questions</summary>
      
      1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
     
      2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
     
      3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
      
      4. What was the average distance travelled for each customer?
      
      5. What was the difference between the longest and shortest delivery times for all orders?
      
      6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
      
      7. What is the successful delivery percentage for each runner?
</details>


**C. Ingredient Optimisation**
<details><summary>Click to expand questions</summary>
      
      1. What are the standard ingredients for each pizza?
     
      2. What was the most commonly added extra?
     
      3. What was the most common exclusion?
      
      4. Generate an order item for each record in the customers_orders table in the format of one of the following:
            o	Meat Lovers
            o	Meat Lovers - Exclude Beef
            o	Meat Lovers - Extra Bacon
            o	Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers
      
      5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table 
         and add a 2x in front of any relevant ingredients (For example: "Meat Lovers: 2xBacon, Beef, ... , Salami")
      
      6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?
</details>

**D. Pricing and Ratings**

<hr>

Source: [Click to see original case study page from 8 Week SQL Challenge](https://8weeksqlchallenge.com/case-study-2)
