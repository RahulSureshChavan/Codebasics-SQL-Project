# Codebasics-SQL-Project

**Exercise 1:**  
```-- Print all movies in the order of their release year (latest first) --  
SELECT * FROM movies ORDER BY release_year DESC;  
-- All movies released in the year 2022 --  
SELECT * FROM movies WHERE release_year = 2022;  
-- Now all the movies released after 2020 --  
SELECT * FROM movies WHERE release_year > 2020;  
-- All movies after the year 2020 that have more than 8 rating --  
SELECT * FROM movies WHERE release_year > 2020 AND imdb_rating > 8;  
-- Select all movies that are by Marvel studios and Hombale Films --  
SELECT * FROM movies WHERE studio IN ("Marvel studios", "Hombale Films");  
-- Select all THOR movies by their release year --  
SELECT * FROM movies WHERE title LIKE "%Thor%" ORDER BY release_year DESC;  
-- Select all movies that are not from Marvel Studios --  
SELECT * FROM movies WHERE studio != "Marvel Studios";
```  

**Exercise 2:**  
```-- How many movies were released between 2015 and 2022 -- 
SELECT COUNT(*) AS movie_count FROM movies WHERE release_year BETWEEN 2015 AND 2022;
-- Print the max and min movie release year --
SELECT 
	MAX(release_year) AS max_release_year,
	MIN(release_year) AS min_release_year
FROM
	movies;
-- Print a year and how many movies were released in that year starting with the latest year --
SELECT
	release_year,
    COUNT(*) AS movie_count
FROM
	movies
GROUP BY
	release_year
ORDER BY
	release_year DESC
```

**Exercise 3:**  
```SELECT * FROM financials;
-- Print profit % for all the movies --
SELECT *,
    round(((revenue - budget) / revenue) * 100,1) AS Profit_percent
FROM financials;
```

**Exercise 4:**  
```-- Print all movie titles and release year for all Marvel Studios movies --
SELECT title, release_year FROM moviesdb.movies WHERE studio = "Marvel Studios";
-- Print all movies that have Avenger in their name --
SELECT title FROM moviesdb.movies WHERE title LIKE "%Avenger%";
-- Print the year when the movie "The Godfather" was released --
SELECT release_year FROM moviesdb.movies WHERE title = "The Godfather";
-- Print all distinct movie studios in the Bollywood industry --
SELECT DISTINCT studio FROM moviesdb.movies WHERE industry = "Bollywood";
```

**Exercise 5:**
```-- Show all the movies with their language names --
SELECT movie_id, title, release_year, m.language_id, name AS Language
FROM movies AS m
INNER JOIN languages AS l
ON m.language_id = l.language_id;
-- Show all Telugu movie names (assuming you don't know the language id for Telugu) --
SELECT title FROM movies AS m
LEFT JOIN languages AS l
ON m.language_id = l.language_id
WHERE l.name = "Telugu";
-- Show the language and number of movies released in that language-- 
SELECT COUNT(m.movie_id) AS no_of_movies, l.name AS Language
FROM movies AS m
LEFT JOIN languages AS l
ON m.language_id = l.language_id
GROUP BY Language
ORDER BY no_of_movies DESC;
```

**Exercise 6:**  
```-- Simple CROSS JOIN 2 tables --
SELECT *
FROM food_db.items
CROSS JOIN food_db.variants;
-- Concatenating name and variant_name --
SELECT *,
	CONCAT(name, "-", variant_name) AS full_name,
    (price + variant_price) AS final_price
FROM food_db.items
CROSS JOIN food_db.variants;
```

**Exercise 7:**  
```-- Profit table --
SELECT m.movie_id, title, budget, revenue, currency, unit, ROUND(profit_inr_mln,1) AS profit_in_mln_inr
FROM movies AS m
LEFT JOIN profit_normalized AS p
ON m.movie_id = p.movie_id
```

**Exercise 8:**  
```-- Join more than 2 tables to generate table with actor, movies and no.of movies --
SELECT 
	a.name AS actor,
    group_concat(m.title separator " | "),
    COUNT(m.title) AS no_of_movies
FROM movies AS m
JOIN movie_actor AS ma
ON m.movie_id = ma.movie_id
JOIN actors AS a
ON ma.actor_id = a.actor_id
GROUP BY actor
ORDER BY no_of_movies DESC
```

**Exercise 9:**
```-- Generate a report of all Hindi movies sorted by their revenue amount in millions. Print movie name, revenue, currency, and unit --
SELECT 
	title,
    ROUND(CASE
		WHEN unit = "Billions" THEN (revenue*1000)
        WHEN unit = "Thousands" THEN (revenue/1000)
        ELSE revenue
    END,1) AS revenue_mln,
    currency,
    unit
FROM 
	movies AS m
JOIN
	financials AS f
ON
	m.movie_id = f.movie_id
JOIN 
	languages AS l
ON
	m.language_id = l.language_id
WHERE
	l.name = "Hindi"
ORDER BY
	revenue_mln DESC
```

**Exercise 10:**  
```-- Select all the movies with minimum and maximum release_year --
-- Note that there can be more than one movie in min and a max year hence output rows can be more than 2 -- 
SELECT * 
FROM movies
WHERE release_year IN
	((SELECT MIN(release_year) FROM movies),
	(SELECT MAX(release_year) FROM movies));

-- Select all the rows from the movies table whose imdb_rating is higher than the average rating --
SELECT *
FROM movies
WHERE imdb_rating > (
	SELECT AVG(imdb_rating) FROM movies);
```

**Exercise 11:**
```-- Using CTE to generate table with actors between 70 and 85 years of age --
WITH actors_age AS
(SELECT
	name AS actor_name,
    YEAR(CURDATE())-birth_year AS age
FROM actors)
SELECT actor_name, age
FROM actors_age
WHERE age > 70 AND age < 85;
```

**Exercise 12:**
```-- Select all Hollywood movies released after the year 2000 --
-- that made more than 500 million $ profit or more profit. --
-- Note that all Hollywood movies have millions as a unit --
-- hence you don't need to do the unit conversion. --
-- Also, you can write this query without CTE as well --
-- but you should try to write this using CTE only --

WITH h_post_2000 AS 
(
	SELECT *
    FROM movies
    WHERE release_year > 2000 AND industry = "Hollywood"
)
SELECT h.title, h.release_year,
	(revenue - budget) AS profit
FROM financials AS f
JOIN h_post_2000 AS h
ON h.movie_id = f.movie_id
WHERE (revenue - budget) > 500;
```

**Exercise 13:**
```-- Gross Sales Report: Monthly Product Transactions
-- Month
-- Product Name
-- Variant
-- Sold Quantity
-- Gross Price Per Item
-- Gross Price Total

SELECT * FROM fact_sales_monthly
WHERE
	customer_code = 90002002 AND
    get_fiscal_year(date) = 2021
ORDER BY date ASC;

SELECT 
	s.date, s.product_code, p.product, p.variant, s.sold_quantity, g.gross_price,
    ROUND(g.gross_price*s.sold_quantity,2) AS gross_price_total
FROM fact_sales_monthly AS s
JOIN dim_product AS p
ON p.product_code = s.product_code
JOIN fact_gross_price AS g
ON 
	g.product_code = s.product_code AND
    g.fiscal_year = get_fiscal_year(s.date)
WHERE
	customer_code = 90002002 AND
    get_fiscal_year(date) = 2021 AND
    get_fiscal_quarter(date) = "Q4"
ORDER BY date ASC 
LIMIT 1000000;
```

**Exercise 14:**
```-- Gross Sales Report: Total Sales Amount --
SELECT 
	fsm.date, 
    SUM(fgp.gross_price*fsm.sold_quantity) AS total_gross_price
FROM fact_sales_monthly AS fsm
JOIN fact_gross_price AS fgp
ON 
	fgp.product_code = fsm.product_code AND 
    fgp.fiscal_year = get_fiscal_year(fsm.date)
WHERE customer_code = 90002002
GROUP BY fsm.date
ORDER BY fsm.date ASC;
```

**Exercise 15:**
```-- Yearly Sales Report
-- Fiscal Year
-- Total Gross Sales amount In that year from Croma
SELECT
	fgp.fiscal_year,
    SUM(fgp.gross_price*fsm.sold_quantity) AS total_gross_sales_amt
FROM fact_gross_price AS fgp
JOIN fact_sales_monthly AS fsm
ON 
	fgp.product_code = fsm.product_code AND
	fgp.fiscal_year = get_fiscal_year(fsm.date)
WHERE customer_code = 90002002
GROUP BY fgp.fiscal_year
ORDER BY fgp.fiscal_year;
```

**Exercise 16:**
```-- Pre-Invoice Discount Report --
SELECT 
	fsm.date,
    fsm.product_code,
    dp.product,
    dp.variant,
    fsm.sold_quantity,
    fgp.gross_price,
    (fgp.gross_price * fsm.sold_quantity) AS gross_price_total,
    pre.pre_invoice_discount_pct
FROM fact_sales_monthly AS fsm
JOIN dim_product AS dp
ON fsm.product_code = dp.product_code
JOIN fact_gross_price AS fgp
ON 
	fsm.product_code = fgp.product_code AND
    get_fiscal_year(fsm.date) = fgp.fiscal_year
JOIN fact_pre_invoice_deductions AS pre
ON
	fsm.customer_code = pre.customer_code AND
    get_fiscal_year(fsm.date) = pre.fiscal_year
WHERE
	get_fiscal_year(fsm.date) = 2021;
```

**Exercise 17:**  
```-- Net invoice sales using database views --
SELECT
	s.date,
    s.fiscal_year,
    s.customer_code,
    c.market,
    s.product_code,
    p.product,
    p.variant,
    s.sold_quantity,
    g.gross_price AS gross_price_per_unit,
    (s.sold_quantity * g.gross_price) AS gross_price_total,
    pre.pre_invoice_discount_pct
    FROM fact_sales_monthly AS s
    JOIN fact_gross_price AS g
    ON
		s.fiscal_year = g.fiscal_year AND
        s.product_code = g.product_code
	JOIN dim_product AS p
    ON
		g.product_code = p.product_code
	JOIN dim_customer AS c
    ON
		c.customer_code = s.customer_code
	JOIN fact_pre_invoice_deductions AS pre
	ON
		s.customer_code = pre.customer_code AND
        s.fiscal_year = pre.fiscal_year;

SELECT
	*,
    (1 - pre_invoice_discount_pct) * gross_price_total AS net_invoice_sales
FROM sales_preinv_discount;
```

**Exercise 18:**
```-- Post Invoice Discount, Net Sales using database views --
SELECT
	*,
    (1 - pre_invoice_discount_pct) * gross_price_total AS net_invoice_sales,
    (po.discounts_pct + po.other_deductions_pct) AS invoice_discount_pct
FROM sales_preinv_discount AS s
JOIN fact_post_invoice_deductions AS po
ON
	s.customer_code = po.customer_code AND
    s.product_code = po.product_code AND
    s.date = po.date;
    
SELECT 
	*,
    (1-post_invoice_discount_pct) * net_invoice_sales AS net_sales
FROM sales_postinv_discount;
```

**Exercise 19:**
```-- Create a view for gross sales. It should have the following columns,
-- date, fiscal_year, customer_code, customer, market, product_code, product, variant,
-- sold_quanity, gross_price_per_item, gross_price_total
SELECT
	s.date,
    s.fiscal_year,
    s.customer_code,
    c.customer,
    c.market,
    s.product_code,
    p.product,
    p.variant,
    s.sold_quantity,
    g.gross_price AS gross_price_per_item,
    (s.sold_quantity * g.gross_price) AS gross_price_total
FROM fact_sales_monthly AS s
JOIN fact_gross_price AS g
ON
	s.fiscal_year = g.fiscal_year AND
    s.product_code = g.product_code
JOIN dim_product AS p
ON
	g.product_code = p.product_code
JOIN dim_customer AS c
ON 
	c.customer_code = s.customer_code
```

**Exercise 20:**
```-- Top markets by net sales for fiscal year 2021
SELECT 
	market,
    ROUND(SUM(net_sales/1000000),2) AS net_sales_mln
FROM 
	gdb0041.net_sales
WHERE
	fiscal_year = 2021
GROUP BY 
	market
ORDER BY
	net_sales_mln DESC
LIMIT 5
```

**Exercise 21:**
```-- Top customers by net sales for fiscal year 2021

SELECT
	c.customer,
    ROUND(SUM(n.net_sales/1000000),2) AS net_sales_mln
FROM
	gdb0041.net_sales AS n
JOIN
	dim_customer AS c
ON
	c.customer_code = n.customer_code
WHERE 
	fiscal_year = 2021
GROUP BY
	c.customer
ORDER BY
	net_sales_mln DESC
LIMIT 5
```

**Exercise 22:**
```-- Write a stored procedure to get the 
-- top n products by net sales for a given year. 
-- Use product name without a variant.

SELECT
	product,
    ROUND(SUM(net_sales/1000000),2) AS net_sales_mln
FROM
	gdb0041.net_sales
WHERE
	fiscal_year = 2021
GROUP BY
	product
ORDER BY
	net_sales_mln DESC
LIMIT 5
```

**Exercise 23:**
```-- Percentage of amount to total expenses

SELECT 
	*,
    amount*100/SUM(amount) OVER() AS pct_of_total_expenses
FROM expenses
ORDER BY category;

-- Percentage of amount to total category expenses

SELECT 
	*,
    amount*100/SUM(amount) OVER(PARTITION BY category) AS pct_of_category_expenses
FROM expenses
ORDER BY category;

-- Cumulative expenses

SELECT
	*,
    SUM(amount) OVER(PARTITION BY category ORDER BY date) AS cumulative_expenses
FROM expenses
ORDER BY 
	category,
    date;
```

**Exercise 24:**
```-- Net sales global market share percentage in FY 2021

WITH CTE1 AS (
SELECT
	c.customer,
    ROUND(SUM(n.net_sales/1000000),2) AS net_sales_mln
FROM
	gdb0041.net_sales AS n
JOIN
	dim_customer AS c
ON
	c.customer_code = n.customer_code
WHERE 
	n.fiscal_year = 2021
GROUP BY
	c.customer
)
    
SELECT *,
	net_sales_mln*100/SUM(net_sales_mln) OVER() AS market_share_pct
FROM CTE1
ORDER BY market_share_pct DESC;
```

**Exercise 25:**
```-- Market share percentage by region

WITH CTE1 AS (
SELECT 
	customer,
    ROUND(SUM(net_sales/1000000),2) AS net_sales_mln
FROM dim_customer AS c
JOIN gdb0041.net_sales AS s
ON c.customer_code = s.customer_code
WHERE
	s.fiscal_year = 2021 AND
    region = "APAC"
GROUP BY customer)

SELECT *,
	net_sales_mln*100/SUM(net_sales_mln) OVER() AS market_share_pct
FROM CTE1
ORDER BY market_share_pct DESC;
```

**Exercise 26:**
```-- Market share percentage by region (as shown in the course video)

WITH CTE1 AS (
SELECT
	c.customer,
    c.region,
    ROUND(SUM(net_sales)/1000000,2) AS net_sales_mln
FROM net_sales AS s
JOIN dim_customer AS c
ON s.customer_code = c.customer_code
WHERE s.fiscal_year = 2021
GROUP BY c.customer, c.region
ORDER BY net_sales_mln DESC)

SELECT *,
	net_sales_mln*100/SUM(net_sales_mln) OVER(PARTITION BY region) AS pct_share_region
FROM CTE1
ORDER BY region, net_sales_mln DESC
```

**Exercise 27:**  
```-- Window Functions: ROW_NUMBER, RANK, DENSE_RANK --
with cte1 as 
		(select
                     p.division,
                     p.product,
                     sum(sold_quantity) as total_qty
                from fact_sales_monthly s
                join dim_product p
                      on p.product_code=s.product_code
                where fiscal_year=2021
                group by p.division, p.product),
           cte2 as 
	        (select 
                     *,
                     dense_rank() over (partition by division order by total_qty desc) as drnk
                from cte1)
	select * from cte2 where drnk<=3
```

**Exercise 28:**
```-- Show top 2 expenses in each category

WITH CTE1 AS (
SELECT 
	*,
    row_number() OVER(PARTITION BY category ORDER BY amount DESC) AS rn,
    rank() OVER(PARTITION BY category ORDER BY amount DESC) AS r,
    dense_rank() OVER(PARTITION BY category ORDER BY amount DESC) AS dr
FROM expenses
ORDER BY category)

SELECT * FROM CTE1 WHERE dr<=2;

-- Show top 5 students

WITH CTE1 AS (
SELECT 
	*,
    row_number() OVER(ORDER BY marks DESC) AS rn,
    rank() OVER(ORDER BY marks DESC) AS r,
    dense_rank() OVER(ORDER BY marks DESC) AS dr
FROM student_marks)

SELECT * FROM CTE1 WHERE dr<=5;
```

**Exercise 29:**  
```-- Retrieve the top 2 markets in every region 
-- by their gross sales amount in FY=2021

WITH CTE1 AS 
(SELECT 
	g.market,
    c.region,
    SUM(g.gross_price_total/1000000) AS gross_sales_mln
FROM gross_sales AS g
JOIN dim_customer AS c
ON
	g.customer_code = c.customer_code
WHERE g.fiscal_year = 2021
GROUP BY g.market, c.region),

CTE2 AS (
SELECT 
	*,
    dense_rank() OVER(PARTITION BY region ORDER BY gross_sales_mln DESC) AS dr
FROM CTE1)

SELECT *
FROM CTE2
WHERE dr<=2
```

**Exercise 30:**
```-- Creating helper table for generating forecast accuracy reports --
CREATE TABLE fact_actual_estimate
(
SELECT
	f.date,
    f.fiscal_year,
    f.product_code,
    f.customer_code,
    f.forecast_quantity,
    s.sold_quantity
FROM
	fact_forecast_monthly AS f
LEFT JOIN
	fact_sales_monthly AS s
USING (date, product_code, customer_code)

UNION

SELECT
	s.date,
    s.fiscal_year,
    s.product_code,
    s.customer_code,
    f.forecast_quantity,
    s.sold_quantity
FROM
	fact_sales_monthly AS s
LEFT JOIN
	 fact_forecast_monthly AS f
USING (date, product_code, customer_code)
);
```

**Exercise 31:**
```-- Forecast Accuracy Report --
WITH CTE1 AS
(
SELECT
	f.customer_code, 
    c.customer,
    c.market,
    SUM(f.sold_quantity) AS total_sold_qty,
    SUM(f.forecast_quantity) AS total_forecast_qty,
    SUM(f.forecast_quantity - f.sold_quantity) AS net_error,
    SUM(f.forecast_quantity - f.sold_quantity)*100/SUM(f.forecast_quantity) AS net_err_pct,
    SUM(ABS(f.forecast_quantity - f.sold_quantity)) AS abs_error,
    SUM(ABS(f.forecast_quantity - f.sold_quantity))*100/SUM(f.forecast_quantity) AS abs_err_pct
FROM
	fact_actual_estimate AS f
JOIN
	dim_customer AS c
ON
	f.customer_code = c.customer_code
WHERE 
	f.fiscal_year = 2021
GROUP BY
	f.customer_code
)
SELECT 
	*,
    IF
		(abs_err_pct > 100, 0, 100-abs_err_pct) AS forecast_accuracy
FROM
	CTE1
ORDER BY forecast_accuracy DESC
```

**Exercise 32:**  
```-- Write a query for the below scenario.
-- The supply chain business manager wants to see which customers’ 
-- forecast accuracy has dropped from 2020 to 2021. Provide a complete 
-- report with these columns: customer_code, customer_name, market, 
-- forecast_accuracy_2020, forecast_accuracy_2021
-- PART A --
CREATE TEMPORARY TABLE FA_21
WITH CTE1 AS
(
SELECT
	f.customer_code, 
    c.customer,
    c.market,
    SUM(f.sold_quantity) AS total_sold_qty,
    SUM(f.forecast_quantity) AS total_forecast_qty,
    SUM(f.forecast_quantity - f.sold_quantity) AS net_error,
    SUM(f.forecast_quantity - f.sold_quantity)*100/SUM(f.forecast_quantity) AS net_err_pct,
    SUM(ABS(f.forecast_quantity - f.sold_quantity)) AS abs_error,
    SUM(ABS(f.forecast_quantity - f.sold_quantity))*100/SUM(f.forecast_quantity) AS abs_err_pct
FROM
	fact_actual_estimate AS f
JOIN
	dim_customer AS c
ON
	f.customer_code = c.customer_code
WHERE 
	f.fiscal_year = 2021
GROUP BY
	f.customer_code
)
SELECT 
	*,
    IF
		(abs_err_pct > 100, 0, 100-abs_err_pct) AS forecast_accuracy
FROM
	CTE1
ORDER BY forecast_accuracy DESC;

-- PART B --
CREATE TEMPORARY TABLE FA_20
WITH CTE1 AS
(
SELECT
	f.customer_code, 
    c.customer,
    c.market,
    SUM(f.sold_quantity) AS total_sold_qty,
    SUM(f.forecast_quantity) AS total_forecast_qty,
    SUM(f.forecast_quantity - f.sold_quantity) AS net_error,
    SUM(f.forecast_quantity - f.sold_quantity)*100/SUM(f.forecast_quantity) AS net_err_pct,
    SUM(ABS(f.forecast_quantity - f.sold_quantity)) AS abs_error,
    SUM(ABS(f.forecast_quantity - f.sold_quantity))*100/SUM(f.forecast_quantity) AS abs_err_pct
FROM
	fact_actual_estimate AS f
JOIN
	dim_customer AS c
ON
	f.customer_code = c.customer_code
WHERE 
	f.fiscal_year = 2020
GROUP BY
	f.customer_code
)
SELECT 
	*,
    IF
		(abs_err_pct > 100, 0, 100-abs_err_pct) AS forecast_accuracy
FROM
	CTE1
ORDER BY forecast_accuracy DESC;

-- PART C --
SELECT
	A.customer_code,
    A.customer,
    A.market,
    B.forecast_accuracy AS FA20,
    A.forecast_accuracy AS FA21
FROM FA_21 AS A
JOIN FA_20 AS B
ON A.customer_code = B.customer_code
WHERE A.forecast_accuracy < B.forecast_accuracy
ORDER BY B.forecast_accuracy
```

**Exercise 33: Gross Sales View**


