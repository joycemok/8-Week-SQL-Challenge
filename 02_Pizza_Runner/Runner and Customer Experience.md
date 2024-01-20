# Case Study #2: Pizza Runner Solutions 
## B - Runner and Customer Experience

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

```SQL
SELECT runners.runner_id, EXTRACT(WEEK FROM runners.registration_date) AS week
FROM pizza_runner.runners
GROUP BY runners.runner_id, week
ORDER BY runners.runner_id;
```

| runner_id | week |
| --------- | ---- |
| 1         | 53   |
| 2         | 53   |
| 3         | 1    |
| 4         | 2    |

You can see from the above output that the first 3 days in January were part of the last week, thus we will need to offset this to make sure that the week starts on January 1st. 

```SQL
SELECT runners.runner_id, EXTRACT(WEEK FROM runners.registration_date + 3) AS week
FROM pizza_runner.runners
GROUP BY runners.runner_id, week
ORDER BY runners.runner_id;
```

| runner_id | week |
| --------- | ---- |
| 1         | 1    |
| 2         | 1    |
| 3         | 2    |
| 4         | 3    |

- For week 1 two runners (1 and 2) signed up
- For week 2 only runner 3 signed up
- For week 3 only runner 4 signed up

---

### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?

```SQL
WITH order_time_diff AS (
 SELECT DISTINCT co.order_id, EXTRACT(EPOCH FROM(ro.pickup_time - co.order_time))/60 AS time_diff 
FROM customer_orders_temp AS co
INNER JOIN runner_orders_temp AS ro 
  ON co.order_id = ro.order_id
WHERE ro.distance IS NOT NULL
 )
SELECT ROUND(AVG(time_diff)::numeric, 2) AS avg_time
FROM order_time_diff;
```

| avg_time |
| -------- |
| 15.98    |

---

### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?

```SQL
WITH orders AS (
SELECT co.order_id, COUNT(co.order_id) AS pizza_count, EXTRACT(EPOCH FROM (ro.pickup_time -co.order_time)) / 60 AS time_diff 
FROM customer_orders_temp AS co
INNER JOIN runner_orders_temp AS ro 
  ON co.order_id = ro.order_id
WHERE ro.distance IS NOT NULL
GROUP BY co.order_id, ro.pickup_time, co.order_time
)
SELECT pizza_count, ROUND(AVG(time_diff)::numeric, 2) AS avg_prep_time
FROM orders
GROUP BY pizza_count;
```

| pizza_count | avg_prep_time |
| ----------- | ------------- |
| 3           | 29.28         |
| 2           | 18.38         |
| 1           | 12.36         |

We can see that when the number of pizzas in an order is higher, then the prep time is longer.

---
### 4. What was the average distance travelled for each customer?

```SQL
SELECT co.customer_id, ROUND(AVG(CAST(ro.distance AS float))::numeric, 2) AS avg_distance
FROM customer_orders_temp AS co
INNER JOIN runner_orders_temp AS ro
  ON co.order_id = ro.order_id
WHERE ro.distance IS NOT NULL
GROUP BY co.customer_id
ORDER BY avg_distance DESC;
```

| customer_id | avg_distance |
| ----------- | ------------ |
| 105         | 25.00        |
| 103         | 23.40        |
| 101         | 20.00        |
| 102         | 16.73        |
| 104         | 10.00        |

---
### 5. What was the difference between the longest and shortest delivery times for all orders?

```SQL
SELECT MAX(duration) - MIN(duration) AS delivery_time_diff
FROM runner_orders_temp
WHERE duration IS NOT NULL;
```

| delivery_time_diff |
| ------------------ |
| 30                 |

---
### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?

```SQL
SELECT order_id, runner_id, ROUND(AVG(CAST(distance AS FLOAT) / (CAST(duration AS FLOAT)/60))::numeric, 2) AS avg_speed
FROM runner_orders_temp
WHERE distance IS NOT NULL
GROUP BY order_id, runner_id
ORDER BY runner_id ASC, avg_speed DESC;
```

| order_id | runner_id | avg_speed |
| -------- | --------- | --------- |
| 10       | 1         | 60.00     |
| 2        | 1         | 44.44     |
| 3        | 1         | 40.20     |
| 1        | 1         | 37.50     |
| 8        | 2         | 93.60     |
| 7        | 2         | 60.00     |
| 4        | 2         | 35.10     |
| 5        | 3         | 40.00     |

Runner 2 seems to have an outlier for one of deliveries which had an average speed of 93.60 km/hour. This should be looked into further as Runner 2 has a large variance between their lowest and highest average speed. 

---
### 7. What is the successful delivery percentage for each runner?
```SQL
SELECT runner_id, 
ROUND(100 * SUM(
  CASE WHEN distance IS NULL THEN 0 
  ELSE 1 END) / COUNT(*), 2) AS success_perc
FROM runner_orders_temp
GROUP BY runner_id
ORDER BY runner_id ASC;
```

| runner_id | success_perc |
| --------- | ------------ |
| 1         | 100.00       |
| 2         | 75.00        |
| 3         | 50.00        |

---
