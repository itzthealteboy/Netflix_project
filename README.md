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
