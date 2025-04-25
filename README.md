# Netflix Movies and TV Shows Analysis using SQL

![Netflix Logo](https://github.com/user-attachments/assets/c0a38db6-c24d-43bf-9c79-586011a0abd6)

## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives

- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema

```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
    show_id      VARCHAR(6),
    type         VARCHAR(10),
    title        VARCHAR(150),
    director     VARCHAR(208),
    casts        VARCHAR(1000),
    country      VARCHAR(150),
    date_added   VARCHAR(50),
    release_year INT,
    rating       VARCHAR(10),
    duration     VARCHAR(15),
    listed_in    VARCHAR(100),
    description  VARCHAR(250)
);
```

## 15 Business Problems and Solutions

### 1. Count the total Number of Movies vs TV Shows.

```sql
SELECT
  type,
  COUNT(*) AS total_count
FROM netflix
GROUP BY type;

```

**Objective:** Determine the distribution of content types on Netflix.

### 2. Find the Most Common Rating for the Movies and the TV Shows.

```sql
SELECT 
  type,
  rating
FROM 
(
   SELECT 
      type,
	  rating,
	  COUNT(*),
	  RANK() OVER(PARTITION BY type ORDER BY COUNT(*) DESC) AS ranking
	FROM netflix
	GROUP BY 1, 2
  ) AS t1
WHERE 
   ranking = 1;

```

**Objective:** Identify the most frequently occurring rating for each type of content.

### 3. List all the Movies that released in a Specific Year (e.g., 2020).

```sql
SELECT 
   title
FROM netflix
WHERE type = 'Movie'
AND release_year = 2020;

```

**Objective:** Retrieve all movies released in a specific year.

### 4. Find the top 5 countries with the most content on the Netflix.

```sql
SELECT
   UNNEST(STRING_TO_ARRAY(country, ',')) AS new_country,
   COUNT(show_id) AS total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

**Objective:** Identify the top 5 countries with the highest number of content items.

### 5. Identify the longest Movie.

```sql
SELECT * FROM netflix
WHERE 
   type = 'Movie'
   AND 
   duration = (SELECT MAX(duration) FROM netflix);
```

**Objective:** Find the movie with the longest duration.

### 6.  Find the content that added in the last 5 years.

```sql
SELECT
   *
FROM netflix
WHERE 
  TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

**Objective:** Retrieve content added to Netflix in the last 5 years.

### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'

```sql
SELECT * FROM netflix
WHERE director LIKE '%Rajiv Chilaka%' ;

```

**Objective:** List all content directed by 'Rajiv Chilaka'.

### 8. List all the TV Shows which have more than 5 Seasons.

```sql
SELECT
   *
FROM netflix
WHERE 
   type = 'TV Show'
   AND 
   SPLIT_PART(duration, ' ', 1)::numeric > 5 ;

```

**Objective:** Identify TV shows with more than 5 seasons.

### 9. Count the number of content items in each genre.

```sql
SELECT
   UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
   COUNT(show_id) as total_content
FROM netflix
GROUP BY 1
ORDER BY 1;

```

**Objective:** Count the number of content items in each genre.

### 10.Find each year and the average numbers of content release in India on netflix. 
and return top 5 year with highest avg content release!

```sql
SELECT
   EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) AS year,
   COUNT(*),
   ROUND(
   COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100
   ,2) AS avg_content_per_year
FROM netflix
WHERE country = 'India'
GROUP BY 1
ORDER BY 1
LIMIT 5;
```

**Objective:** Calculate and rank years by the average number of content releases by India.

### 11. List all the Movies that are Documentaries.

```sql
SELECT 
  title 
FROM netflix
WHERE type = 'Movie' 
      AND listed_in ILIKE '%documentaries%' ;
 
```

**Objective:** Retrieve all movies classified as documentaries.

### 12. Find all the content that is without a Director.

```sql
SELECT * 
FROM netflix
WHERE director IS NULL;
```

**Objective:** List content that does not have a director.

### 13. Find out the name of movies in which 'Salman Khan' is appeared as Actor in the last 10 years.

```sql
SELECT 
  show_id,
  type,
  title,
  release_year
FROM netflix
WHERE 
  casts ILIKE '%Salman Khan%'
  AND
  release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10 ;

```

**Objective:** Count the number of movies featuring 'Salman Khan' in the last 10 years.

### 14. Find the top 10 actors who have appeared in the highest number of movies that produced in India.

```sql
SELECT 
  UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
  COUNT(*) AS total_content
FROM netflix
WHERE country ILIKE '%India%'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;

```

**Objective:** Identify the top 10 actors with the most appearances in Indian-produced movies.

### 15. Categorize the content based on the presence of the keywords 'Kill' and 'Violence' in the description field.
Lable content containing these keywords as 'Bad' and all other content as 'Good'.Count how many items fall into each category.

```sql
WITH label_content AS(

SELECT
*,
  CASE
  WHEN 
      description ILIKE '%kill%' OR
	  description ILIKE '%violence%' THEN 'Bad_Content'
	  ELSE 'Good_Content'
  END category
FROM netflix

)
SELECT 
    category,
	COUNT(*) AS total_content
FROM label_content
GROUP BY 1;

```

**Objective:** Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.



## Author - Rishika Chawda

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. 



