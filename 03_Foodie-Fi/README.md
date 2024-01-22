# Case Study #3: Foodie-Fi
<img src = "https://github.com/joycemok/8-Week-SQL-Challenge/assets/107952129/05d3f533-ff3f-4fb6-a2b4-e83000c2e7ff" width = 550, height = 550>

## Business Goal
Subscription based businesses are super popular and Danny realised that there was a large gap in the market - he wanted to create a new streaming service that only had food related content - something like Netflix but with only cooking shows!

Danny finds a few smart friends to launch his new startup Foodie-Fi in 2020 and started selling monthly and annual subscriptions, giving their customers unlimited on-demand access to exclusive food videos from around the world! He created Foodie-Fi with a data driven mindset and wanted to ensure all future investment decisions and new features were decided using data. 

## Entity Relationship Diagram
![foodie-fi_erd](https://github.com/joycemok/8-Week-SQL-Challenge/assets/107952129/40acdd62-1c22-459f-83c8-72f19341faa5)

```plan``` table:

| plan_id | plan_name     | price  |
| ------- | ------------- | ------ |
| 0       | trial         | 0      |
| 1       | basic monthly | 9.90   |
| 2       | pro monthly   | 19.90  |
| 3       | pro annual    | 199.00 |
| 4       | churn         | null   |

Plans: 
- **Trial** - Customers can sign up to an initial 7 day free trial will automatically continue with the pro monthly subscription plan unless they cancel, downgrade to basic or upgrade to an annual pro plan at any point during the trial
- **Basic Plan** - customers have limited access and can only stream their videos and is only available monthly at $9.90
- **Pro Plan** - Pro plan customers have no watch time limits and are able to download videos for offline viewing.
  - **Pro Monthly** - start at $19.90 a month
  - **Pro Annual** - $199 for an annual subscription
- **Churn** - When customers cancel their Foodie-Fi service - they will have a churn plan record with a null price but their plan will continue until the end of the billing period.

```subscriptions``` table:

| customer_id | plan_id | start_date               |
| ----------- | ------- | ------------------------ |
| 1           | 0       | 2020-08-01T00:00:00.000Z |
| 1           | 1       | 2020-08-08T00:00:00.000Z |
| 2           | 0       | 2020-09-20T00:00:00.000Z |
| 2           | 3       | 2020-09-27T00:00:00.000Z |
| 11          | 0       | 2020-11-19T00:00:00.000Z |
| 11          | 4       | 2020-11-26T00:00:00.000Z |
| 13          | 0       | 2020-12-15T00:00:00.000Z |
| 13          | 1       | 2020-12-22T00:00:00.000Z |
| 13          | 2       | 2021-03-29T00:00:00.000Z |
| 15          | 0       | 2020-03-17T00:00:00.000Z |
| 15          | 2       | 2020-03-24T00:00:00.000Z |
| 15          | 4       | 2020-04-29T00:00:00.000Z |
| 16          | 0       | 2020-05-31T00:00:00.000Z |
| 16          | 1       | 2020-06-07T00:00:00.000Z |
| 16          | 3       | 2020-10-21T00:00:00.000Z |
| 18          | 0       | 2020-07-06T00:00:00.000Z |
| 18          | 2       | 2020-07-13T00:00:00.000Z |
| 19          | 0       | 2020-06-22T00:00:00.000Z |
| 19          | 2       | 2020-06-29T00:00:00.000Z |
| 19          | 3       | 2020-08-29T00:00:00.000Z |

## A - Customer Journey
### Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.

This is the ```subscriptions``` table with the 8 sample customers selected:    

| customer_id | plan_id | start_date               |
| ----------- | ------- | ------------------------ |
| 1           | 0       | 2020-08-01T00:00:00.000Z |
| 1           | 1       | 2020-08-08T00:00:00.000Z |
| 2           | 0       | 2020-09-20T00:00:00.000Z |
| 2           | 3       | 2020-09-27T00:00:00.000Z |
| 11          | 0       | 2020-11-19T00:00:00.000Z |
| 11          | 4       | 2020-11-26T00:00:00.000Z |
| 13          | 0       | 2020-12-15T00:00:00.000Z |
| 13          | 1       | 2020-12-22T00:00:00.000Z |
| 13          | 2       | 2021-03-29T00:00:00.000Z |
| 15          | 0       | 2020-03-17T00:00:00.000Z |
| 15          | 2       | 2020-03-24T00:00:00.000Z |
| 15          | 4       | 2020-04-29T00:00:00.000Z |
| 16          | 0       | 2020-05-31T00:00:00.000Z |
| 16          | 1       | 2020-06-07T00:00:00.000Z |
| 16          | 3       | 2020-10-21T00:00:00.000Z |
| 18          | 0       | 2020-07-06T00:00:00.000Z |
| 18          | 2       | 2020-07-13T00:00:00.000Z |
| 19          | 0       | 2020-06-22T00:00:00.000Z |
| 19          | 2       | 2020-06-29T00:00:00.000Z |
| 19          | 3       | 2020-08-29T00:00:00.000Z |

We are going to join the ```subscription``` table and the  ```plan``` table with the 8 customers selected:

```SQL
SELECT sub.customer_id, plans.plan_id, plans.plan_name, sub.start_date
FROM foodie_fi.plans 
INNER JOIN foodie_fi.subscriptions AS sub
  ON plans.plan_id = sub.plan_id
WHERE sub.customer_id IN (1,2,11,13,15,16,18,19)
ORDER BY sub.customer_id ASC, plans.plan_id ASC;
```

| customer_id | plan_id | plan_name     | start_date               |
| ----------- | ------- | ------------- | ------------------------ |
| 1           | 0       | trial         | 2020-08-01T00:00:00.000Z |
| 1           | 1       | basic monthly | 2020-08-08T00:00:00.000Z |
| 2           | 0       | trial         | 2020-09-20T00:00:00.000Z |
| 2           | 3       | pro annual    | 2020-09-27T00:00:00.000Z |
| 11          | 0       | trial         | 2020-11-19T00:00:00.000Z |
| 11          | 4       | churn         | 2020-11-26T00:00:00.000Z |
| 13          | 0       | trial         | 2020-12-15T00:00:00.000Z |
| 13          | 1       | basic monthly | 2020-12-22T00:00:00.000Z |
| 13          | 2       | pro monthly   | 2021-03-29T00:00:00.000Z |
| 15          | 0       | trial         | 2020-03-17T00:00:00.000Z |
| 15          | 2       | pro monthly   | 2020-03-24T00:00:00.000Z |
| 15          | 4       | churn         | 2020-04-29T00:00:00.000Z |
| 16          | 0       | trial         | 2020-05-31T00:00:00.000Z |
| 16          | 1       | basic monthly | 2020-06-07T00:00:00.000Z |
| 16          | 3       | pro annual    | 2020-10-21T00:00:00.000Z |
| 18          | 0       | trial         | 2020-07-06T00:00:00.000Z |
| 18          | 2       | pro monthly   | 2020-07-13T00:00:00.000Z |
| 19          | 0       | trial         | 2020-06-22T00:00:00.000Z |
| 19          | 2       | pro monthly   | 2020-06-29T00:00:00.000Z |
| 19          | 3       | pro annual    | 2020-08-29T00:00:00.000Z |

Let's take a look at a few of the customers subscription journeys at Foodie-Fi:

**Customer 1:**

| customer_id | plan_id | plan_name     | start_date               |
| ----------- | ------- | ------------- | ------------------------ |
| 1           | 0       | trial         | 2020-08-01T00:00:00.000Z |
| 1           | 1       | basic monthly | 2020-08-08T00:00:00.000Z |

Customer 1 started on a trial plan on 08/01/2020 and downgraded to a basic monthly plan on 08/08/2020 after the end of their 7 day free trial.

**Customer 15:**

| customer_id | plan_id | plan_name     | start_date               |
| ----------- | ------- | ------------- | ------------------------ |
| 15          | 0       | trial         | 2020-03-17T00:00:00.000Z |
| 15          | 2       | pro monthly   | 2020-03-24T00:00:00.000Z |
| 15          | 4       | churn         | 2020-04-29T00:00:00.000Z |

Customer 15 started a trial plan on 03/17/2020 and subscribed to a pro-monthly plan on 03/24/2020 after the end of their 7 day free trial. On 04/29/2020, about a month later, this customer terminated their plan and churned until the end of their subscription.

**Customer 16:**
| customer_id | plan_id | plan_name     | start_date               |
| ----------- | ------- | ------------- | ------------------------ |
| 16          | 0       | trial         | 2020-05-31T00:00:00.000Z |
| 16          | 1       | basic monthly | 2020-06-07T00:00:00.000Z |
| 16          | 3       | pro annual    | 2020-10-21T00:00:00.000Z |

Customer 16 started a trial plan on 05/31/2020 and downgraded to a basic monthly plan on 06/07/2020 after the end of their 7 day free trial. On 10/21/2020, about 4 months later, the customer upgraded to a pro-annual plan.

---

## B - Data Analysis Questions

### 1. How many customers has Foodie-Fi ever had?
```SQL
SELECT COUNT(DISTINCT customer_id) AS number_of_customers
FROM foodie_fi.subscriptions;
```
|number_of_customers|
| ----------------- |
|1000               |

### 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value?
```SQL
SELECT EXTRACT(MONTH FROM sub.start_date) AS month_num, 
TO_CHAR(start_date, 'month') AS month_name, COUNT(customer_id) AS customer_count
FROM foodie_fi.subscriptions AS sub
WHERE sub.plan_id = 0 
GROUP BY month_name, month_num
ORDER BY customer_count DESC;
```

| month_num | month_name | customer_count |
| --------- | ---------- | -------------- |
| 3         | march      | 94             |
| 7         | july       | 89             |
| 8         | august     | 88             |
| 5         | may        | 88             |
| 1         | january    | 88             |
| 9         | september  | 87             |
| 12        | december   | 84             |
| 4         | april      | 81             |
| 6         | june       | 79             |
| 10        | october    | 79             |
| 11        | november   | 75             |
| 2         | february   | 68             |

---

### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name?
```SQL
SELECT sub.plan_id, plans.plan_name, COUNT(sub.plan_id) AS plans_started
FROM foodie_fi.subscriptions AS sub
INNER JOIN foodie_fi.plans ON sub.plan_id = plans.plan_id
WHERE sub.start_date > '2020-12-31'
GROUP BY sub.plan_id, plans.plan_name
ORDER BY sub.plan_id ASC;
```

| plan_id | plan_name     | plans_started |
| ------- | ------------- | ------------- |
| 1       | basic monthly | 8             |
| 2       | pro monthly   | 60            |
| 3       | pro annual    | 63            |
| 4       | churn         | 71            |

---

### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
```SQL
    SELECT COUNT(customer_id) AS customer_churn_count, 
    ROUND(100.0 * COUNT(customer_id) / (SELECT COUNT(DISTINCT customer_id) 
                                            FROM foodie_fi.subscriptions), 1) AS percent_churned
    FROM foodie_fi.subscriptions
    WHERE plan_id = 4;
```

| customer_churn_count | percent_churned |
| -------------------- | --------------- |
| 307                  | 30.7            |

---
### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
```SQL
WITH cte_plan_rank AS (
  SELECT *, RANK() OVER(PARTITION BY sub.customer_id ORDER BY sub.start_date ASC) AS plan_rank
  FROM foodie_fi.subscriptions AS sub
)
SELECT COUNT(
  CASE 
      WHEN plan_rank = 2 AND plan_id = 4 THEN 1
    END
) AS churn_after_trial_count, 
ROUND ((100.0 * COUNT(
  CASE 
      WHEN plan_rank = 2 AND plan_id = 4 THEN 1
    END
) / COUNT(DISTINCT customer_id)), 0) AS percent_churn_after_trial
FROM cte_plan_rank;
```

| churn_after_trial_count | percent_churn_after_trial |
| ----------------------- | ------------------------- |
| 92                      | 9                         |

---

### 6. What is the number and percentage of customer plans after their initial free trial?
```SQL
WITH plan_rank AS (
  SELECT sub.customer_id, plans.plan_id, plans.plan_name, RANK() OVER(PARTITION BY sub.customer_id ORDER BY sub.start_date) AS plan_rank
  FROM foodie_fi.subscriptions AS sub
    INNER JOIN foodie_fi.plans ON sub.plan_id = plans.plan_id
    
)
SELECT plan_id, plan_name, COUNT(plan_id) AS plans_converted, ROUND(100.0 * COUNT(plan_id)::numeric/(SELECT COUNT(plan_id) FROM plan_rank WHERE plan_rank = 2), 2) AS plan_conversion_percentage
FROM plan_rank
WHERE plan_rank = 2
GROUP BY plan_id, plan_name;
```

| plan_id | plan_name     | plans_converted | plan_conversion_percentage |
| ------- | ------------- | --------------- | -------------------------- |
| 1       | basic monthly | 546             | 54.60                      |
| 2       | pro monthly   | 325             | 32.50                      |
| 3       | pro annual    | 37              | 3.70                       |
| 4       | churn         | 92              | 9.20                       |

---

### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
```SQL
WITH ranking AS (
  SELECT sub.customer_id, sub.plan_id, sub.start_date, plans.plan_name, RANK() OVER(PARTITION BY sub.customer_id ORDER BY sub.start_date DESC) AS plan_rank 
  FROM foodie_fi.subscriptions AS sub
  INNER JOIN foodie_fi.plans ON sub.plan_id = plans.plan_id
  WHERE start_date <= '2020-12-31'
)
SELECT plan_name, COUNT(plan_id) AS customer_count, ROUND(100.0 * COUNT(plan_id)/(SELECT COUNT(plan_id) FROM ranking WHERE plan_rank = 1), 2) AS customer_percentage
FROM ranking
WHERE plan_rank = 1
GROUP BY plan_name;
```

| plan_name     | customer_count | customer_percentage |
| ------------- | -------------- | ------------------- |
| basic monthly | 224            | 22.40               |
| churn         | 236            | 23.60               |
| pro annual    | 195            | 19.50               |
| pro monthly   | 326            | 32.60               |
| trial         | 19             | 1.90                |

---

### 8. How many customers have upgraded to an annual plan in 2020?
```SQL
    SELECT COUNT(DISTINCT sub.customer_id) as annual_plan_count
    FROM foodie_fi.subscriptions AS sub
    WHERE (sub.start_date BETWEEN '2020-01-01' AND '2020-12-31') AND sub.plan_id = 3;
```

| annual_plan_count |
| ----------------- |
| 195               |

---

### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
```SQL
SELECT ROUND(AVG(s2.start_date - s1.start_date)) AS average_days
FROM foodie_fi.subscriptions AS s1
INNER JOIN foodie_fi.subscriptions AS s2
ON s1.customer_id = s2.customer_id
  AND s1.plan_id + 3 = s2.plan_id
WHERE s2.plan_id = 3;
```

| average_days |
| ------------ |
| 105          |

---

### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
```SQL
WITH duration_table AS (
  SELECT s2.start_date - s1.start_date AS duration,
  WIDTH_BUCKET(s2.start_date - s1.start_date, 1, 360, 12) AS bin
  FROM foodie_fi.subscriptions s1
  INNER JOIN foodie_fi.subscriptions s2
  ON s1.customer_id = s2.customer_id
   AND s1.plan_id +  3 = s2.plan_id
  WHERE s2.plan_id = 3
  ORDER BY duration 
 )
 SELECT CONCAT((bin-1)*30+1,' - ',bin*30,' days') AS intervals, 
  ROUND(AVG(duration)) AS avg_days,
  COUNT(bin) AS customer_count
 FROM duration_table
 GROUP BY bin;
```

| intervals      | avg_days | customer_count |
| -------------- | -------- | -------------- |
| 1 - 30 days    | 10       | 49             |
| 31 - 60 days   | 42       | 24             |
| 61 - 90 days   | 71       | 34             |
| 91 - 120 days  | 101      | 35             |
| 121 - 150 days | 133      | 42             |
| 151 - 180 days | 162      | 36             |
| 181 - 210 days | 191      | 26             |
| 211 - 240 days | 224      | 4              |
| 241 - 270 days | 257      | 5              |
| 271 - 300 days | 285      | 1              |
| 301 - 330 days | 327      | 1              |
| 331 - 360 days | 346      | 1              |

---

### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
```SQL
    WITH ranked AS (
      SELECT sub.customer_id, plans.plan_id, plans.plan_name, 
      LEAD(plans.plan_id) OVER (PARTITION BY sub.customer_id ORDER BY sub.start_date) AS next_plan
      FROM foodie_fi.subscriptions AS sub
      INNER JOIN foodie_fi.plans 
      	ON sub.plan_id = plans.plan_id
     WHERE sub.start_date BETWEEN '2020-01-01' AND '2020-12-31'
    )
    SELECT COUNT(customer_id) AS customer_downgrade_count
    FROM ranked
    WHERE plan_id = 2 AND next_plan = 1;
```

| customer_downgrade_count |
| ------------------------ |
| 0                        |

---
