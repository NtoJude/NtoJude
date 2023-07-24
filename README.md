-- CREATE SCHEMA dannys_diner;

 USE dannys_diner;

CREATE TABLE sales (
  customer_id VARCHAR(1),
  order_date DATE,
  product_id INTEGER
);

INSERT INTO sales
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
 
 CREATE TABLE menu (
  product_id INTEGER,
  product_name VARCHAR(5),
  price INTEGER
);

INSERT INTO menu
  (product_id, product_name, price)
VALUES
  ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');
  
  CREATE TABLE members (
    customer_id VARCHAR(1),
    join_date DATE
);

INSERT INTO members
  (customer_id, join_date)
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
  
 

SELECT* FROM members;
SELECT* FROM menu;
SELECT* FROM sales;
 -- 1. What is the total amount each customer spent at the restaurant?
 SELECT s.customer_id, SUM(mn.price) AS total_spent
 FROM sales s
 INNER JOIN menu mn
 ON s.product_id = mn.product_id
 GROUP BY s.customer_id
 ORDER BY s.customer_id;
 
-- Alternatively
select customer_id, sum(price) as total_amount
from sales
left join menu
on sales.product_id = menu.product_id
group by customer_id;
 
-- Alternatively
SELECT s.customer_id, mn.price AS total_amount
   FROM sales s
   INNER JOIN menu mn
   ON s.product_id = mn.product_id
   ORDER BY customer_id;
   
   -- ALTERNATIVELY
   SELECT customer_id, SUM(price) AS total_Amount_Spent
FROM sales
JOIN menu
USING (product_id)
GROUP BY customer_id;

      ##ALTERNATIVELY
SELECT customer_id, SUM(price) AS Amount_Spent
FROM sales
RIGHT JOIN menu
USING (product_id)
GROUP BY customer_id
ORDER BY customer_id;      
 
-- 2. How many days has each customer visited the restaurant?
SELECT customer_id, COUNT(DISTINCT order_date) AS tot_days_visited
   FROM sales
   GROUP BY customer_id
   ORDER BY customer_id;
   
 -- Alternatively
  SELECT customer_id, order_date, COUNT(order_date) AS visit_date
FROM sales
GROUP BY customer_id, order_date
ORDER BY order_date;

      -- ALTERNATIVELY
SELECT customer_id, COUNT(DISTINCT (order_date)) AS visited_dates
FROM sales
GROUP BY customer_id, order_date
ORDER BY order_date;

			##ALTERNATIVELY
SELECT customer_id, COUNT(order_date) AS Num_of_days_Visited
FROM sales
GROUP BY customer_id
ORDER BY Num_of_days_Visited DESC;            

-- 3. What was the first item from the menu purchased by each customer?
select s.*, m.product_name
from sales s
inner join (
  select customer_id, min(order_date) as min_order_date
  from sales s
  group by customer_id
) as t on s.customer_id = s.customer_id and s.order_date = t.min_order_date
inner join menu m on m.product_id = s.product_id;

-- Alternatively
select customer_id, product_name, order_date
from sales
left join menu
on sales.product_id = menu.product_id
where order_date = '2021-01-01'
order by customer_id;

       -- ALTERNATIVELY
SELECT customer_id, product_name AS first_items_purchased, order_date
FROM sales
JOIN menu
USING (product_id)
WHERE order_date < '2021-01-02'
ORDER BY customer_id;


-- Alternatively
select s.customer_id, m.product_name
from sales s
inner join (
  select customer_id, min(order_date) as min_order_date
  from sales s
  group by customer_id
) as t on s.customer_id = s.customer_id and s.order_date = t.min_order_date
inner join menu m on m.product_id = s.product_id;

-- Alternatively
SELECT customer_id , product_name, order_date
FROM sales
LEFT JOIN menu 
  ON sales.product_id = menu.product_id
WHERE order_date = '2021-01-01' 
ORDER BY customer_id;

         ##ALTERNATIVELY
SELECT customer_id, product_name AS first_item_Purchased, order_date
FROM sales
RIGHT JOIN menu
USING (product_id)
WHERE order_date = '2021-01-01)'
ORDER BY customer_id;
         
-- 4. What is the most purchased item on the menu and how many times was it purchased by all customers?
Select product_name, Count(product_name) as time_purchased
from sales
left join menu
on sales.product_id = menu.product_id
group by product_name 
order by time_purchased desc
limit 1;

        -- ALTERNATIVELY
SELECT product_name, COUNT(product_name) AS most_purchased_item
FROM sales
JOIN menu
USING (product_id)
GROUP BY product_name
ORDER BY most_purchased_item;

          --  ALTERNATIVELY     
SELECT product_name, COUNT(product_name) AS most_purchased_item
FROM sales
JOIN menu
USING (product_id)
GROUP BY product_name
ORDER BY most_purchased_item DESC
LIMIT 1;          

             ##ALTERNATIVELY
SELECT product_name, COUNT(product_name) AS most_purchased_item
FROM sales
RIGHT JOIN menu
ON sales.product_id = menu.product_id
GROUP BY product_name;

        
-- 5. Which item was the most popular for each customer?
select customer_id, product_name, count(product_name) as times_purchased
from sales
left join menu
on sales.product_id = menu.product_id
group by customer_id, product_name
order by times_purchased desc;

       --  ALTERNATIVELY
SELECT customer_id, product_name, COUNT(product_name) AS most_popular
FROM sales
JOIN menu
USING (product_id)
GROUP BY customer_id, product_name
ORDER BY most_popular  ASC;       

     ## ALTERNATIVELY
SELECT customer_id, product_name, COUNT(product_name) AS num_of_times_purchased
FROM sales
JOIN menu
USING (product_id)
GROUP BY customer_id, product_name
ORDER BY num_of_times_purchased;

-- 6. Which item was purchased first by the customer after they became a member?
select customer_id, order_date, product_name
from sales
inner join menu
on sales.product_id = menu.product_id
where customer_id = 'A'
And order_date > '2021-01-07' -- dates after membership
order by order_date
limit 1;
       -- ALTERNATIVELY FOR CUSTOMER A
SELECT customer_id, product_name, order_date
FROM sales
JOIN members
USING (customer_id)
JOIN menu
USING (product_id)
WHERE customer_id = 'A' AND order_date > '2021-01-07'
ORDER BY customer_id
LIMIT 1;       

         --  CUSTOMER B
select customer_id, product_name, order_date
from sales
inner join menu
on sales.product_id = menu.product_id
where customer_id = 'B'
and order_date > '2021-01-09' -- dates after membership
order by order_date desc
limit 1;

     -- ALTERNATIVELY FOR CUSTOMER B
SELECT customer_id, product_name, order_date
FROM sales
JOIN members
USING (customer_id)
JOIN menu 
USING (product_id)
WHERE customer_id = 'B' AND order_date > '2021-01-09'
ORDER BY customer_id
LIMIT 1;

         ##ALTERNATIVELY
SELECT customer_id, product_name, order_date
FROM sales
RIGHT JOIN menu
USING (product_id)
WHERE customer_id = 'B'
AND order_date < '2021-01-09'
ORDER BY customer_id
LIMIT 1;
         
-- 7. Which item was purchased just before the customer became a member?
        -- FOR CUSTOMER A
select customer_id, product_name, order_date
from sales 
left join menu
on sales.product_id = menu.product_id
where customer_id = 'A'
and order_date < '2021-01-10' -- get dates before membership
order by order_date
limit 1;

        --   ALTERNATIVELY
        SELECT customer_id, product_name, order_date
FROM sales
JOIN menu
USING (product_id)
WHERE customer_id = 'A' AND order_date <'2021-01-07'
ORDER BY customer_id;

           --  FOR CUSTOMER B
select customer_id, product_name, order_date
from sales
left join menu
on sales.product_id = menu.product_id
where customer_id = 'B' 
and order_date < '2021-01-09' -- get dates before membership
order by order_date desc
limit 1;

             -- ALTERNATIVELY FOR CUSTOMER B
SELECT customer_id, product_name, order_date
FROM sales
JOIN menu
USING (product_id)
WHERE customer_id = 'B' AND order_date < '2021-01-09'
ORDER BY customer_id
LIMIT 1;
                 
-- 8. What is the total items and amount spent for each member before they became a member?

      -- FOR CUSTOMER A
select customer_id, order_date, count(product_name) as total_items, SUM(PRICE) as total_amount_spent
from sales
left join menu
on sales.product_id = menu.product_id
where customer_id = 'A'
and order_date < '2021-01-07'
group by customer_id, order_date
order by order_date;

      --      ALTERNATIVELY FOR A
SELECT customer_id, COUNT(product_name) AS total_items, SUM(price) AS total_amount_spent
FROM sales
JOIN menu
USING (product_id)
WHERE  Customer_id = 'A' AND order_date < '2021-01-07'
GROUP BY customer_id;

-- for customer B
select customer_id, order_date, count(product_name) as total_items, SUM(PRICE) as total_amount_spent
from sales
left join menu
on sales.product_id = menu.product_id
where customer_id = 'B'
and order_date < '2021-01-09'
group by customer_id, order_date
order by order_date;

          --  ALTERNATIVELY FOR B
SELECT customer_id, COUNT(product_name) AS total_items, SUM(price) AS total_amount_spent
FROM sales
JOIN menu
USING (product_id)
WHERE  Customer_id = 'B' AND order_date < '2021-01-09'
GROUP BY customer_id; 

-- 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
select customer_id,
sum(case
    when product_name = 'sushi' then 20*price
    else 10*price
    end) as total_items
    from sales
    left join menu
    on sales.product_id = menu.product_id
    group by customer_id;
    
                      --  ALTERNATIVELY
SELECT customer_id, 
    SUM(
    CASE
    WHEN product_name = 'sushi' THEN 20 * price
    ELSE 10 * price
    END) total_points
    FROM sales
    JOIN menu
    USING (product_id)
    GROUP BY customer_id;
    
-- 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
WITH cte_OfferValidity AS 
    (SELECT s.customer_id, m.join_date, s.order_date,
        date_add(m.join_date, interval(6) DAY) firstweek_ends, menu.product_name, menu.price
    FROM sales s
    LEFT JOIN members m
      ON s.customer_id = m.customer_id
    LEFT JOIN menu
        ON s.product_id = menu.product_id)
SELECT customer_id,
    SUM(CASE
            WHEN order_date BETWEEN join_date AND firstweek_ends THEN 20 * price 
            WHEN (order_date NOT BETWEEN join_date AND firstweek_ends) AND product_name = 'sushi' THEN 20 * price
            ELSE 10 * price
        END) points
FROM cte_OfferValidity
WHERE order_date < '2021-02-01' -- filter jan points only
GROUP BY customer_id;

           ## ALTERNATIVELY
WITH cte_OfferValidity AS
       (SELECT s.customer_id, m.join_date, s.order_date,
       date_add(m.join_date, INTERVAL(6) DAY) firstweek_ends, menu.product_name, menu.price
       FROM sales s
       JOIN members m
       USING (customer_id)
       JOIN menu
       USING (product_id))
       SELECT customer_id,
       SUM(CASE
       WHEN order_date BETWEEN join_date AND firstweek_ends THEN 20 * price
       WHEN (order_date NOT BETWEEN join_date AND firstweek_ends)
       AND product_name = 'sushi' THEN 20 * price
       ELSE 10 * price
       END
       ) Total_Point
       FROM cte_OfferValidity
       WHERE order_date < '2021-02-01'
       GROUP BY customer_id
       ORDER BY customer_id;           
