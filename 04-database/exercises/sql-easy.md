
#  Simple SQL operations

## Selecting all columns:

```sql
SELECT
  *
FROM
  ne_10m_populated_places_simple;
```

## Selecting some columns:

```sql
SELECT
  cartodb_id,
  name as city,
  adm1name as region,
  adm0name as country,
  pop_max,
  pop_min
FROM
  ne_10m_populated_places_simple
```

## Selecting distinct values:

```sql
SELECT DISTINCT
  adm0name as country
FROM
  ne_10m_populated_places_simple
```

### 2.4 Filtering

![filtering](../img/common/filtering.png)

## Filtering numeric fields:

```sql
SELECT
  *
FROM
  ne_10m_populated_places_simple
WHERE
  pop_max > 5000000;
```

## Filtering character fields:

```sql
SELECT
  *
FROM
  ne_10m_populated_places_simple
WHERE
  adm0name ilike 'spain'
```

## Filtering a range:

```sql
SELECT
  *
FROM
  ne_10m_populated_places_simple
WHERE
  name in ('Madrid', 'Barcelona')
AND
  adm0name ilike 'spain'
```

## Combining character and numeric filters:

```sql
SELECT
  *
FROM
  ne_10m_populated_places_simple
WHERE
  name in ('Madrid', 'Barcelona')
AND
  adm0name ilike 'spain'
AND
  pop_max > 5000000
```

### 2.5 Others:

## Selecting aggregated values:

**count**

```sql
SELECT
  count(*) as total_rows
FROM
  ne_10m_populated_places_simple
```
**sum**

```sql
SELECT
  sum(pop_max) as total_pop_spain
FROM
  ne_10m_populated_places_simple
WHERE
  adm0name ilike 'spain'
```
**avg**

```sql
SELECT
  avg(pop_max) as avg_pop_spain
FROM
  ne_10m_populated_places_simple
WHERE
  adm0name ilike 'spain'
```

## Ordering results:

```sql
SELECT
  cartodb_id,
  name as city,
  adm1name as region,
  adm0name as country,
  pop_max
FROM
  ne_10m_populated_places_simple
WHERE
  adm0name ilike 'spain'
ORDER BY
  pop_max DESC
```

## **Limiting results**:

```sql
SELECT
  cartodb_id,
  name as city,
  adm1name as region,
  adm0name as country,
  pop_max
FROM
  ne_10m_populated_places_simple
WHERE
  adm0name ilike 'spain'
ORDER BY
  pop_max DESC
LIMIT 10
```
