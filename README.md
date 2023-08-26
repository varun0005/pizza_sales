# pizza_sales
create database pizza_salesaug23;

use pizza_salesaug23;

select * from pizzas;
select * from orders;
select * from pizza_types;
select * from order_details;

-- Creating a VIEW

CREATE VIEW pizza_details AS
SELECT p.pizza_id , p.pizza_type_id , pt.name , pt.category , p.size , p.price , pt.ingredients
FROM pizzas p 
JOIN pizza_types pt
ON p.pizza_type_id = pt.pizza_type_id; 

select * from pizza_details;

-- Changing the Data-Types

ALTER TABLE orders
MODIFY date DATE; 

ALTER TABLE orders
MODIFY time TIME;

DESCRIBE orders;

select * from order_details;
select * from pizza_details;
select * from orders;

-- TOTAL REVENUE
SELECT ROUND(SUM(od.quantity * p.price),2) AS total_revenue
FROM order_details od
JOIN pizza_details p 
ON od.pizza_id = p.pizza_id; 

-- TOTAL NO. OF PIZZAS SOLD
SELECT SUM(quantity) AS pizza_sold
FROM order_details;

-- TOTAL ORDERS
SELECT COUNT(DISTINCT(order_id)) AS total_orders
FROM order_details;

-- AVERAGE ORDER VALUE
SELECT SUM(od.quantity * p.price) / COUNT(DISTINCT(od.order_id)) AS avg_order_value
FROM order_details od
JOIN pizza_details p
ON od.pizza_id = p.pizza_id;

-- AVERAGE NO. OF PIZZA PER ORDER
SELECT SUM(od.quantity) /  COUNT(DISTINCT(od.order_id)) AS pizza_per_order
FROM order_details od;

-- TOTAL REVENUE AND NO. OF ORDERS PER CATEGORY
SELECT * FROM order_details;
SELECT * FROM pizza_details;

SELECT ROUND(SUM(od.quantity * p.price),2) as total_revenue , COUNT(DISTINCT(order_id)) AS total_orders , p.category
FROM order_details od
JOIN pizza_details p 
ON od.pizza_id = p.pizza_id
GROUP BY p.category;

-- TOTAL REVENUE AND NO.OF ORDERS PER SIZE
SELECT ROUND(SUM(od.quantity * p.price),2) AS total_revenue , COUNT(DISTINCT(od.order_id)) AS total_orders , p.size
FROM order_details od
JOIN pizza_details p 
ON od.pizza_id = p.pizza_id
GROUP BY p.size; 

-- HOURLY , MONTHLY , AND DAILY TREND OF ORDERS AND REVENUE OF PIZZA
SELECT * FROM pizza_details;
select * from order_details;
select * from orders;
-- HOURLY
SELECT 
     CASE 
         WHEN HOUR(o.time) BETWEEN 9 AND 12 THEN "LATE MORNING"
		 WHEN HOUR(o.time) BETWEEN 12 AND 15 THEN "LUNCH"
		 WHEN HOUR(o.time) BETWEEN 15 AND 18 THEN "MID AFTERNOON"
		 WHEN HOUR(o.time) BETWEEN 18 AND 21 THEN "DINNER"
		 WHEN HOUR(o.time) BETWEEN 21 AND 23 THEN "LATE NIGHT"
         ELSE "OTHERS"
         END AS meal_time , COUNT(DISTINCT(od.order_id)) AS total_orders
	FROM order_details od
    JOIN orders o 
    ON od.order_id = o.order_id
    GROUP BY meal_time
    ORDER BY meal_time DESC;
         
-- DAILY

SELECT DAYNAME(o.date) AS weekdays , COUNT(DISTINCT(od.order_id)) AS total_orders
FROM orders o 
JOIN order_details od
ON o.order_id = od.order_id
GROUP BY DAYNAME(o.date)
ORDER BY DAYNAME(o.date) DESC;

-- MONTHLY
SELECT 
    MONTHNAME(o.date) AS MONTH,
    COUNT(DISTINCT (od.order_id)) AS total_orders
FROM
    orders o
        JOIN
    order_details od ON o.order_id = od.order_id
GROUP BY MONTHNAME(o.date)
ORDER BY MONTHNAME(o.date)DESC;

-- MOST ORDERED PIZZA
SELECT p.name , p.size , SUM(od.order_id) AS total_orders
FROM pizza_details p 
JOIN order_details od 
ON p.pizza_id = od.pizza_id
GROUP BY p.name , p.size
ORDER BY SUM(od.order_id) DESC;

-- TOP 5 PIZZAS BY REVENUE
SELECT ROUND(SUM(od.quantity * p.price),2) AS revenue , p.name 
FROM order_details od 
JOIN pizza_details p 
ON od.pizza_id = p.pizza_id
GROUP BY p.name 
ORDER BY revenue DESC LIMIT 5;

-- TOP 5 PIZZAS BY SALE
SELECT * FROM order_details;
SELECT p.name , SUM(od.quantity) AS pizzas_sold
FROM order_details od 
JOIN pizza_details p 
ON od.pizza_id = p.pizza_id
GROUP BY p.name 
ORDER BY pizzas_sold DESC LIMIT 5;

-- PIZZA ANALYSIS

-- MIN AND MAX PRICE OF PIZZA
SELECT * FROM pizzas;
SELECT MIN(price) AS MIN_PRICE , MAX(price) AS MAX_PRICE 
FROM pizzas;

-- TOP USED INGREDIENTS
SELECT * FROM pizza_details;

SELECT ingredient , COUNT(ingredient) AS ingredient_count
FROM (
      SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(ingredients,',',n),',' , -1) as ingredient
      FROM order_details
      JOIN pizza_details ON
      pizza_details.pizza_id = order_details.pizza_id
      JOIN numbers ON CHAR_LENGTH(ingredients) - CHAR_LENGTH(REPLACE(ingredients,',','')) >= n-1
      ) AS subquery
GROUP BY ingredient
ORDER BY ingredient_count DESC;

CREATE TEMPORARY TABLE numbers as (
SELECT 1 AS N UNION ALL
SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL
SELECT 5 UNION ALL SELECT 6 UNION ALL SELECT 7 UNION ALL
SELECT 8 UNION ALL SELECT 9 UNION ALL SELECT 10
);

select * from pizza_details;
