# Exploring Netflix Project Data Analysis Using MySQL

![Netflix_Logo](https://github.com/itzthealteboy/Netflix_project/blob/main/original%20netflix.jpg)

## Overview
In a recent project of a detailed analysis of Netflix movies and TV shows, I used MySQL to analyze viewer habits, uncovering popular genres and peak viewing times. This data guided content acquisition and targeted marketing, enhancing user experience and boosting subscriber growth by aligning our offerings with what viewers truly wanted. The following README provides a detailed account of the project's objectives, businesss problems, solutions, findings and conclusion.

# Objectives
 * Examine the distribution of content type (Movies Vs TV shows)
 * Examine the highest common rating for movies and TV shows
 * Identify the top contents based on years, countries
 * Analyze other categories based on specific criteria and keywords

# Dataset
The data for the project is sourced from Kaggle dataset :
 * Dataset Link : https://www.kaggle.com/datasets/victorsoeiro/netflix-tv-shows-and-movies?select=titles.csv
SELECT * FROM netflix.titles;

-- total content
SELECT COUNT(*)
FROM titles;

-- 1. COUNT THE NUMBER OF MOVIES TO SHOWS
SELECT type, count(*) as no_of_content
FROM titles
GROUP BY type;

-- 2.Find the most common rating for TV & Shows
SELECT type,
	   age_certification
FROM
      (SELECT type, age_certification, COUNT(age_certification), RANK() OVER(PARTITION BY type ORDER BY COUNT(age_certification ) DESC) AS ranking
       FROM titles
       GROUP BY 1,2
	   ORDER BY 4 DESC) AS t1
	    WHERE ranking = 1 
;


-- 3. List all movies released at a a particular year
SELECT *
FROM titles
WHERE release_year = 2003
AND type = 'MOVIE' 
;
 
 -- 4. Find the top 10 countries with the most content
WITH RECURSIVE prod_countr AS (
    -- Get the first animal from each row
    SELECT 
        TRIM(SUBSTRING_INDEX(production_countries, ',', 1)) AS country,
        SUBSTRING(production_countries, LENGTH(SUBSTRING_INDEX(production_countries, ',', 1)) + 2) AS rest
    FROM titles
UNION ALL
    
    -- Recursively get the next countries
    SELECT 
        TRIM(SUBSTRING_INDEX(rest, ',', 1)),
        SUBSTRING(rest, LENGTH(SUBSTRING_INDEX(rest, ',', 1)) + 2)
    FROM prod_countr
    WHERE rest <> ''
)
SELECT country, COUNT(country) FROM prod_countr
GROUP BY country
ORDER BY 2 DESC
LIMIT 10;


-- 5. IDENTIFY THE LONGEST MOVIES
SELECT CONCAT(runtime, ' mins') AS time
FROM titles;

UPDATE titles
SET runtime = CONCAT(runtime, ' mins') ;

ALTER TABLE titles
MODIFY COLUMN runtime VARCHAR(20);  -- Increase size as necessary

UPDATE titles
SET runtime = CONCAT(runtime, ' mins');

SELECT runtime
FROM titles
;
SELECT title, runtime
FROM titles
WHERE type = 'MOVIE'
ORDER BY 2 DESC;

-- Looking for the last time movie was updated 
SELECT *
FROM titles
WHERE release_year like '20%'
ORDER BY 4 DESC
;


-- 6. Find the content added in the last 5 years where the current year is 2014
SELECT MIN(release_year), MAX(release_year) FROM titles;

SELECT *
FROM titles
WHERE release_year BETWEEN '2010' AND '2014'
;


-- 7. Find the most popular TV shows or movies in each year
SELECT title,release_year, tmdb_popularity
FROM( 
SELECT title, type, release_year, tmdb_popularity,
RANK() OVER(PARTITION BY release_year ORDER BY tmdb_popularity DESC) as ranking
FROM titles
ORDER BY 3 DESC
) AS t2
WHERE ranking = 1
;

-- 8. the highest human rating shows in each year
SELECT title,release_year, imdb_score, type
FROM( 
SELECT title, type, release_year, imdb_score,
RANK() OVER(PARTITION BY release_year ORDER BY imdb_score DESC) as ranking
FROM titles
ORDER BY 3 DESC
) AS t2
WHERE ranking = 1
AND type = 'SHOW'
;

-- 9. Count the content item in each genre
WITH RECURSIVE content AS (
    -- Get the first genre from each row
    SELECT 
        TRIM(SUBSTRING_INDEX(genres, ',', 1)) AS country,
        SUBSTRING(genres, LENGTH(SUBSTRING_INDEX(genres, ',', 1)) + 2) AS rest
    FROM titles
UNION ALL
    
    -- Recursively get the next countries
    SELECT 
        TRIM(SUBSTRING_INDEX(rest, ',', 1)),
        SUBSTRING(rest, LENGTH(SUBSTRING_INDEX(rest, ',', 1)) + 2)
    FROM content
    WHERE rest <> ''
)
SELECT country, COUNT(country) FROM content
GROUP BY country
ORDER BY 2 DESC
;

-- 10. Find the average release year for content by US on netflix
SELECT release_year,COUNT(release_year)
FROM titles
WHERE production_countries = 'US'
GROUP BY release_year
ORDER BY 1 DESC;

-- 11. List all movies that are documentation
select title, genres, type
from titles
where genres = 'documentation'
ANd type = 'MOVIE'
;
