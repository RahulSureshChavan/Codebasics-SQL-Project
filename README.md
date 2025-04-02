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
