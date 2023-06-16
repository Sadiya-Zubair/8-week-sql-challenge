# Introduction
Did you know that over 115 million kilograms of pizza is consumed daily worldwide??? (Well according to Wikipedia anyway…)

Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

## Available Data
Because Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.

He has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.

All datasets exist within the pizza_runner database schema - be sure to include this reference within your SQL scripts as you start exploring the data and answering the case study questions.
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/b4ef45b4-de70-45c3-aed8-d39b3e2ec7b8)
### Table 1: runners
The runners table shows the registration_date for each new runner

### Table 2: customer_orders
Customer pizza orders are captured in the customer_orders table with 1 row for each individual pizza that is part of the order.

The pizza_id relates to the type of pizza which was ordered whilst the exclusions are the ingredient_id values which should be removed from the pizza and the extras are the ingredient_id values which need to be added to the pizza.

Note that customers can order multiple pizzas in a single order with varying exclusions and extras values even if the pizza is the same type!

The exclusions and extras columns will need to be cleaned up before using them in your queries.
### Table 3: runner_orders
After each orders are received through the system - they are assigned to a runner - however not all orders are fully completed and can be cancelled by the restaurant or the customer.

The pickup_time is the timestamp at which the runner arrives at the Pizza Runner headquarters to pick up the freshly cooked pizzas. The distance and duration fields are related to how far and long the runner had to travel to deliver the order to the respective customer.

There are some known data issues with this table so be careful when using this in your queries - make sure to check the data types for each column in the schema SQL!

### Table 4: pizza_names
At the moment - Pizza Runner only has 2 pizzas available the Meat Lovers or Vegetarian!
### Table 5: pizza_recipes
Each pizza_id has a standard set of toppings which are used as part of the pizza recipe.
### Table 6: pizza_toppings
This table contains all of the topping_name values with their corresponding topping_id value.
## Case Study Questions

This case study has LOTS of questions - they are broken up by area of focus including:

1. Pizza Metrics
2. Runner and Customer Experience
3. Ingredient Optimisation
4. Pricing and Ratings
5. Bonus DML Challenges (DML = Data Manipulation Language)

Each of the following case study questions can be answered using a single SQL statement.
##  Data Cleaning

     SELECT * FROM customer_orders;
### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/aceec8da-62c6-4eac-a84e-8affe96efc33)

In customer_orders table, we see a lot of empty spaces and null vaues in the columns exclusions and extras

    DROP TABLE IF EXISTS new_customer_orders;

    CREATE TEMPORARY TABLE new_customer_orders AS (
                                                  SELECT order_id, customer_id, pizza_id,

                                                   CASE 
                                                       
                                                       WHEN exclusions='' or exclusions like 'null' THEN null
                                                    
                                                       ELSE exclusions END AS exclusions,
                                                    
                                                    CASE
                                                        WHEN extras='' or extras like'null' THEN null
                                                       
                                                        ELSE extras END AS extras,order_time FROM customer_orders);
     
     SELECT * FROM new_customer_orders;

### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/a358c602-f57c-4964-9ff2-322071e99c96)

Now, for the table runner_orders

       DROP TABLE IF EXISTS new_runner_orders;
      
       CREATE TEMPORARY TABLE new_runner_orders as (SELECT order_id,runner_id,CASE WHEN pickup_time="" or pickup_time like "null" 
                                                
                                                 then null end as pickup_time,,
                                                 
                                                 NULLIF(regexp_replace(distance,"[^0-9.]",""),"") as distance,
										
                                                 NULLIF(regexp_replace(duration,"[^0-9.]",""),"") as duration,
                                             
                                                 CASE WHEN cancellation LIKE 'null' OR cancellation LIKE 'NaN' OR cancellation LIKE ''                                                   
                                                 THEN NULL ELSE cancellation END AS cancellation FROM runner_orders);
      
      ALTER TABLE new_runner_orders
       
       MODIFY COLUMN pickuP_time timestamp,
       
       MODIFY COLUMN distance NUMERIC,
       
       MODIFY COLUMN duration NUMERIC;

      SELECT * FROM new_runner_orders;  
### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/05be9cb7-a529-4963-b5c1-862ed2b635a5)

      
      
 

