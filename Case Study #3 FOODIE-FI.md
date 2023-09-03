# Introduction

Subscription based businesses are super popular and Danny realised that there was a large gap in the market - he wanted to create a new streaming service that only had food related content - something like Netflix but with only cooking shows!

Danny finds a few smart friends to launch his new startup Foodie-Fi in 2020 and started selling monthly and annual subscriptions, giving their customers unlimited on-demand access to exclusive food videos from around the world!

Danny created Foodie-Fi with a data driven mindset and wanted to ensure all future investment decisions and new features were decided using data. This case study focuses on using subscription style digital data to answer important business questions.

## Available Data

Danny has shared the data design for Foodie-Fi and also short descriptions on each of the database tables - our case study focuses on only 2 tables but there will be a challenge to create a new table for the Foodie-Fi team.

All datasets exist within the foodie_fi database schema - be sure to include this reference within your SQL scripts as you start exploring the data and answering the case study questions.

## Entity Relationship Diagram

![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/c44fc91d-9a3d-4e2b-a5d6-2d76a222f1a4)

### Table 1: plans

Customers can choose which plans to join Foodie-Fi when they first sign up.

Basic plan customers have limited access and can only stream their videos and is only available monthly at $9.90

Pro plan customers have no watch time limits and are able to download videos for offline viewing. Pro plans start at $19.90 a month or $199 for an annual subscription.

Customers can sign up to an initial 7 day free trial will automatically continue with the pro monthly subscription plan unless they cancel, downgrade to basic or upgrade to an annual pro plan at any point during the trial.

When customers cancel their Foodie-Fi service - they will have a churn plan record with a null price but their plan will continue until the end of the billing period.

![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/2ab4bba1-ee6d-4324-ab69-40e8cf9401d7)

### Table 2: subscriptions

Customer subscriptions show the exact date where their specific plan_id starts.

If customers downgrade from a pro plan or cancel their subscription - the higher plan will remain in place until the period is over - the start_date in the subscriptions table will reflect the date that the actual plan changes.

When customers upgrade their account from a basic plan to a pro or annual pro plan - the higher plan will take effect straightaway.

When customers churn - they will keep their access until the end of their current billing period but the start_date will be technically the day they decided to cancel their service.



## Case Study Questions

### 1.How many customers has Foodie-Fi ever had?
 
       Select count(distinct(customer_id))
             as n from subscription;

### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/be107163-bf8b-44b6-9cec-7e8df60a558f) 

### 2.What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value

        Select MONTH(start_date) as Month,count(plan_id) as n from subscription
        where plan_id=0
        group by month 
        order by month ASC;

### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/f347c727-8c4e-4044-93c6-26a4a0a11491)

### 3.What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
        Select plans.plan_name, count(plans.plan_id) as n
        from plans right join subscription on plans.plan_id=subscription.plan_id
        where year(start_date)>2020
        group by plan_name;
### output
![image](https://github.com/Sadiya-Zubair/8-week-sql-challenge/assets/36756199/55eb60ee-0d63-4845-9fad-5eab192feec5)

### 4.What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
        Select 
             count(customer_id)  as n_count,
             round(count(customer_id)/(select count(distinct(customer_id),1) from subscription)*100,1) as Percentage
        from plans right join subscription on plans.plan_id=subscription.plan_id
        where plan_name="churn"
        group by plan_name;
### output
![image](https://github.com/Sadiya-Zubair/8-week-sql-challenge/assets/36756199/9cfe6b5d-fb18-4bf0-a46c-23b71d429773)

### 5.How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?       
        with churned_customers as (
               select *, Row_number() over (partition by customer_id order by plan_id) as row_num
               from subscription
           )
       select 
             count(customer_id) as n_counts,
	            round(count(customer_id)/(select count(distinct(customer_id))
       from subscription)*100) as Percentage from churned_customers 
       where row_num=2 and plan_id=4;
### output
![image](https://github.com/Sadiya-Zubair/8-week-sql-challenge/assets/36756199/46acf952-d7b6-4ca4-b54f-c8b5355a9060)

### 6.What is the number and percentage of customer plans after their initial free trial?

           with churned_customers as (
           select *, Row_number() over (partition by customer_id order by plan_id) as row_num
           from subscription
          )
          select 
            plan_id,count(customer_id) as n_counts,
	           round(count(customer_id)/(select count(distinct(customer_id))
         from subscription)*100) as Percentage from churned_customers 
         where row_num=2 
         group by plan_id;
### output
![image](https://github.com/Sadiya-Zubair/8-week-sql-challenge/assets/36756199/12437944-1bdc-4bf9-86a9-26c6c3160a22)

### 7.What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?

         select plan_id,count(customer_id) as customer_count,
                round(count(customer_id)/(select count(distinct(customer_id))
         from subscription)*100) as Percentage from subscription 
         group by plan_id;
 ### output
 ![image](https://github.com/Sadiya-Zubair/8-week-sql-challenge/assets/36756199/a7b2f3a9-f025-4e6b-9cbc-a393d2ae3e0c)

### 8.How many customers have upgraded to an annual plan in 2020?
          
           select count(customer_id) as customer_count 
                  from plans as p join subscription as s on p.plan_id=s.plan_id
           where plan_name="pro annual";
### output
![image](https://github.com/Sadiya-Zubair/8-week-sql-challenge/assets/36756199/6df34485-2cbb-4449-b8a4-5cd8c904e459)

### 9.How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
           
            with t1 as(select customer_id, start_date from subscription where plan_id=0),
                 t2 as (select customer_id, start_date from subscription where plan_id=3)
            select
                 round(avg(datediff(t2.start_date,t1.start_date))) as avg_no_of_days 
            from t1 join t2 on t1.customer_id=t2.customer_id;
### output
![image](https://github.com/Sadiya-Zubair/8-week-sql-challenge/assets/36756199/dc62157e-973b-443e-94be-e9de3c4c38a5)

       


        




