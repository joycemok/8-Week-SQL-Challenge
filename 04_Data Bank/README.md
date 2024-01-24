# Case Study #4: Data Bank
<img src = "https://github.com/joycemok/8-Week-SQL-Challenge/assets/107952129/682bd586-499a-44fc-b215-59a6e638a82e" width = 550, height = 550)>

## Business Goal
Data Bank runs just like any other digital bank - but it isn’t only for banking activities, they also have the world’s most secure distributed data storage platform!

Customers are allocated cloud data storage limits which are directly linked to how much money they have in their accounts. There are a few interesting caveats that go with this business model, and this is where the Data Bank team need your help!

The management team at Data Bank want to increase their total customer base - but also need some help tracking just how much data storage their customers will need.

## Entity Relationship Diagram
![data_bank_erd](https://github.com/joycemok/8-Week-SQL-Challenge/assets/107952129/c50c1ba1-4dcd-46d8-9284-1dca077d0c9c)

# Case Study Questions

## A - Customer Nodes Exploration
### 1. How many unique nodes are there on the Data Bank system?
```SQL
SELECT COUNT(DISTINCT node_id)
FROM data_bank.customer_nodes;
```

| count |
| ----- |
| 5     |

---
### 2. What is the number of nodes per region?
```SQL
SELECT regions.region_id, regions.region_name, COUNT(DISTINCT cn.node_id)
FROM data_bank.customer_nodes AS cn
LEFT JOIN data_bank.regions ON regions.region_id = cn.region_id
GROUP BY regions.region_id, regions.region_name;
```

| region_id | region_name | count |
| --------- | ----------- | ----- |
| 1         | Australia   | 5     |
| 2         | America     | 5     |
| 3         | Africa      | 5     |
| 4         | Asia        | 5     |
| 5         | Europe      | 5     |

---
### 3. How many customers are allocated to each region?
```SQL
    SELECT regions.region_id, regions.region_name, COUNT(cn.customer_id) 
    FROM data_bank.customer_nodes AS cn
    LEFT JOIN data_bank.regions ON regions.region_id = cn.region_id
    GROUP BY regions.region_id, regions.region_name;
```

| region_id | region_name | count |
| --------- | ----------- | ----- |
| 1         | Australia   | 770   |
| 2         | America     | 735   |
| 5         | Europe      | 616   |
| 4         | Asia        | 665   |
| 3         | Africa      | 714   |

---
### 4. How many days on average are customers reallocated to a different node?
```SQL
    SELECT AVG(cn.end_date - cn.start_date) AS avg_reallocation_in_days
    FROM data_bank.customer_nodes AS cn;
```

| avg_reallocation_in_days |
| ------------------------ |
| 416373.411714285714      |

When I found the average days for realloaction, 41,6373 days was the result which seemed rather large, thus I checked for outliers in the dates. I found that there was a date of 12/31/9999.

```SQL
SELECT MAX(end_date), MIN(start_date)
FROM data_bank.customer_nodes;
```

| max                      | min                      |
| ------------------------ | ------------------------ |
| 9999-12-31T00:00:00.000Z | 2020-01-01T00:00:00.000Z |

So, to calculate the average properly, I removed the irregular date.

```SQL
SELECT ROUND(AVG(cn.end_date - cn.start_date),2) AS avg_reallocation_in_days
FROM data_bank.customer_nodes AS cn
WHERE end_date != '9999-12-31';
```

| avg_reallocation_in_days |
| ------------------------ |
| 14.63                    |

---
### 5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region? 
```SQL
WITH rd_cte AS (
  SELECT cn.region_id, regions.region_name, (cn.end_date- cn.start_date) AS reallocation_days 
  FROM data_bank.customer_nodes AS cn
  INNER JOIN data_bank.regions ON regions.region_id = cn.region_id
  WHERE end_date != '9999-12-31'
)
SELECT region_name,
PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY reallocation_days) AS median_days,
PERCENTILE_CONT(0.8) WITHIN GROUP (ORDER BY reallocation_days) AS percentile_80_days,
PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY reallocation_days) AS percentile_95_days
FROM rd_cte
GROUP BY region_name;
```

| region_name | median_days | percentile_80_days | percentile_95_days |
| ----------- | ----------- | ------------------ | ------------------ |
| Africa      | 15          | 24                 | 28                 |
| America     | 15          | 23                 | 28                 |
| Asia        | 15          | 23                 | 28                 |
| Australia   | 15          | 23                 | 28                 |
| Europe      | 15          | 24                 | 28                 |

---

## B - Customer Nodes Exploration
### 1. What is the unique count and total amount for each transaction type?
```SQL
SELECT DISTINCT ct.txn_type, COUNT(ct.txn_type) AS unique_count,
SUM(ct.txn_amount)
FROM data_bank.customer_transactions AS ct
GROUP BY ct.txn_type;
```

| txn_type   | unique_count | sum     |
| ---------- | ------------ | ------- |
| withdrawal | 1580         | 793003  |
| purchase   | 1617         | 806537  |
| deposit    | 2671         | 1359168 |

---
### 2. What is the average total historical deposit counts and amounts for all customers?
```SQL
    WITH deposits_cte AS (
      SELECT customer_id, COUNT(txn_type) AS total_count, SUM(txn_amount) AS total_deposit
      FROM data_bank.customer_transactions AS ct
      WHERE txn_type = 'deposit'
      GROUP BY customer_id
    )
    SELECT ROUND(AVG(total_count)) AS avg_count, ROUND(AVG(total_deposit), 2) AS avg_total_deposit
    FROM deposits_cte;
```

| avg_count | avg_total_deposit |
| --------- | ----------------- |
| 5         | 2718.34           |

---

### 3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?
```SQL
WITH transactions AS (
SELECT
TO_CHAR(txn_date, 'month') AS months,
customer_id,
SUM(CASE WHEN txn_type = 'deposit' THEN 1 ELSE 0 END) as deposits,
SUM(CASE WHEN txn_type <> 'deposit' THEN 1 ELSE 0 END) AS withdrawals_and_purchases
FROM data_bank.customer_transactions
GROUP BY customer_id, TO_CHAR(txn_date, 'month')
HAVING SUM(CASE WHEN txn_type = 'deposit' THEN 1 ELSE 0 END) > 1
AND SUM(CASE WHEN txn_type <> 'deposit' THEN 1 ELSE 0 END) > 1
)
SELECT
months,
COUNT(customer_id) AS customers
FROM transactions
GROUP BY months;
```

| months   | customers |
| -------- | --------- |
| april    | 48        |
| february | 145       |
| january  | 115       |
| march    | 154       |

---

### 4. What is the closing balance for each customer at the end of the month?
```SQL
SELECT customer_id, TO_CHAR(txn_date, 'month') AS months,
SUM(
  CASE 
  	WHEN txn_type = 'deposit' THEN txn_amount 
  	WHEN txn_type = 'withdrawl' THEN -txn_amount
  	WHEN txn_type = 'purchase' THEN - txn_amount
  	ELSE 0
  END
) AS total_balance
FROM data_bank.customer_transactions
GROUP BY customer_id, TO_CHAR(txn_date, 'month')
ORDER BY customer_id ASC, TO_CHAR(txn_date, 'month') ASC;
```

This is the first 3 customers on the list as the full table is too long to display. 

| customer_id | months    | total_balance |
| ----------- | --------- | ------------- |
| 1	          | january   | 312           |
| 1	          | march	  |-952           |
| 2	          | january   | 549           |
| 2	          | march     | 61            |
| 3	          | april     | 493           |
| 3	          | february  | -965          |
| 3           |	january   |	144           |
| 3	          | march     |	0             |

---
