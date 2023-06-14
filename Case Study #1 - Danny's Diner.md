## Introduction
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

## Problem Statement
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

Danny has shared with you 3 key datasets for this case study:

1. sales
2. menu
3. members
You can inspect the entity relationship diagram and example data below.

## Entity Relationship Diagram
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/566af03b-cddd-402a-abac-120c36826927)

## Case Study Questions And Answers

####  1 /* What is the total amount each customer spent at the restaurant? */

select sales.customer_id, sum(menu.price) 

from menu inner join sales  on menu.product_id=sales.product_id

group by customer_id;

#### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/59c8b996-3f7c-4503-b343-41b99cf081f1)

#### 2 /* How many days has each customer visited the restaurant? */

select customer_id, count(distinct(order_date)) as no_days from sales 

group by customer_id

order by no_days DESC;

#### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/1cc0a0ad-376b-40a9-a334-b853442c65d8)
#### 3/* What was the first item from the menu purchased by each customer? */

With first_order as (select s.customer_id,m.product_name,

row_number() OVER(partition by s.customer_id 

order by s.order_date,s.product_id ) as n

from sales as s join menu as m on m.product_id=s.product_id)

select customer_id,product_name from first_order

where n=1;

#### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/8e687e81-87ef-4273-a155-912eee8e0996)
#### 4/* What is the most purchased item on the menu and how many times was it purchased by all customers?*/

select m.product_name,count(s.product_id) as n_purchased from menu as m 

join sales as son m.product_id=s.product_id

group by m.product_name

order by n_purchased DESC

limit 1;

#### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/5eda05fd-4f73-4568-81ce-b15a98f13782)
#### 5/* Which item was the most popular for each customer?*/

with Most_popular as (select s.customer_id,m.product_name,

Rank() over (partition by s.Customer_id 

order by count(m.product_name) DESC

) as r from menu as m join sales as s

on m.product_id=s.product_id

group by s.customer_id,m.product_name)

select customer_id,product_name,r from Most_popular

where r=1;
#### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/e0bca9e2-c5b4-49a6-94c4-3746ffd67d1f)

#### 6 /* Which item was purchased first by the customer after they became a member?*/

with item_after_membership as(select mem.customer_id as customer, m.product_name as product ,

rank() over(partition by mem.customer_id

order by s.order_date ) as r

from members as mem  join sales as s on mem.customer_id=s.customer_id

join  menu as m on m.product_id=s.product_id

where mem.join_date>=s.order_date)

select customer,product,r from item_after_membership

where r=1 ;

#### output
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/b58e21d2-ff7a-4771-b15b-a1ec3e0e1045)
#### 7/* Which item was purchased just before the customer became a member?*/

with item_before_membership as(select mem.customer_id as customer, m.product_name as product ,

rank() over(partition by mem.customer_id

order by s.order_date DESC ) as r

from members as mem join sales as s on s.customer_id=mem.customer_id

join menu as m on s.product_id=m.product_id

where mem.join_date<s.order_date)

select distinct(customer),product,r from item_before_membership

where r=1 ;
#### output 
![image](https://github.com/dreamersz/8-week-sql-challenge/assets/36756199/a9858d8c-969b-440a-90ae-605cdfe043d2)

 
 
 

 



