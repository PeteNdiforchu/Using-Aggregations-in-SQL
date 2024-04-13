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


# SQL Queries - Data Quality Checks

```sql
1. **Check for Missing Values in Customer Table:**
SELECT *
FROM customer
WHERE first_name IS NULL OR last_name IS NULL;

----

2. **Identify Duplicate Films in Film Table:**
SELECT title, COUNT(*)
FROM film
GROUP BY title
HAVING COUNT(*) > 1;

----
3. **Validate Rental Dates in Rental Table:**
SELECT *
FROM rental
WHERE rental_date > return_date;

---
4. **Ensure Consistent Category Names in Film Category Table::**
SELECT DISTINCT name
FROM category
WHERE name NOT IN (
    SELECT DISTINCT name
    FROM film_category
    INNER JOIN category ON film_category.category_id = category.category_id
);

----
5. **Check for Inconsistent Customer Addresses in Customer Table:**
SELECT address_id, COUNT(*)
FROM customer
GROUP BY address_id
HAVING COUNT(*) > 1;

----
6. **Identify Films with No Inventory in Inventory Table:**
SELECT film_id
FROM film
WHERE film_id NOT IN (
    SELECT DISTINCT film_id
    FROM inventory
);

----
7. **Validate Payment Amounts in Payment Table:**
SELECT *
FROM payment
WHERE amount <= 0;

----
8. **Check for Missing Staff Records in Staff Table:**
SELECT *
FROM staff
WHERE staff_id NOT IN (SELECT DISTINCT staff_id FROM rental);

----
9. **Ensure Proper Address Formatting in Address Table:**
SELECT *
FROM address
WHERE LENGTH(city) < 3

----
10. **Identify Customers with Incomplete Address Information:**
SELECT customer_id, 
    CASE 
        WHEN address_id IS NULL THEN 'Missing Address'
        WHEN city IS NULL OR LENGTH(city) < 3 THEN 'Incomplete City'
        WHEN country_id IS NULL THEN 'Missing Country'
        ELSE 'Complete Address'
    END AS address_status
FROM customer;

----
11. **Categorize Films by Length:**
SELECT title, 
    CASE 
        WHEN length < 60 THEN 'Short'
        WHEN length >= 60 AND length < 120 THEN 'Medium'
        ELSE 'Long'
    END AS length_category
FROM film;

----
12. **Classify Payments by Amount Ranges:**
SELECT payment_id, amount,
    CASE 
        WHEN amount < 10 THEN 'Low'
        WHEN amount >= 10 AND amount < 50 THEN 'Medium'
        ELSE 'High'
    END AS payment_category
FROM payment;

----
13. **Identify Popular Film Genres:**
SELECT fc.category_id, c.name AS category_name,
    COUNT(*) AS film_count,
    CASE 
        WHEN COUNT(*) >= 100 THEN 'Popular'
        WHEN COUNT(*) >= 50 AND COUNT(*) < 100 THEN 'Moderately Popular'
        ELSE 'Less Popular'
    END AS popularity_status
FROM film_category fc
JOIN category c ON fc.category_id = c.category_id
GROUP BY fc.category_id, c.name;

----
14. **Detect Customers with High Rental Frequency:**
SELECT customer_id,
    CASE 
        WHEN COUNT(*) >= 50 THEN 'High Frequency'
        WHEN COUNT(*) >= 20 AND COUNT(*) < 50 THEN 'Moderate Frequency'
        ELSE 'Low Frequency'
    END AS rental_frequency
FROM rental
GROUP BY customer_id;

----
15. **Categorize Films by Rating:**
SELECT film_id, title, rating,
    CASE 
        WHEN rating = 'G' THEN 'General Audience'
        WHEN rating = 'PG' THEN 'Parental Guidance'
        WHEN rating = 'PG-13' THEN 'Parental Guidance 13+'
        WHEN rating = 'R' THEN 'Restricted'
        ELSE 'Not Rated'
    END AS rating_category
FROM film;



