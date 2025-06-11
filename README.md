# Netflix Movies and TV Show Data Analysis using SQL
![Netflix Logo](https://github.com/Fraz-Rasheed/Netflix_sql-Project/blob/main/download.png)

**Overview**
This project involves a comprehensive analysis of Netflix’s movies and TV shows data using SQL. The goal is to extract valuable insight and answer various business questions based on the dataset.
**Objectives** 
•	Analyze the distribution of content types(movie vs TV show).
•	Identify the most common ratings for movies and TC shows.
•	List and analyze content based on release years, countries , and durations.
•	Explore and categorize content based on specific criteria and keywords.
**Dataset**
The data for this project is sourced from the Kaggle dataset:

         
**Schema**
 
CREATE TABLE netflix(
show_id VARCHAR(6),
type VARCHAR(10),
title VARCHAR(150),
director VARCHAR(210),
casts VARCHAR(1000),	
country VARCHAR(150),
date_added VARCHAR(50),
release_year INT,
rating	VARCHAR(10),
duration VARCHAR(15),
listed_in VARCHAR(100),
description VARCHAR(250)
);









**Business Problems and Solutions
1.	For finding total content **

 SELECT * FROM netflix;

SELECT 
    COUNT(*) as total_content 
FROM netflix;


**2.	For finding distinct type**

SELECT 
DISTINCT type
FROM netflix;



 
**3.	Number of movies vs TV Shows**
 

SELECT 
type, 
COUNT(*) as total_content
FROM netflix
GROUP BY TYPE ;


**4.	Most common rating to movies and tc shows**

SELECT 
type, 
rating
FROM
(

SELECT 
type, 
rating,
COUNT(*),
RANK() OVER(PARTITION BY type ORDER BY COUNT(*) DESC) as ranking
FROM netflix
GROUP BY 1,2
) as t1
WHERE ranking =1;
 
**5.	List of movies released in a specfic year**

SELECT * FROM netflix
WHERE type = 'Movie' And
release_year=2021;
 
**6.	Top countries with the most content on Netflix**
SELECT
UNNEST(STRING_TO_ARRAY(country,',')) as new_country

,COUNT(show_id) as total_content
FROM netflix 
GROUP BY 1
ORDER BY 2 DESC;
 
**7.	Identify the longest movie**
SELECT *From netflix
WHERE 
type ='Movie'
AND
duration = (SELECT MAX(duration)FROM netflix)
;

 
**8.	find content added in last 5 years**
 SELECT *

FROM netflix
WHERE 
TO_DATE(date_added, 'Month DD,YYYY') >= CURRENT_DATE-INTERVAL '5 years';
**9.	Finding all the movie/tv Shows by director 'Rajiv Chitlaka'**
 SELECT * FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%';
**10.	List all the tv show more that 5 seasons**
SELECT *
FROM netflix
where
type ='TV Show'
AND
SPLIT_PART(duration, ' ',1)::numeric > 5 ;
 
**11.	Counting the number of contents items in each genre**
SELECT 
UNNEST(STRING_TO_ARRAY(listed_in,','))
as genre,
COUNT(show_id) as total_content

FROM netflix
GROUP BY 1;

 
**12.	Find each year and the average numbers of content release by United States on Netflix return top 5 year with highest avg content release**
SELECT 
EXTRACT(YEAR FROM TO_DATE(date_added,'MONTH DD,YYYY'))as yearly_content
,COUNT(*),
ROUND(COUNT(*)::numeric/(SELECT COUNT(*)FROM netflix WHERE country = 'United States') * 100 ,2)as avg_content_per_year
FROM netflix
WHERE country ILIKE '%United States%'
GROUP BY 1;
 
**13.	List all movies that are documentariens** 

SELECT * FROM netflix WHERE
listed_in ILIKE '%Documentaries%';



**14.	 finding all the content without director**
 
 SELECT * FROM netflix where
 director IS NULL;
**15.	13 Finding how many movies actor 'Salman Khan' appeared in last 10 years**

 	 SELECT * FROM netflix
	 WHERE 
	 casts ILIKE '%Salman Khan%'
	 AND
	 release_year>EXTRACT(YEAR FROM CURRENT_DATE)-10;
**16.	Finding the top 10 actors who apperaed in the highest number of movies produced in INDIA**
 

SELECT 

UNNEST(STRING_TO_ARRAY(casts,','))as actors
,COUNT(*)as total_content
FROM netflix
WHERE country ILIKE '%India%'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;





**17.	Categorizing the content based on the presence of the keywords 'kill' and 'violance' 
in the description field. Label content containing these keywords as 'Bad' and all other 
'GOOd' . Count how many items fall into each category.**

WITH new_table
AS(
SELECT *,
CASE
WHEN
 description ILIKE '%kill%'
 OR
 description ILIKE '%violence%'

THEN 'Bad-Film'
ELSE 'Good Content'
END category
FROM netflix
)
SELECT
category,
COUNT(*) as total_content
FROM new_table
GROUP BY 1;
 
