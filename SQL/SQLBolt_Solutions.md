# My solutions for SQLBolt tutorial

## Link SQLBolt

https://sqlbolt.com/

### SQL Lesson 1: SELECT queries 101

1. Find the title of each film

```
SELECT title
FROM movies;
```

2. Find the director of each film

```
SELECT director
FROM movies;
```

3. Find the title and director of each film

```
SELECT title, director
FROM movies;
```

4. Find the title and year of each film

```
SELECT title, year
FROM movies;
```

5. Find all the information about each film

```
SELECT *
FROM movies;
```

### SQL Lesson 2: Queries with constraints (Pt. 1)

1. Find the movie with a row id of 6

```
SELECT *
FROM movies
WHERE id=6;
```

2. Find the movies released in the years between 2000 and 2010

```
SELECT *
FROM movies
WHERE year BETWEEN 2000 AND 2010;
```

3. Find the movies not released in the years between 2000 and 2010

```
SELECT * FROM movies
WHERE year NOT BETWEEN 2000 AND 2010;
```

4. Find the first 5 Pixar movies and their release year

```
SELECT *
FROM movies
WHERE id IN (1,2,3,4,5);
```

### SQL Lesson 3: Queries with constraints (Pt. 2)

1. Find all the Toy Story movies

```
SELECT *
FROM movies
WHERE Title LIKE "Toy Story%";
```

2. Find all the movies directed by John Lasseter

```
SELECT *
FROM movies
WHERE Director LIKE "John Lasseter";
```

3. Find all the movies (and director) not directed by John Lasseter

```
SELECT *
FROM movies
WHERE Director NOT LIKE "John Lasseter";
```

4. Find all the WALL-* movies

```
SELECT *
FROM movies
WHERE Title LIKE "WALL-%";
```

### SQL Lesson 4: Filtering and sorting Query results

1. List all directors of Pixar movies (alphabetically), without duplicates

```
SELECT DISTINCT Director
FROM movies
ORDER BY DIRECTOR ASC;
```

2.  List the last four Pixar movies released (ordered from most recent to least)

```
SELECT *
FROM movies
ORDER BY Year DESC
LIMIT 4;
```

3. List the first five Pixar movies sorted alphabetically

```
SELECT *
FROM movies
ORDER BY Title ASC
LIMIT 5;
```

4. List the next five Pixar movies sorted alphabetically

```
SELECT *
FROM movies
ORDER BY Title ASC
LIMIT 5
OFFSET 5;
```

### SQL Review: Simple SELECT Queries

1. List all the Canadian cities and their populations

```
SELECT City, Population
FROM north_american_cities
WHERE Country="Canada";
```

2. Order all the cities in the United States by their latitude from north to south

```
SELECT *
FROM north_american_cities
WHERE Country="United States"
ORDER BY Latitude DESC;
```

3. List all the cities west of Chicago, ordered from west to east

```
SELECT *
FROM north_american_cities
ORDER BY Longitude ASC
LIMIT 6;
```

4. List the two largest cities in Mexico (by population)

```
SELECT *
FROM north_american_cities
WHERE Country ="Mexico"
ORDER BY Population DESC
LIMIT 2;
```

5. List the third and fourth largest cities (by population) in the United States and their population

```
SELECT City, Population
FROM north_american_cities
WHERE Country ="United States"
ORDER BY Population DESC
LIMIT 2
OFFSET 2;
```

### SQL Lesson 6: Multi-table queries with JOINs


1. Find the domestic and international sales for each movie

```
SELECT *
FROM movies INNER JOIN boxoffice ON movies.id=boxoffice.movie_id;
```

2. Show the sales numbers for each movie that did better internationally rather than domestically

```
SELECT *
FROM movies INNER JOIN boxoffice ON movies.id=boxoffice.movie_id
WHERE International_sales > domestic_sales
ORDER BY ID;
```

3. List all the movies by their ratings in descending order

```
SELECT *
FROM movies INNER JOIN boxoffice ON movies.id=boxoffice.movie_id
ORDER BY Rating DESC;
```

### SQL Lesson 7: OUTER JOINs

1. Find the list of all buildings that have employees

```
SELECT DISTINCT building
FROM employees;
```

2. Find the list of all buildings and their capacity

```
SELECT *
FROM Buildings;
```

3. List all buildings and the distinct employee roles in each building (including empty buildings)

```
SELECT DISTINCT Building_name, Role
FROM Buildings LEFT JOIN Employees ON Buildings.Building_name=Employees.Building;
```

### SQL Lesson 8: A short note on NULLs

1. Find the name and role of all employees who have not been assigned to a building

```
SELECT *
FROM employees
WHERE Building IS NULL;
```

2. Find the names of the buildings that hold no employees

```
SELECT building_name
FROM buildings LEFT JOIN employees ON buildings.building_name=employees.building
WHERE building IS NULL;
```

### SQL Lesson 9: Queries with expressions


1. List all movies and their combined sales in millions of dollars

```
SELECT (domestic_sales + international_sales) / 1000000 AS millions, title
FROM movies INNER JOIN boxoffice ON movies.id=boxoffice.movie_id;
```

2. List all movies and their ratings in percent

```
SELECT rating * 10 AS rating, title
FROM movies INNER JOIN boxoffice ON movies.id=boxoffice.movie_id;
```

3. List all movies that were released on even number years

```
SELECT title, year
FROM movies
WHERE year % 2 = 0;
```

### SQL Lesson 10: Queries with aggregates (Pt. 1)

1. Find the longest time that an employee has been at the studio

```
SELECT name, MAX(years_employed)
FROM employees;
```

2. For each role, find the average number of years employed by employees in that role

```
SELECT role, AVG(years_employed)
FROM employees
GROUP BY role;
```

3. Find the total number of employee years worked in each building

```
SELECT building, SUM(years_employed)
FROM employees
GROUP BY building;
```

### SQL Lesson 11: Queries with aggregates (Pt. 2)

1. Find the number of Artists in the studio (without a HAVING clause)

```
SELECT COUNT(role)
FROM employees
WHERE role="Artist";
```

2. Find the number of Employees of each role in the studio

```
SELECT role, COUNT(role)
FROM employees
GROUP BY role;
```

3. Find the total number of years employed by all Engineers

```
SELECT role, SUM(Years_employed)
FROM employees
WHERE role="Engineer";

SELECT role, SUM(years_employed)
FROM employees
GROUP BY role
HAVING role = "Engineer";
```

### SQL Lesson 12: Order of execution of a Query

1. Find the number of movies each director has directed

```
SELECT director, COUNT(*)
FROM movies
GROUP BY director;
```

2. Find the total domestic and international sales that can be attributed to each director

```
SELECT director, SUM(domestic_sales) +  SUM(international_sales) as total
FROM movies INNER JOIN boxoffice ON movies.id=boxoffice.movie_id
GROUP BY director;
```

### SQL Lesson 13: Inserting rows

1. Add the studio's new production, Toy Story 4 to the list of movies (you can use any director)

```
INSERT INTO movies (title)
VALUES ("Toy Story 4");
```

2. Toy Story 4 has been released to critical acclaim! It had a rating of 8.7, and made 340 million domestically and 270 million internationally. Add the record to the BoxOffice table.

```
INSERT INTO boxoffice (movie_id, rating, domestic_sales, international_sales)
VALUES (4, 8.7, 340000000, 270000000);
```

### SQL Lesson 14: Updating rows

1. The director for A Bug's Life is incorrect, it was actually directed by John Lasseter

```
UPDATE movies
SET director = "John Lasseter"
WHERE title = "A Bug's Life";
```

2. The year that Toy Story 2 was released is incorrect, it was actually released in 1999

```
UPDATE movies
SET year = 1999
WHERE title = "Toy Story 2";
```

3. Both the title and director for Toy Story 8 is incorrect! The title should be "Toy Story 3" and it was directed by Lee Unkrich

```
UPDATE movies
SET title = "Toy Story 3", director = "Lee Unkrich"
WHERE title = "Toy Story 8";
```

### SQL Lesson 15: Deleting rows

1. This database is getting too big, lets remove all movies that were released before 2005.

```
DELETE
FROM movies
WHERE year < 2005;
```

2. Andrew Stanton has also left the studio, so please remove all movies directed by him.

```
DELETE
FROM movies
WHERE director = "Andrew Stanton";
```

### SQL Lesson 16: Creating tables

1. Create a new table named Database with the following columns:

– Name A string (text) describing the name of the database

– Version A number (floating point) of the latest version of this database

– Download_count An integer count of the number of times this database was downloaded

This table has no constraints.

```
CREATE TABLE Database (Name TEXT, Version FLOAT, Download_count INTEGER);
```

### SQL Lesson 17: Altering tables

1. Add a column named Aspect_ratio with a FLOAT data type to store the aspect-ratio each movie was released in.

```
ALTER TABLE movies
ADD Aspect_ratio FLOAT;
```

2. Add another column named Language with a TEXT data type to store the language that the movie was released in. Ensure that the default for this language is English.

```
ALTER TABLE movies
ADD Language TEXT DEFAULT "English";
```

### SQL Lesson 18: Dropping tables

1. We've sadly reached the end of our lessons, lets clean up by removing the Movies table

```
DROP TABLE IF EXISTS movies;
```

2. And drop the BoxOffice table as well

```
DROP TABLE IF EXISTS boxoffice;
```
