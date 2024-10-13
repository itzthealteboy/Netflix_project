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


## Business Problem and Solution

### 1. Count the number of TV shoes and movies

```sql
SELECT COUNT(*)
FROM titles;
```
### 2.Find the most common rating for TV & Shows

```sql
SELECT type,
	   age_certification
FROM
      (SELECT type, age_certification, COUNT(age_certification), RANK() OVER(PARTITION BY type ORDER BY COUNT(age_certification ) DESC) AS ranking
       FROM titles
       GROUP BY 1,2
	   ORDER BY 4 DESC) AS t1
	    WHERE ranking = 1 
;
```
### 3. List all movies released at a a particular year

```sql
SELECT *
FROM titles
WHERE release_year = 2003
AND type = 'MOVIE' 
;
``` 
### 4. Find the top 10 countries with the most content

```sql
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
```
### 5. Identify the longest movie

```sql
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
;
```

### 6. Find the content added in the last 5 years where the current year is 2014

```sql
-- Looking for the last time movie was updated 
SELECT *
FROM titles
WHERE release_year like '20%'
ORDER BY 4 DESC
SELECT MIN(release_year), MAX(release_year) FROM titles;

SELECT *
FROM titles
WHERE release_year BETWEEN '2010' AND '2014'
;
```

### 7. Find the most popular TV shows or movies in each year

```sql
SELECT title,release_year, tmdb_popularity
FROM( 
SELECT title, type, release_year, tmdb_popularity,
RANK() OVER(PARTITION BY release_year ORDER BY tmdb_popularity DESC) as ranking
FROM titles
ORDER BY 3 DESC
) AS t2
WHERE ranking = 1
;
```
### 8. The highest human rating shows in each year

```sql
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
```
### 9. Count the content item in each genre

```sql
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
```
### 10. Find the average release year for content by US on netflix

```sql
SELECT release_year,COUNT(release_year)
FROM titles
WHERE production_countries = 'US'
GROUP BY release_year
ORDER BY 1 DESC;
```
### 11. List all movies that are documentation

```sql
select title, genres, type
from titles
where genres = 'documentation'
ANd type = 'MOVIE'
;
```

### INSIGHTS AND CONCLUSION
* Content Distribution: The dataset comprises a diverse collection of movies and TV shows, featuring a wide array of ratings and genres.
* Common Ratings: Insights into the common ratings provide a deeper understanding of the intended audience for the content.
* Location Analysis : Location influences themes, cultural references, and audience relatability in movies and TV shows, shaping their overall narrative and appeal.

### AUTHOR - OLAJUBU OLASUBOMI
This project highlights my SQL skills essential for data analyst roles and is part of my portfolio. For questions, feedback, or collaboration, please reach out!

### For more content on my data analyst skills on data-related topics, make sure to follow me on social media 
*LinkedIn :https://www.linkedin.com/in/olajubu-olasubomi-980418239?lipi=urn%3Ali%3Apage%3Ad_flagship3_profile_view_base_contact_details%3B7HCbaVsqR5%2BFlIub3KESrg%3D%3D

*Email address: olasubomiolajubu@gmail.com

Thank you for your support, and I look forward to connect with you!
