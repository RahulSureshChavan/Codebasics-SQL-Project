# Codebasics-SQL-Project

Exercise 1:  
-- Print all movies in the order of their release year (latest first) --  
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
