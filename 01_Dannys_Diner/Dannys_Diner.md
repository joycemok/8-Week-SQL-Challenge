
# Case Study #1: Danny's Diner
<img src = "https://github.com/joycemok/8-Week-SQL-Challenge/assets/107952129/3b4ee767-a7c9-4a99-8f0d-8f7f637bdc04" width = 550, height = 550>

## Business Goal
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

The full description of the case study is found [here](https://8weeksqlchallenge.com/case-study-1/).

## Entity Relationship Diagram
![SQL_Dannys_Diner_Diagram](https://github.com/joycemok/8-Week-SQL-Challenge/assets/107952129/9e957c85-6ce5-41e1-903a-92f7b358a0d4)

## Create Database
Schema (PostgreSQL v13)

``` SQL
    CREATE SCHEMA dannys_diner;
    SET search_path = dannys_diner;
    
    CREATE TABLE sales (
      "customer_id" VARCHAR(1),
      "order_date" DATE,
      "product_id" INTEGER
    );
    
    INSERT INTO sales
      ("customer_id", "order_date", "product_id")
    VALUES
      ('A', '2021-01-01', '1'),
      ('A', '2021-01-01', '2'),
      ('A', '2021-01-07', '2'),
      ('A', '2021-01-10', '3'),
      ('A', '2021-01-11', '3'),
      ('A', '2021-01-11', '3'),
      ('B', '2021-01-01', '2'),
      ('B', '2021-01-02', '2'),
      ('B', '2021-01-04', '1'),
      ('B', '2021-01-11', '1'),
      ('B', '2021-01-16', '3'),
      ('B', '2021-02-01', '3'),
      ('C', '2021-01-01', '3'),
      ('C', '2021-01-01', '3'),
      ('C', '2021-01-07', '3');
     
    
    CREATE TABLE menu (
      "product_id" INTEGER,
      "product_name" VARCHAR(5),
      "price" INTEGER
    );
    
    INSERT INTO menu
      ("product_id", "product_name", "price")
    VALUES
      ('1', 'sushi', '10'),
      ('2', 'curry', '15'),
      ('3', 'ramen', '12');
      
    
    CREATE TABLE members (
      "customer_id" VARCHAR(1),
      "join_date" DATE
    );
    
    INSERT INTO members
      ("customer_id", "join_date")
    VALUES
      ('A', '2021-01-07'),
      ('B', '2021-01-09');
```

---

## Case Study Questions

### 1. What is the total amount each customer spent at the restaurant?

**Process:**
- **JOIN** ```dannys_diner.sales``` with  ```dannys_diner.menu``` to get ```sales.customer_id``` from sales table and ```menu.price``` from menu table
- Use **SUM** to add item prices together
- Group the total prices by ```sales.customer_id``` to get amount each customer spent
  
``` SQL
    SELECT sales.customer_id, SUM(menu.price) as total_price
    FROM dannys_diner.sales
    LEFT JOIN dannys_diner.menu ON sales.product_id = menu.product_id
    GROUP BY sales.customer_id
    ORDER BY sales.customer_id;
```

**Answer:**

| customer_id | total_price |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

- Customer A spent a total of $76
- Customer B spent a total of $74
- Customer C spent a total of $36

---
### 2. How many days has each customer visited the restaurant?

**Process:**
- Notice that the question asks for days meaning if a customer visited multiple times a day it should be counted only once, so to account for this we can use **COUNT(DISTINCT order_date)** to avoid duplicate counts of visits on the same day
    
``` SQL
    SELECT sales.customer_id, COUNT(DISTINCT sales.order_date)
    FROM dannys_diner.sales
    GROUP BY sales.customer_id;
```

**Answer:**

| customer_id | count |
| ----------- | ----- |
| A           | 4     |
| B           | 6     |
| C           | 2     |

- Customer A visited 4 times
- Customer B visited 6 times
- Customer C visited 2 times

---
### 3. What was the first item from the menu purchased by each customer?

**Process:**
- Create a CTE (Common Table Expression) called ```cte_rank``` to order the items purchased by each customer by ```sales.order_date```
- Use **DENSE_RANK()** to rank the orders by ```sales.order_date```  to get ranking where duplicate order dates get equivalent rank values
- Use **PARTITION BY** ```sales.customer_id``` to create groupings for each customer allowing us to get the first item for each customer
- **SELECT** the entries where ```date_rank = 1``` for each customer

``` SQL
    WITH cte_rank AS (
    SELECT sales.customer_id, DENSE_RANK() OVER (PARTITION BY sales.customer_id ORDER BY sales.order_date) AS date_rank, menu.product_name, sales.order_date
    FROM dannys_diner.sales 
    LEFT JOIN dannys_diner.menu 
      ON sales.product_id = menu.product_id
    ORDER BY sales.customer_id ASC, date_rank ASC
    )
    SELECT customer_id, product_name
    FROM cte_rank
    WHERE date_rank = 1
    GROUP BY customer_id, product_name;
```

**Answer:**

| customer_id | product_name |
| ----------- | ------------ |
| A           | curry        |
| A           | sushi        |
| B           | curry        |
| C           | ramen        |

- Customer A purchased curry and sushi first
- Customer B purchased curry first
- Customer C purchased ramen first

---
### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

**Process:**
- **COUNT** ```menu.product_name``` and **GROUP BY** ```menu.product_name``` to get count of purchases for each item
- Use ```LIMIT 1``` to get most purchased item

``` SQL
    SELECT menu.product_name, COUNT(menu.product_name) AS total_sales
    FROM dannys_diner.sales
    LEFT JOIN dannys_diner.menu ON sales.product_id = menu.product_id
    GROUP BY menu.product_name
    ORDER BY total_sales DESC
    LIMIT 1;
```

**Answer:**

| product_name | total_sales |
| ------------ | ----------- |
| ramen        | 8           |

- Ramen was the most purchased item with 8 sales

---
### 5. Which item was the most popular for each customer?

**Process:***
- Create CTE to get the number of each item purchased by each customer, as the number of times an item is purchased will be our measure of 'popularity'
- Use **DENSE_RANK()** to get a rank of each item ```ORDER BY``` the number of times each item was purchased
- **PARTITION BY** ```sales.customer_id``` to rank how many times each item is purchased for each customer
- **SELECT** the most popular item for each customer

``` SQL
    WITH pop_items AS (
    SELECT sales.customer_id, menu.product_name, COUNT(menu.product_name) AS product_count, DENSE_RANK() OVER (PARTITION BY sales.customer_id ORDER BY COUNT(sales.customer_id) DESC) AS count_rank
    FROM dannys_diner.sales
    LEFT JOIN dannys_diner.menu ON sales.product_id = menu.product_id
    GROUP BY sales.customer_id, menu.product_name
    )
    SELECT customer_id, product_name, product_count
    FROM pop_items
    WHERE count_rank = 1
    ORDER BY customer_id ASC, product_count DESC;
```

**Answer:**

| customer_id | product_name | product_count |
| ----------- | ------------ | ------------- |
| A           | ramen        | 3             |
| B           | ramen        | 2             |
| B           | curry        | 2             |
| B           | sushi        | 2             |
| C           | ramen        | 3             |

- Ramen was the most popular item for Customer A
- Ramen, curry, and sushi were equally popular for Customer B
- Ramen was the most popular item for Custoemr C
  
---
### 6. Which item was purchased first by the customer after they became a member?

**Process:**
- Create CTE that selects purchases of each customer **ORDER BY** ```sales.order_date```
- Use **PARTITION BY** ```sales.customer_id``` to group customers and apply **DENSE_RANK()** to each customer partition to rank the order dates from first purchase to latest purchase
- Exclude orders purchased before each customer's membership date 

``` SQL
    WITH first_purchase AS (
    SELECT sales.customer_id, menu.product_name, sales.order_date, DENSE_RANK() OVER (PARTITION BY sales.customer_id ORDER BY sales.order_date) AS date_rank
    FROM dannys_diner.sales
    LEFT JOIN dannys_diner.menu ON sales.product_id = menu.product_id
    LEFT JOIN dannys_diner.members ON sales.customer_id = members.customer_id
    WHERE sales.order_date > members.join_date
    )
    SELECT customer_id, product_name
    FROM first_purchase
    WHERE date_rank = 1;
```

**Answer:**

| customer_id | product_name |
| ----------- | ------------ |
| A           | ramen        |
| B           | sushi        |

- The first order purchased by Customer A after becoming a member was ramen
- The first order purchased by Customer B after becoming a member was sushi

---
### 7. Which item was purchased just before the customer became a member?

**Process:**
- Create CTE that selects purchases of each customer **ORDER BY** ```sales.order_date``` latest to earliest
- Use **PARTITION BY** ```sales.customer_id``` to group customers and apply **DENSE_RANK()** to each customer partition to rank the order dates from first purchase to latest purchase
- Exclude dates after customers became a member

``` SQL
    WITH bf_membership AS (
    SELECT sales.customer_id, menu.product_name, sales.order_date, members.join_date, DENSE_RANK() OVER (PARTITION BY sales.customer_id ORDER BY sales.order_date DESC) AS date_rank
    FROM dannys_diner.sales
    LEFT JOIN dannys_diner.menu ON sales.product_id = menu.product_id
    LEFT JOIN dannys_diner.members ON sales.customer_id = members.customer_id
    WHERE sales.order_date < members.join_date
    )
    SELECT customer_id, product_name
    FROM bf_membership
    WHERE date_rank = 1;
```

**Answer:**

| customer_id | product_name |
| ----------- | ------------ |
| A           | sushi        |
| A           | curry        |
| B           | sushi        |

- Customer A purchased sushi and curry right before becoming a member
- Customer B purchased sushi right before becoming a member

---
### 8. What is the total items and amount spent for each member before they became a member?

**Process:**
- Only include purchases before customers became members ```sales.order_date < members.join_date```
- Use **COUNT(product_id)** to get total number of items purchased and **SUM(price)** to get total price of the items purchased
- **GROUP BY** ```sales.customer_id``` to get number of items purchased and total price for each customer

``` SQL
    SELECT sales.customer_id, COUNT(menu.product_id) AS item_count, SUM(menu.price) AS total_price
    FROM dannys_diner.sales
    LEFT JOIN dannys_diner.menu ON sales.product_id = menu.product_id
    INNER JOIN dannys_diner.members ON sales.customer_id = members.customer_id
    	AND sales.order_date < members.join_date
    GROUP BY sales.customer_id
    ORDER BY sales.customer_id ASC;
```

**Answer:**

| customer_id | item_count | total_price |
| ----------- | ---------- | ----------- |
| A           | 2          | 25          |
| B           | 3          | 40          |

- Customer A purchased 2 items for a total price of $25 before becoming a member
- Customer B purchased 3 items for a total price of $40 before becoming a member

---
### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

**Process:**
- For items that are not sushi **$1 = 10 points**
- For items that are sushi **$1 = 20 points**
- Using the conditional **CASE** statement:
  - **IF** product_id = 1 (sushi), multiply every $1 by 20 points.
  - **ELSE** multiply $1 by 10 points.
- Calculate the total points for each customer

``` SQL
    WITH order_points AS (
    SELECT sales.customer_id, 
    	CASE
        	WHEN menu.product_id = 1 THEN menu.price * 10 * 2
            ELSE menu.price * 10
        END AS points
    FROM dannys_diner.sales
    LEFT JOIN dannys_diner.menu ON sales.product_id = menu.product_id
    )
    SELECT customer_id, SUM(points)
    FROM order_points
    GROUP BY customer_id
    ORDER BY customer_id ASC;
```

**Answer:**

| customer_id | sum |
| ----------- | --- |
| A           | 860 |
| B           | 940 |
| C           | 360 |

- Customer A earned a total of 860 points
- Customer B earned a total of 940 points
- Customer C earned a total of 360 points

---
### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

**Process:**
- Create CTE that calculates when double points occur
- Use **CASE** statements with this logic:
  - **IF** order is within within a week of the member's join date then 2x points for all items
  - **IF** item is sushi then 2x points
  - **ELSE** $1 = 10 points
- Exclude dates after January 31st
- Calculate the total points for each customer

``` SQL
    WITH double_points AS (
    SELECT sales.customer_id, 
    	CASE
        	WHEN sales.order_date BETWEEN members.join_date AND members.join_date + 6 THEN menu.price * 10 * 2
      		WHEN sales.product_id = 1 THEN menu.price * 10 * 2
      		WHEN sales.order_date > '2021-01-31' THEN menu.price * 0
            ELSE menu.price * 10
        END AS points
    FROM dannys_diner.sales
    LEFT JOIN dannys_diner.menu ON sales.product_id = menu.product_id
    INNER JOIN dannys_diner.members ON sales.customer_id = members.customer_id
    )
    SELECT customer_id, SUM(points)
    FROM double_points
    GROUP BY customer_id
    ORDER BY customer_id ASC;
```

**Answer:**

| customer_id | sum  |
| ----------- | ---- |
| A           | 1370 |
| B           | 820  |

- Customer A earned a total of 1370 points
- Customer B earned a total of 820 points 

---
