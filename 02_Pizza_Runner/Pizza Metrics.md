# Case Study #2: Pizza Runners Solutions 
## A - Pizza Metrics 
### 1. How many pizzas were ordered?

```SQL
SELECT COUNT(order_id) AS Pizza_Count
FROM customer_orders_temp;
```

| pizza_count |
| ----------- |
| 14          |

---
### 2.How many unique customer orders were made?

```SQL
SELECT COUNT(DISTINCT order_id) AS Unique_Orders
FROM customer_orders_temp;
```

| unique_orders |
| ------------- |
| 10            |

---
### 3. How many successful orders were delivered by each runner?

```SQL
SELECT runner_id, COUNT(distance)
FROM runner_orders_temp
WHERE distance IS NOT NULL
GROUP BY runner_id
ORDER BY runner_id ASC;
```

| runner_id | count |
| --------- | ----- |
| 1         | 4     |
| 2         | 3     |
| 3         | 1     |

---
### 4. How many of each type of pizza was delivered?

```SQL
SELECT co.pizza_id, pizza_names.pizza_name, COUNT(co.pizza_id)
FROM customer_orders_temp AS co
INNER JOIN runner_orders_temp AS ro
  ON co.order_id = ro.order_id
LEFT JOIN pizza_runner.pizza_names
  ON co.pizza_id = pizza_names.pizza_id
WHERE ro.distance IS NOT NULL
GROUP BY co.pizza_id, pizza_names.pizza_name
ORDER BY co.pizza_id;
```

| pizza_id | pizza_name | count |
| -------- | ---------- | ----- |
| 1        | Meatlovers | 9     |
| 2        | Vegetarian | 3     |

---
### 5. How many Vegetarian and Meatlovers were ordered by each customer?

```SQL
SELECT co.customer_id, pizza_names.pizza_name, COUNT(co.pizza_id)
FROM customer_orders_temp AS co
LEFT JOIN pizza_runner.pizza_names
  ON co.pizza_id = pizza_names.pizza_id
GROUP BY co.customer_id, pizza_names.pizza_name
ORDER BY co.customer_id ASC;
```

| customer_id | pizza_name | count |
| ----------- | ---------- | ----- |
| 101         | Meatlovers | 2     |
| 101         | Vegetarian | 1     |
| 102         | Meatlovers | 2     |
| 102         | Vegetarian | 1     |
| 103         | Meatlovers | 3     |
| 103         | Vegetarian | 1     |
| 104         | Meatlovers | 3     |
| 105         | Vegetarian | 1     |

---
### 6. What was the maximum number of pizzas delivered in a single order?

```SQL
SELECT co.order_id, COUNT(co.pizza_id) AS pizza_count
FROM customer_orders_temp AS co
INNER JOIN runner_orders_temp AS ro
  ON co.order_id = ro.order_id
WHERE ro.distance IS NOT NULL
GROUP BY co.order_id
ORDER BY pizza_count DESC
LIMIT 1;
```

| order_id | pizza_count |
| -------- | ----------- |
| 4        | 3           |

---
### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```SQL
SELECT co.customer_id,
   COUNT(
     CASE 
       WHEN co.exclusions != '' OR co.extras != '' THEN 1
     END
   ) AS change,
   COUNT(
     CASE 
       WHEN co.exclusions = '' AND co.extras = '' THEN 1
     END
   ) AS no_change 
FROM customer_orders_temp AS co
INNER JOIN runner_orders_temp AS ro ON co.order_id = ro.order_id
WHERE ro.distance IS NOT NULL
GROUP BY co.customer_id
ORDER BY co.customer_id;
```

| customer_id | change | no_change |
| ----------- | ------ | --------- |
| 101         | 0      | 2         |
| 102         | 0      | 3         |
| 103         | 3      | 0         |
| 104         | 2      | 1         |
| 105         | 1      | 0         |

---
### 8. How many pizzas were delivered that had both exclusions and extras?

```SQL
SELECT COUNT(
  CASE 
      WHEN co.exclusions != '' AND co.extras != '' THEN 1
  END
) AS pizzas_w_exclusions_and_extras
FROM customer_orders_temp AS co
INNER JOIN runner_orders_temp AS ro ON co.order_id = ro.order_id
WHERE ro.distance IS NOT NULL;
```

| pizzas_w_exclusions_and_extras |
| ------------------------------ |
| 1                              |

---
### 9. What was the total volume of pizzas ordered for each hour of the day?

```SQL
SELECT EXTRACT(HOUR FROM co.order_time) AS hour_of_day, COUNT(co.pizza_id) AS pizza_count
FROM customer_orders_temp AS co
GROUP BY hour_of_day
ORDER BY hour_of_day;
```

| hour_of_day | pizza_count |
| ----------- | ----------- |
| 11          | 1           |
| 13          | 3           |
| 18          | 3           |
| 19          | 1           |
| 21          | 3           |
| 23          | 3           |

---
### 10. What was the volume of orders for each day of the week?

```SQL
SELECT TO_CHAR(co.order_time, 'day') AS day_of_week, COUNT(co.pizza_id) AS pizza_count
FROM customer_orders_temp AS co
GROUP BY day_of_week
ORDER BY day_of_week ASC;
```

| day_of_week | pizza_count |
| ----------- | ----------- |
| friday      | 1           |
| saturday    | 5           |
| thursday    | 3           |
| wednesday   | 5           |

---
