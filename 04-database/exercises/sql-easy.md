
#  Simple SQL operations

When doing maps it's a good idea to introduce you to a bit of the query language we use to render our maps. SQL is a language widely used to query relational databases and actually a powerful tool to analyze your data.

CARTO allows you to interact with your datasets using the interface so you can filter, order and modify your data values directly from BUILDER. Sometimes it will be useful to use the SQL view to perform more advanced tasks like formatting your data joining different tables.

This section will cover the basics of SQL and is meant to be exercised using either the procedure outlined in the [frontpage](../README.md#set-up) of this module or the CARTO DATA tab SQL view, where you can run queries and see the results immediately. If you don't have it yet, import from CARTO Data Library the `ne_10m_populated_places_simple` and `world_borders`datasets so you can follow all these queries.

## Contents

<!-- MarkdownTOC -->

- Selecting all columns
- Selecting some columns
- Selecting distinct values
- Filtering numeric fields
- Filtering character fields
- Filtering a range
- Combining character and numeric filters
- Ordering results
- Limiting results
- Making calculations
- Joining and grouping datasets
- Other aggregation functions

<!-- /MarkdownTOC -->


## Selecting all columns

The most basic query to a table is requesting all rows and columns.

```sql
SELECT *
  FROM ne_10m_populated_places_simple;
```

## Selecting some columns

Sometimes we don't need all the columns of a table so we can select just some of them by putting their names. This is specially useful when you have big tables. Note also that we can change the name of the columns using aliases.

```sql
  SELECT cartodb_id,
         name AS city,
         adm1name AS region,
         adm0name AS country,
         pop_max,
         pop_min
    FROM ne_10m_populated_places_simple
```

## Selecting distinct values

If for any reason you want to know the domain of values that of a field use the `DISTINCT` keyword.

```sql
SELECT DISTINCT featurecla
  FROM ne_10m_populated_places_simple
```

## Filtering numeric fields

Filtering is a common operation when working with CARTO. With the following examples you'll see how to subset your table according to different criteria.

You can use the `>`, `<`, `=`, `!=` operators to restrict a numeric or a date field.


```sql
SELECT name, pop_max
  FROM ne_10m_populated_places_simple
 WHERE pop_max > 5000000
```

## Filtering character fields

Even you can use `=` with text fields, is more convenient to use `LIKE` or even better `ILIKE`. The former will do a case-insesitive search.

```sql
SELECT name, adm0name
  FROM ne_10m_populated_places_simple
 WHERE adm0name ilike 'spain'
```

## Filtering a range

If you want to filter by several values you can use the `IN` keyword and pass a list of values between parenthesis and comma separated.

```sql
  SELECT *
    FROM ne_10m_populated_places_simple
   WHERE name IN ('Madrid', 'Barcelona')
     AND adm0name ilike 'spain'
```

## Combining character and numeric filters

Filters can be combined using the `AND`, `OR` and `NOT` keywords. If you have doubts about the operator precedence is always good idea to use parenthesis to make explicit your conditions.

```sql
SELECT *
  FROM ne_10m_populated_places_simple
 WHERE name IN ('Madrid', 'Barcelona')
   AND adm0name ilike 'spain'
   AND pop_max > 5000000
```

## Ordering results

Even you can order the results on BUILDER, sometimes it's useful to order explicitly the results of your query by some field. By default `ORDER` works in ascending order (`ASC`) so you don't need to specify it.

```sql
  SELECT cartodb_id,
         name AS city,
         adm1name AS region,
         adm0name AS country,
         pop_max
    FROM ne_10m_populated_places_simple
   WHERE adm0name ilike 'spain'
ORDER BY pop_max DESC
```

## Limiting results

If your data is ordered, then you can limit the number of results to retrieve for example the top ten municipalities of Spain by population where Spanish PP party won.

```sql
  SELECT cartodb_id,
         name AS city,
         adm1name AS region,
         adm0name AS country,
         pop_max
    FROM ne_10m_populated_places_simple
   WHERE adm0name ilike 'spain'
ORDER BY pop_max DESC
   LIMIT 10
```

## Making calculations

You can make calculations and run functions on your query `SELECT` part and also on the `WHERE` section. This way you can compute densities, normalize columns, format dates and numbers, etc. Next example shows how to get the 20 most densified countries.

```sql
WITH data AS (
  SELECT *,
         pop2005/ST_Area(the_geom::geography) * 1e6 AS pop_km2
    FROM world_borders
)
  SELECT *
    FROM data
ORDER BY pop_km2 DESC
```

More about mathematical functions [here](https://www.postgresql.org/docs/9.1/static/functions-math.html).

Note also the use of the `WITH` clause that creates a *virtual named table* that is afterwards referenced on the final `SELECT`. This is known as a *Common Table Expression* and you can find more about it at the [official documentation](https://www.postgresql.org/docs/9.5/static/queries-with.html).

## Joining and grouping datasets

It's very common to have different datasets that we need to join to produce a map. Typically we have a geographic reference dataset and we need to join it with some business data. To do so we use the `JOIN` clause where we refer to another table and make explicit the condition to join fields from one table to the other, normally using a common field or like in this case a spatial relation (don't worry about that `ST_Intersects` function as for now).

Our example relates cities and countries, so we have a **one to many** relationship because we are counting cities inside every country and summing their population. To make the aggregation we group by country name. Finally on our example we order and limit the results to get the 10 countries with the highest sum of population on their main cities.

```sql
SELECT w.*,
       count(pp.*) AS places,
       sum(pp.pop_max)  AS cities_pop
  FROM world_borders AS w
  JOIN ne_10m_populated_places_simple AS pp
    ON ST_Intersects(w.the_geom,pp.the_geom)
 GROUP BY w.cartodb_id
 ORDER BY cities_pop DESC
 LIMIT 10
```

Note the use of alias for tables and how they are used at the `SELECT` section.

Note also the `GROUP BY`. Normally we should put all the `world_borders` fields but as the `cartodb_id` field is a *Primary Key* we don't need to do so.

## Other aggregation functions

There are other functions you can apply to your columns. For example you can compute aggregated functions to get the maximum, minimum and average values for a column.

```sql
SELECT count(*) AS counts,
       max(pop_max) AS max_pob,
       min(pop_max) AS min_pob,
       avg(pop_max) AS avg_pob
  FROM ne_10m_populated_places_simple;
```

`ROUND` and `TRUNC` will convert float numbers into integers, the first rounding to the nearest one. `ROUND` can also accept a second parameter to round to a specific decimal position. `TO_CHAR` is a more complex function that can be used to format numbers and dates into strings with decimal and thousand separators, any arbitrary date format, etc.

```sql
SELECT round(1.9) AS rounded,
       round(1.193,1) AS rounded2,
       trunc(1.9) AS truncated,
       to_char(12345.9332,'999,999.99') AS formatted,
       to_char(now(),'Day DD/MM/YY HH:mm') AS today;10:06:32' */
```

More about the `TO_CHAR` function [here](https://www.postgresql.org/docs/9.5/static/functions-formatting.html).