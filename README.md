# NETFLIX MOVIES AND TV SHOWS DATA ANALYSIS USING SQL
 ![NETFLIX LOGO](https://github.com/swetha0460/NETFLIX_SQL_1/blob/main/NETFLIX.IMAGE.jpeg)

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
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

## Business Problems and Solutions

### 1. Count the Number of Movies vs TV Shows

```sql
SELECT 
    type,
    COUNT(*) as total_content
FROM netflix
GROUP BY type;
```

**Objective:** Determine the distribution of content types on Netflix.

### 2. Find the Most Common Rating for Movies and TV Shows

```sql
    SELECT 
        type,
        rating,
    FROM
(
    SELECT 
        type,
        rating,
        RANK() OVER (PARTITION BY type ORDER BY count (*)) AS ranking
    FROM NETFLIX
GROUP BY
1,2
) AS T1
WHERE rankING = 1;
```

**Objective:** Identify the most frequently occurring rating for each type of content.

### 3. List All Movies Released in a Specific Year (e.g., 2020)

```sql
SELECT * 
FROM netflix
WHERE
TYPE='Movie'
AND
release_year = 2020;
```

**Objective:** Retrieve all movies released in a specific year.

### 4. Find the Top 5 Countries with the Most Content on Netflix

```sql
    SELECT 
        UNNEST(STRING_TO_ARRAY(REPLACE(TRIM(country, ' ,',','),',')) AS new_country,
        COUNT(SHOW_ID) AS total_content
    FROM netflix
    GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```

**Objective:** Identify the top 5 countries with the highest number of content items.

### 5. Identify the Longest Movie

```sql
SELECT 
    TITLE,
SUBSTRING(DURATION,1,POSITION('m' IN DURATION)-1):: INT DURATION
FROM netflix
WHERE type = 'Movie'
AND DURATION IS NOT NULL
ORDER BY 2 DESC
LIMIT 5
```

**Objective:** Find the movie with the longest duration.

### 6. Find Content Added in the Last 5 Years

```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

**Objective:** Retrieve content added to Netflix in the last 5 years.

### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'

```sql
SELECT *
TYPE,
TITLE,
DIRECTOR
    FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%';
```

**Objective:** List all content directed by 'Rajiv Chilaka'.

### 8. List All TV Shows with More Than 5 Seasons

```sql
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::NUMERIC > 5;
```

**Objective:** Identify TV shows with more than 5 seasons.

### 9. Count the Number of Content Items in Each Genre

```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(SHOW_ID) AS COUNT_GENRE
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
```

**Objective:** Count the number of content items in each genre.

### 10.Find each year and the average numbers of content release in India on netflix. 
return top 5 year with highest avg content release!

```sql
SELECT
	EXTRACT(
		YEAR
		FROM
			TO_DATE(DATE_ADDED, 'Month DD,YYYY')
	) AS YEAR,
	COUNT(*) AS YEARLY_CONTENT,
	ROUND(
		COUNT(*)::NUMERIC / (
			SELECT
				COUNT(*)
			FROM
				NETFLIX
			WHERE
				COUNTRY = 'India'
		)::NUMERIC * 100,
		2
	) AS AVG_CONTENT
FROM
	NETFLIX
WHERE
	COUNTRY ='India'
GROUP BY
	1
ORDER BY
	YEARLY_CONTENT DESC
LIMIT
	5

```

**Objective:** Calculate and rank years by the average number of content releases by India.

### 11. List All Movies that are Documentaries

```sql
SELECT * 
FROM netflix
WHERE TYPE='Movie'
AND
listed_in LIKE '%Documentaries';
```

**Objective:** Retrieve all movies classified as documentaries.

### 12. Find All Content Without a Director

```sql
SELECT * 
FROM netflix
WHERE director IS NULL;
```

**Objective:** List content that does not have a director.

### 13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years

```sql
SELECT * 
FROM netflix
WHERE casts ILIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

**Objective:** Count the number of movies featuring 'Salman Khan' in the last 10 years.

### 14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

```sql
SELECT 
    TRIM(UNNEST(STRING_TO_ARRAY(casts, ','))) AS actors,
    COUNT(*) AS NO_OF_FILMS
FROM netflix
WHERE country ILIKE '%India%'
AND
TYPE='Movie'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
```

**Objective:** Identify the top 10 actors with the most appearances in Indian-produced movies.

### 15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords

```sql
WITH
	NEW_TABLE AS (
		SELECT
			*,
			CASE
				WHEN DESCRIPTION ILIKE '%KILL%'
				OR DESCRIPTION ILIKE '%VIOLENCE%' THEN 'BAD_CONTENT'
				ELSE 'GOOD_CONTENT'
			END CATEGORY
		FROM
			NETFLIX
	)
SELECT
	CATEGORY,
	COUNT(*) AS TOTAL_CONTENT
FROM
	NEW_TABLE
GROUP BY
	1

```

**Objective:** Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.




