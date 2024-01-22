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


### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
### 6. What is the number and percentage of customer plans after their initial free trial?
### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
### 8. How many customers have upgraded to an annual plan in 2020?
### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
### 11. How many customers downgraded from a pro monthly to a basic monthly plan in 2020?

