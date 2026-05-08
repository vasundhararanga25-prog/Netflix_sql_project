# Netflix movies and tv shows data analysis using sql

![Netflix Logo](https://github.com/vasundhararanga25-prog/Netflix_sql_project/blob/main/logo.png)

## Objective
This project analyzes Netflix movies and TV shows data using SQL to extract meaningful insights about content trends, ratings, genres, countries, and release patterns.

## 📌 Project Overview

The goal of this project is to explore and analyze Netflix content data using SQL queries and understand:

Distribution of Movies vs TV Shows
Most common ratings
Top countries producing Netflix content
Popular genres/categories
Year-wise content growth
Directors and actors with most content
Duration analysis
Content trends over time
🛠️ Tools & Technologies Used
MySQL / PostgreSQL
SQL
Git & GitHub
Kaggle Dataset / Netflix Dataset
📂 Dataset

The dataset contains information about Netflix Movies and TV Shows including:

Title
Type
Director
Cast
Country
Release Year
Rating
Duration
Genre
Date Added

Dataset size: 8000+ records

## 🔍 Key SQL Concepts Used
SELECT Statements
WHERE, GROUP BY, ORDER BY
Aggregate Functions
CASE Statements
JOINS
Subqueries
CTEs
Window Functions (if used)
📊 Business Questions Solved

-- Netflix Project

## SCHEMA 
 '''sql
CREATE TABLE netflix
(
show_id VARCHAR(6),
type VARCHAR(10),
title VARCHAR(150),
director VARCHAR(210),
casts VARCHAR(1000),
country VARCHAR(150),
date_added VARCHAR(50),
release_year INT,
rating VARCHAR(10),
duration VARCHAR(15),
listed_in VARCHAR(100),
description VARCHAR(250)
);


SELECT * FROM netflix ;
'''

## 15 Business problems 

### 1. Count the number of tv shows and movies 

'''sql
  SEELCT type,
  COUNT(*) as total_content 
  FROM netflix 
  GROUP BY type;
'''

### 2. Find the most common rating for movies and tv shows
'''sql
 SEELCT 
type, 
rating 
FROM (
SELECT 
   type,
   rating,
   count(*),
   RANK () OVER(PARTITION BY type ORDER BY  COUNT(*) DESC) as ranking 
FROM netflix
GROUP BY 1,2 
) as t1 
WHERE 
     ranking=1 ;
'''

### 3. List all  movies released in a specific year (e.g. 2020)
   '''sql

SELECT 
   * FROM netflix 
WHERE 
    type='Movie'
	  AND 
	release_year=2020;
'''

### 4.Find the top 5 countries with the most content on netflix 

'''sql
  SELECT 
      UNNEST (STRING_TO_ARRAY(country,',')) as new_country,
	  COUNT(show_id) as total_content 
  FROM netflix 
  GROUP BY 1
  ORDER BY 2 DESC
  LIMIT 5;
'''

### 5. Find the longest movie 
'''sql
SELECT * FROM netflix 
WHERE 
  type='Movie'
  AND 
  duration=( SELECT MAX(DURATION) FROM netflix);
'''

### 6. fIND CONTENT added in last 5 years 
'''sql
SELECT * 
FROM netflix 
 WHERE 
    TO_DATE(date_added,'month DD,YYYY')>= CURRENT_DATE-INTERVAL '5 years'
'''

### 7.Find all the movies/tv shows by director 'rajiv chilaka'
'''sql
SELECT *
FROM netflix
WHERE 
  director='Rajiv Chilaka'
  ;
'''

### 8. List all the tv shows with more than 5 seasons 

'''sql
SELECT * FROM netflix 
WHERE
   type='TV Show'
   AND
   SPLIT_PART(duration,' ',1)::numeric >5 ;
'''

### 9. Count the number of content items in each genre 

'''sql
SELECT 
	UNNEST(STRING_TO_ARRAY(listed_in,',')),
	COUNT(show_id) as total_content
FROM netflix 
GROUP BY 1;
'''

### 10. Find each year and the average numbers of content release by India on netflix . 
     return top 5 year with highest avg content release:

'''sql
 SELECT 
 EXTRACT(YEAR FROM TO_DATE(date_added,'Month DD,YYYY')) as year,
 COUNT(*) as yearly_content,
 ROUND(
 COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country='India')::numeric * 100 ,2 )as avg_content_per_year
 FROM netflix 
   WHERE 
   country='India'
   GROUP BY 1
'''
### 11. List all the movies that are documentaries 
'''sql
SELECT * FROM netflix 
WHERE 
listed_in ILIKE '%documentaries%';
'''
### 12. Find all content without a director 

'''sql
SELECT * FROM netflix 
WHERE director IS NULL;
'''

### 13. Find how many movies actor 'Salman Khan' appeeared in last 10 years:

'''sql
SELECT * FROM netflix 
WHERE 
casts ILIKE '%salman khan%'
AND 
release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;

'''
### 14. Find the top 10 actors who have appeared in the highest number of movies produced in India:

'''sql
SELECT 
UNNEST(STRING_TO_ARRAY(casts,','))as actors,
COUNT(*) as total_content
FROM netflix 
WHERE 
  country ILIKE '%INDIA%'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
'''

### 15. Categorize the content based on the presence of the keywords 'kill' and 'voilence'in 
    the description field. Label content containing these keywords as 'bad' and all other
	content as good. Count how many items fall inton each category. 
'''sql
WITH new_table 
AS 
(
SELECT * ,
   CASE 
   WHEN 
        description ILIKE '%KILL%' 
        OR description ILIKE '%VIOLENCE%' THEN 'bad_content'
    ELSE 'Good_content'
END category
FROM netflix 
)
SELECT 
    category ,
	COUNT(*)as total_content
	FROM new_table
	GROUP BY 1;
'''
## 📈 Key Findings
Movies dominate Netflix content compared to TV Shows.
TV-MA is one of the most common ratings.
United States contributes the highest amount of content.
Netflix content increased rapidly after 2015.

##🚀 How to Run This Project
Download the dataset
Import the CSV file into MySQL/PostgreSQL
Run the SQL queries provided in the project
Analyze the output and insights
