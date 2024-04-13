# Using Aggregations in SQL - LinkedIn Live Guidelines

# Gitpod Setup

We will be using Gitpod which is a cloud development environment which allows developers to quickly setup and access pre-configured and customized computing environments with a few clicks.

For this LinkedIn Live we would be using a Gitpod environment pre-configured by Danny Ma for a couple of his SQL Courses. Huge thanks to Danny for making this available to the public.

Here is the link to the gitpod SQL Pad environment to follow with the session:

https://gitpod.io/#https://github.com/datawithdanny/serious-sql-environment

# Step-By-Step Instructions

## Step 1: Sign into Gitpot

You can sign into to Gitpot using Github or LinkedIn

![image](https://github.com/PeteNdiforchu/SQL_LinkedInLive/assets/157251680/247bb46d-f514-4293-92c1-f25d4bda917a)

![image](https://github.com/PeteNdiforchu/SQL_LinkedInLive/assets/157251680/a12dd391-9732-43f6-9687-92ed93ed178a)


## Step 2: 

Select the compute environment: *serious_sql_environment

![image](https://github.com/PeteNdiforchu/SQL_LinkedInLive/assets/157251680/a6a7d32b-cc56-46d1-a7d3-ad327ec42c92)

## Step 3: Initialize the Database

Click the link in the pop-up on your browser to take you to the SQL pad editor.

![image](https://github.com/PeteNdiforchu/SQL_LinkedInLive/assets/157251680/76590296-b9bf-4eee-8f26-fc7a4276a457)


## Step 4: Get ready to query the data

Now you are all set to start querying the data and writing your data quality checks

![image](https://github.com/PeteNdiforchu/SQL_LinkedInLive/assets/157251680/19ed6611-9b28-44bb-a191-18a15d21cc81)


# SQL Queries - Aggregations

```sql
-- 1. Calculate the total amount paid for each category:

SELECT c.name as category_name,
       SUM(p.amount) AS total_amount_paid
FROM dvd_rentals.category c
JOIN dvd_rentals.film_category fc ON c.category_id = fc.category_id
JOIN dvd_rentals.film f ON fc.film_id = f.film_id
JOIN dvd_rentals.inventory i ON f.film_id = i.film_id
JOIN dvd_rentals.rental r ON i.inventory_id = r.inventory_id
JOIN dvd_rentals.payment p ON r.rental_id = p.rental_id
GROUP BY c.name;

-- 2. Calculate the total amount paid for each category, and include a column indicating whether the total amount is above 5000:

SELECT c.name as category_name,
       SUM(p.amount) AS total_amount_paid,
       CASE WHEN SUM(p.amount) > 5000 THEN 'Above 5000' ELSE 'Below or Equal to 5000' END AS amount_status
FROM dvd_rentals.category c
JOIN dvd_rentals.film_category fc ON c.category_id = fc.category_id
JOIN dvd_rentals.film f ON fc.film_id = f.film_id
JOIN dvd_rentals.inventory i ON f.film_id = i.film_id
JOIN dvd_rentals.rental r ON i.inventory_id = r.inventory_id
JOIN dvd_rentals.payment p ON r.rental_id = p.rental_id
GROUP BY c.name;

-- 3. Calculate the total amount paid for each category, only including categories with a total amount paid greater than 1000:

SELECT c.name as category_name,
       SUM(p.amount) AS total_amount_paid
FROM dvd_rentals.category c
JOIN dvd_rentals.film_category fc ON c.category_id = fc.category_id
JOIN dvd_rentals.film f ON fc.film_id = f.film_id
JOIN dvd_rentals.inventory i ON f.film_id = i.film_id
JOIN dvd_rentals.rental r ON i.inventory_id = r.inventory_id
JOIN dvd_rentals.payment p ON r.rental_id = p.rental_id
GROUP BY c.name
HAVING SUM(p.amount) > 1000;

-- 4. Calculate the average amount paid for each category:

SELECT c.name as category_name,
       AVG(p.amount) AS average_amount_paid
FROM dvd_rentals.category c
JOIN dvd_rentals.film_category fc ON c.category_id = fc.category_id
JOIN dvd_rentals.film f ON fc.film_id = f.film_id
JOIN dvd_rentals.inventory i ON f.film_id = i.film_id
JOIN dvd_rentals.rental r ON i.inventory_id = r.inventory_id
JOIN dvd_rentals.payment p ON r.rental_id = p.rental_id
GROUP BY c.name;

-- 5. Calculate the total amount paid for each customer:

SELECT CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
       SUM(p.amount) AS total_amount_paid
FROM dvd_rentals.customer c
JOIN dvd_rentals.payment p ON c.customer_id = p.customer_id
GROUP BY CONCAT(c.first_name, ' ', c.last_name);

-- 6. Calculate the total amount paid for each customer, only including customers with more than 5 transactions:

SELECT CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
       COUNT(*) AS transaction_count,
       SUM(p.amount) AS total_amount_paid
FROM dvd_rentals.customer c
JOIN dvd_rentals.payment p ON c.customer_id = p.customer_id
GROUP BY CONCAT(c.first_name, ' ', c.last_name)
HAVING COUNT(*) > 5;

-- 7. Calculate the total amount paid for each customer, including the percentage of the total amount they represent:

SELECT CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
       SUM(p.amount) AS total_amount_paid,
       (SUM(p.amount) / (SELECT SUM(amount) FROM dvd_rentals.payment)) * 100 AS percentage_of_total
FROM dvd_rentals.customer c
JOIN dvd_rentals.payment p ON c.customer_id = p.customer_id
GROUP BY CONCAT(c.first_name, ' ', c.last_name);

-- 8. Calculate the total amount paid for each month, year-wise:

SELECT EXTRACT(YEAR FROM p.payment_date) AS year,
       EXTRACT(MONTH FROM p.payment_date) AS month,
       SUM(p.amount) AS total_amount_paid
FROM dvd_rentals.payment p
GROUP BY EXTRACT(YEAR FROM p.payment_date), EXTRACT(MONTH FROM p.payment_date);

-- 9. Calculate the total amount paid for each year, including the percentage change from the previous year:

-- SELECT EXTRACT(YEAR FROM p.payment_date) AS year,
--       SUM(p.amount) AS total_amount_paid,
--       (SUM(p.amount) - LAG(SUM(p.amount), 1, 0) OVER (ORDER BY EXTRACT(YEAR FROM p.payment_date))) / LAG(SUM(p.amount), 1, 0) OVER (ORDER BY EXTRACT(YEAR FROM p.payment_date)) * 100 AS percentage_change
-- FROM dvd_rentals.payment p
-- GROUP BY EXTRACT(YEAR FROM p.payment_date);

-- 10. Calculate the total amount paid for each category, but only including payments that occurred after a specific date:

SELECT c.name as category_name,
       SUM(p.amount) AS total_amount_paid
FROM dvd_rentals.category c
JOIN dvd_rentals.film_category fc ON c.category_id = fc.category_id
JOIN dvd_rentals.film f ON fc.film_id = f.film_id
JOIN dvd_rentals.inventory i ON f.film_id = i.film_id
JOIN dvd_rentals.rental r ON i.inventory_id = r.inventory_id
JOIN dvd_rentals.payment p ON r.rental_id = p.rental_id
WHERE p.payment_date > '2005-01-01'
GROUP BY c.name;


