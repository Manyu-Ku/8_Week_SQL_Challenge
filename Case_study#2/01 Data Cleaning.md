# Case Study #2: :runner:Pizza Runner - Data Cleaning
(MS SQL Server)

### Entity Diagram

<img width="563" alt="diagram_1" src="https://user-images.githubusercontent.com/122411152/213341480-0a4f0bd3-6761-4f87-a17e-042f2bee922f.png">

 :books:Based on the tables provided by Danny, I will clean the data table by table.
  
  **However, only [table 2](#table-2-customer_orders) and [table 3](#table-3-runner_orders) were modified.**
<hr>

### Table 1: runners
| Original table | Data Type |
| :---: | :---: |
| <img width="170" alt="c2_t1" src="https://user-images.githubusercontent.com/122411152/213341652-8df8c642-47df-4388-b8ea-c526c4107fa5.png"> | <img width="167" alt="t1_d" src="https://user-images.githubusercontent.com/122411152/213341719-81808da5-39b2-41a3-bb3d-36d4ebff70cd.png"> |

Nothing needs to be changed for this table.:+1:

---
### Table 2: customer_orders
| Original table | Data Type |
| :---: | :---: |
| <img width="400" alt="c2_t2" src="https://user-images.githubusercontent.com/122411152/213342053-9c623255-41d5-47db-80e2-6f5a588ae462.png"> | <img width="166" alt="t2_d" src="https://user-images.githubusercontent.com/122411152/213341838-68836799-d03a-42f6-aaaa-877ebeda3bc3.png"> |

**Issues:** columns ```exclusions``` and ```extras``` have non-standardized null value
```sql
-- column: exclusions - standardize the NULL value

UPDATE customer_orders
SET exclusions = NULL
WHERE exclusions = '' OR exclusions = 'null';

-- column: extras - standardize the NULL value

UPDATE customer_orders
SET extras = NULL
WHERE extras = '' OR extras = 'null';
```
**Result:**
| Cleaned table | Data Type |
| :---: | :---: |
| <img width="400" alt="c2_t2 1" src="https://user-images.githubusercontent.com/122411152/213341941-f0a7c2e7-de50-4812-8e77-6c0fac4a0841.png"> | <img width="166" alt="t2_d" src="https://user-images.githubusercontent.com/122411152/213341838-68836799-d03a-42f6-aaaa-877ebeda3bc3.png"> |

---
### Table 3: runner_orders
| Original table | Data Type |
| :---: | :---: |
| <img width="450" alt="c2_t3" src="https://user-images.githubusercontent.com/122411152/213342284-408ad197-677b-4423-94ae-d964305d03fb.png"> | <img width="181" alt="t3_d" src="https://user-images.githubusercontent.com/122411152/213342331-851c5edd-84e7-4560-a7da-8da088f0878f.png"> |

**Issues:**
 1. Column ```pickup_time```'s data type should be DATETIME.
 2. The value of column ```distance``` should be standardized, and the data type should be DECIMAL for further calculations.
 3. The value of column ```duration``` should be standardized, and the data type should be INTEGER for further calculations.
 4. Column ```cancellation```'s null value should be standardized.
```sql
-- Reminder: Standardized NULL value before changing data types

-- column: pickup_time

UPDATE runner_orders
SET pickup_time = NULL
WHERE pickup_time = 'null';

ALTER TABLE runner_orders
ALTER COLUMN pickup_time DATETIME;

-- column: distance

UPDATE runner_orders
SET distance = NULL
WHERE distance = 'null';

UPDATE runner_orders
SET distance = TRIM('km ' FROM distance)
WHERE distance Like '%km';

ALTER TABLE runner_orders
ALTER COLUMN distance DECIMAL(10, 1);

-- column: duration

UPDATE runner_orders
SET duration = NULL
WHERE duration = 'null';

UPDATE runner_orders
SET duration = TRIM(' minutes' FROM duration)
WHERE duration Like '%min%';

ALTER TABLE runner_orders
ALTER COLUMN duration INTEGER;

-- column: cancellation

UPDATE runner_orders
SET cancellation = NULL
WHERE cancellation = 'null' OR cancellation = '' ;
```
**Result:**
| Cleaned table | Data Type |
| :---: | :---: |
| <img width="450" alt="c2_t3 1" src="https://user-images.githubusercontent.com/122411152/213342756-dd681f93-02f7-4a82-b1d6-333aae0780d5.png"> | <img width="182" alt="t3_d1" src="https://user-images.githubusercontent.com/122411152/213342831-3f45e486-ab8f-4cbd-ab93-adf92da95922.png">
 |

---
### Table 4: pizza_names
| Original table | Data Type |
| :---: | :---: |
| <img width="150" alt="c2_t4" src="https://user-images.githubusercontent.com/122411152/213343027-490e9b85-3ef9-4fde-baae-a36b2c50ec57.png"> | <img width="134" alt="t4_d" src="https://user-images.githubusercontent.com/122411152/213343076-82ae223f-cf43-47b4-b036-c9902d27eb35.png"> |

Nothing needs to be changed for this table.:+1:

---
### Table 5: pizza_recipes
| Original table | Data Type |
| :---: | :---: |
| <img width="180" alt="c2_t5" src="https://user-images.githubusercontent.com/122411152/213343240-5538474e-ffd0-4b26-ab50-5e68fac452a8.png"> | <img width="136" alt="t5_d" src="https://user-images.githubusercontent.com/122411152/213343277-94cec50f-2fb1-4f9e-9063-b4abcfe8f295.png"> |

Nothing needs to be changed for this table.:+1:

---
### Table 6: pizza_toppings
| Original table | Data Type |
| :---: | :---: |
| <img width="180" alt="c2_t6" src="https://user-images.githubusercontent.com/122411152/213343537-0a5e63e7-d3ec-4bad-a334-2063449a4581.png"> | <img width="151" alt="t6_d" src="https://user-images.githubusercontent.com/122411152/213343580-63d85cae-184b-4d41-9d95-fad1109cfa6b.png"> |

Nothing needs to be changed for this table.:+1:

[Back to Case Study #2 homepage](https://github.com/Manyu-Ku/8_Week_SQL_Challenge/tree/main/Case_study%232)
