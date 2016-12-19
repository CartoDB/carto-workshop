# PostgreSQL stored procedures

## Contents
<!-- MarkdownTOC -->

- What is a stored procedure?
- Basic structure of a PL/pgSQL function
- Functions that doesn't return any row
- Function that returns a table
- Functions with Security Definer
- Trigger functions
- Control structures
- Pl/pgSQL and PostGIS
- Exercise
- Further reading

<!-- /MarkdownTOC -->



## What is a stored procedure?

PostgreSQL allows you to extend the database functionality with user-defined functions by using various procedural languages, which are often referred to as stored procedures.

With stored procedures you can create your own custom functions and reuse them in applications or as part of other database's workflow.

## Basic structure of a PL/pgSQL function

```sql
CREATE FUNCTION function_name(argument1 type,argument2 type)
 RETURNS type AS
BEGIN
  staments;
END;
LANGUAGE 'language_name';
```

Where:

1. We specify the name of function followed by the ``CREATE FUNCTION``clause.
2. Provide a list of parameters inside the paretheses, also specifying each data type (integer, boolean, geometry, etc..)
3. ``RETURNS`` specifies the return type of the function.
4. Place the block of code inside inside the ``BEGIN`` and ``END;``.
5. Indicate the procedural language of the function. For PostgreSQL is usually ``plpgsql``.

To call the created function you could use:

```sql
SELECT * FROM function_name(val1,val2);
```
if the function returns a table, or just

```sql
SELECT function_name(val1,val2);
```

If one of the variables is taken from a column in a table:

```sql
SELECT function_name(val1,val2) FROM table;
```

### Input and output arguments

In the previous section we defined a function like

```sql
CREATE FUNCTION function_name(argument1 type,argument2 type)
 RETURNS type AS
BEGIN
  staments;
END;
LANGUAGE language_name;
```

Where we define the input arguments and their type and then we define the type of the output argument within the `RETURNS` statement.

However, there is another way to define the input and output arguments with `IN` and `OUT`, where `IN` define the input arguments and `OUT` the
output arguments. The `OUT` argument declaration is useful when you need to return multiple values. For example:

```sql
CREATE FUNCTION sum_n_product(IN x int,IN y int, OUT sum int, OUT prod int) AS $$
BEGIN
    sum := x + y;
    prod := x * y;
END;
$$ LANGUAGE plpgsql;
```

Then the function can be called as: ``SELECT * FROM sum_n_product(1,2)``, where you only need to pass the `IN` arguments to the function.

Notice that if we use IN and OUT arguments, we don't use the ``RETURNS`` statement in the function definition.

On the other hand, there are different ways to refer to the input arguments within the function.

1. If the input arguments have no names:

```sql
CREATE FUNCTION function_name(type,type)
 RETURNS type AS
BEGIN
  v1:= abs($1); -- $1 refers to the first input argument (reading from left to right)
  v2:= $2; -- $2 refers to the second input argument (reading from left to right)
END;
LANGUAGE 'plpgsql';
```

2. Using aliases for the input arguments.

We can assign alias to the input arguments.

```sql
CREATE FUNCTION function_name(type,type)
 RETURNS type AS
DECLARE
  val1 alias for $1;
  val2 alias for $2;
BEGIN
  v1:= abs($1); -- $1 refers to the first input argument (reading from left to right)
  v2:= $2; -- $2 refers to the second input argument (reading from left to right)
END;
LANGUAGE 'plpgsql';
```

### Variables and constants

A PL/pgSQL variable holds a values that can be changed through the function. A variables is always associated to a data type.
Before you use a variable we must declare it in the proper section, using the following syntax:

```sql
CREATE FUNCTION function_name(argument1 type,argument2 type)
 RETURNS type AS
 DECLARE
  variable_name data_type [:= value];
BEGIN
  staments;
END;
LANGUAGE language_name;
```

Constants can't be changed through the function. It is declared in a very similar way than variables.

```sql
CREATE FUNCTION function_name(argument1 type,argument2 type)
 RETURNS type AS
 DECLARE
  variable_name CONSTANT data_type [:= value];
BEGIN
  staments;
END;
LANGUAGE language_name;
```

## Functions that doesn't return any row

If we create a function that doesn't return any row, but performs a writing operation, such as an `UPDATE`, `INSERT` or `DELETE`, we need to set the `RETURNS TYPE` statement as `RETURNS void`

For example:

```sql
CREATE OR REPLACE FUNCTION logfunc1(logtxt text) RETURNS void AS $$
    BEGIN
      INSERT INTO untitled_table_1 (description,currenttime) VALUES (logtxt,'now');
    END;
$$ LANGUAGE plpgsql
```
We could call this function with `SELECT logfunc1('coolestWordEver')` and postgreSQL will return no rows, but if you go to your table you will see that there is a new row with values in `description` and `currentime` columns.

## Function that returns a table

To return a table from a PL/pgSQL function, we have to set `RETURNS TABLE (column1 type, column2 type, etc)` where `column1`, `column2` are the names of the columns that will be returned.

This example returns a frequency table from a specific column and specific table:

```sql
CREATE OR REPLACE FUNCTION get_freq_table()
RETURNS TABLE(
                    name VARCHAR,
               frequency INT,
         total_frequency INT,
      relative_frequency NUMERIC,
  sum_relative_frequency NUMERIC
)
AS $$
BEGIN
  RETURN QUERY
    WITH a as (
      SELECT DISTINCT adm0_a3, count(*) OVER (PARTITION BY adm0_a3) as freq,
             count(*) OVER() as total_freq
        FROM populated_places_spf
    ),
    b as(
      SELECT a.adm0_a3, a.freq, a.total_freq,
             round(
                (a.freq::numeric/a.total_freq::numeric),
                2
              ) as relative_freq
        FROM a
    )
    SELECT b.adm0_a3::VARCHAR,
           b.freq::INT,
           b.total_freq::INT,
           b.relative_freq::NUMERIC,
           sum(b.relative_freq)
             OVER(ORDER BY b.relative_freq)::NUMERIC as suma_rel_freq
      FROM b;
END;
$$ LANGUAGE 'plpgsql'
```

We could also modify the function to return a frequency table with a dynamic function:

```sql
CREATE OR REPLACE FUNCTION get_freq_table_general(col VARCHAR, tablName VARCHAR)
RETURNS TABLE(
                    name VARCHAR,
               frequency INT,
         total_frequency INT,
      relative_frequency NUMERIC,
  sum_relative_frequency NUMERIC
)
AS $$
BEGIN
  RETURN QUERY EXECUTE '
    WITH a as (
      SELECT DISTINCT '|| col || ' as colName, count(*) OVER (PARTITION BY '||
      col ||') as freq , count(*) OVER() as total_freq FROM '|| tablName ||'
    ),
    b as(
      SELECT a.colName,a.freq, a.total_freq,
             round(
               (a.freq::numeric/a.total_freq::numeric),
                2::integer
              ) as relative_freq
         FROM a)
    SELECT b.colName::VARCHAR, b.freq::INT, b.total_freq::INT,
           b.relative_freq::NUMERIC,
           sum(b.relative_freq)
             OVER(ORDER BY b.relative_freq)::NUMERIC as suma_rel_freq
      FROM b';
END;
$$ LANGUAGE 'plpgsql'
```

## Functions with Security Definer

In order to allow CARTO anonymous user (called `publicuser`) to perform writing operations such as `INSERT` or `UPDATE` queries, we need to define those actions inside a stored procedure. That function will afterwards be granted execution permission, so it will run with the same permission as the account owner.

This is an example of function that can be called to insert new points and alphanumerical data in a table:

```sql
CREATE OR REPLACE FUNCTION insertpoint(
          lon numeric,
          lat numeric,
         name text,
  description text,
     category text,
    tablename text
)
 RETURNS TABLE(cartodb_id INT)
LANGUAGE 'plpgsql' SECURITY DEFINER
 RETURNS NULL ON NULL INPUT
AS $$
DECLARE
  sql text;
BEGIN
  sql:= 'WITH do_insert AS (
        INSERT INTO '||quote_ident(tablename)||'(the_geom, name, description, category)
        VALUES '
          || '('
          || 'ST_SetSRID(ST_MakePoint('||lon||','||lat||'), 4326),'
          || quote_literal(name)||','
          || quote_literal(description)||','
          || quote_literal(category)
          || ')'
          ||'RETURNING cartodb_id)'
          ||'SELECT cartodb_id FROM do_insert';

RETURN QUERY EXECUTE sql;

END;
$$;

--Grant access to the public user
   GRANT EXECUTE ON
FUNCTION insertpoint(numeric, numeric, text, text, text, text)
      TO publicuser;
```
**[Click here](https://gist.github.com/ernesmb/beb25f539f8ff38bbd891e6d114ea7f4)** to find more detailed instructions for creating a Security Definer function that allows to collect points from a public map in a secure way

## Trigger functions

The trigger functions have three main properties:

1. They are immutable
2. The ``RETURNS`` statement returns a trigger type
3. They don't take any input argument

The trigger functions can be defines in two setps:

1- Define the trigger function:
```sql
CREATE OR REPLACE FUNCTION tr_function()
RETURNS TRIGGER
AS $$
BEGIN
  NEW.c3 = NEW.c1 + NEW.c2
  RETURN NEW
END;
$$
LANGUAGE 'plpgsql';
```

Where ``NEW`` is a variable that contains a row of the table.


2- Define when the trigger will be fired:

```sql
CREATE TRIGGER tr
BEFORE INSERT ON tableName
FOR EACH ROW EXECUTE
PROCEDURE tr_function();
```

Where:

* BEFORE (or AFTER): define **when** the trigger function will be activated (before or after the `INSERT` operation)
* FOR EACH ROW: the operation of the trigger function is executed once for each row.
* FOR EACH STATEMENT: the operation of the trigger function is executed once for each statement.


If we want to remove our triggers:

```sql
DROP TRIGGER triggerName ON tableName
```

or

```sql
DROP TRIGGER IF EXISTS triggerName ON tableName
```
## Control structures

### IF statement

The ``IF`` statement it is used to execute a command conditionally. PL/pgSQL has 3 ways to use the ``IF`` statements.

1. IF THEN
  ```sql
  IF condition THEN
    statement;
  END IF;
  ```
  The condition is a boolean expression that has to be evaluated as `true` or `false`, the statement will only be executed if the condition is `true`.

2. IF THEN/ELSE
  ```sql
  IF condition THEN
    statements;
  ELSE
    alternative-statements;
  END IF;
  ```
  The ``IF THEN/ELSE`` statement executes a command when the condition is `true` and it executes an alternative command when the condition is `false`.

3. IF THEN/ ELSIF THEN/ELSE

  ```sql
  IF condition-1 THEN
    if-statement;
  ELSIF condition-2 THEN
    elsif-statement-2
  ...
  ELSIF condition-n THEN
    elsif-statement-n;
  ELSE
    else-statement;
  END IF;
  ```
  The ``IF THEN/ ELSIF THEN/ELSE`` statement allows you to have multiple conditions to evaluate. In case one condition is `true`, PostgreSQL will stop evaluating the underneath conditions.

### CASE statement

This is similar to ``IF`` statement, but  the ``CASE`` operator allows you to execute statements based on conditions. There are two ways to apply a ``CASE``.

1. Simple CASE statement
  ```sql
  CASE search-expression
     WHEN expression_1 [, expression_2, ...] THEN
        when-statements
    [ ... ]
    [ELSE
        else-statements ]
  END CASE;
  ```
  The search-expression is an expression that will be evaluated against the expression in each `WHEN` branch using equality operand (`=`). If a match is found, the `when-statements` in the corresponding `WHEN` branch are executed. The subsequent expressions underneath will not be evaluated.

  Example:

  ```sql
  CREATE OR REPLACE FUNCTION get_price_segment(p_film_id integer)
     RETURNS VARCHAR(50) AS $$
  DECLARE
            rate NUMERIC;
   price_segment VARCHAR(50);
  BEGIN
     -- get the rate based on film_id
      SELECT INTO rate rental_rate
        FROM film
       WHERE film_id = p_film_id;

      CASE rate
        WHEN 0.99 THEN price_segment = 'Mass';
        WHEN 2.99 THEN price_segment = 'Mainstream';
        WHEN 4.99 THEN price_segment = 'High End';
        ELSE price_segment = 'Unspecified';
      END CASE;

      RETURN price_segment;
  END; $$
  LANGUAGE plpgsql;
  ```

2. Searched CASE statement

  ```sql
  CASE
      WHEN boolean-expression-1 THEN statements
      [ WHEN boolean-expression-2 THEN statements ... ]
      [ ELSE statements ]
  END CASE;
  ```

  The searched `CASE` statement executes statements based on the result of Boolean expressions in each `WHEN` clause.

### LOOP statement

This is used to execute a block of statements repeatedly until a condition becomes true. The basic syntax is:

```sql
LOOP
   Statements;
   EXIT [<<label>>] WHEN condition;
END LOOP;
```

Example:

```sql
CREATE OR REPLACE FUNCTION fibonacci (n INTEGER)
 RETURNS INTEGER AS $$
DECLARE
   counter INTEGER := 0 ;
         i INTEGER := 0 ;
         j INTEGER := 1 ;
BEGIN

 IF (n < 1) THEN
   RETURN 0 ;
 END IF;

 LOOP
   EXIT WHEN counter = n ;
   counter := counter + 1 ;
   SELECT j, i + j INTO i, j ;
 END LOOP ;

 RETURN i ;
END ;
$$ LANGUAGE plpgsql;
```

The Fibonacci function accepts an integer and returns the Nth Fibonacci number. By definition, Fibonacci numbers are sequence of integers starting with 0 and 1, and each subsequent number is the product the previous two numbers, for example 1, 1, 2 (1+1), 3 (2+1), 5 (3 +2), 8 (5+3), …

In the declaration section, the counter variable is initialized to zero (0). Inside the loop, when counter equals `n`, the loop exits. The statement:

```sql
SELECT j, i + j INTO i, j ;
```

Swaps `i` and `j` at the same time without using a temporary variable.

### WHILE loops

The `WHILE` loop statement executes a block of statements until a condition evaluates to false. In the `WHILE` loop statement, PostgreSQL evaluates the condition before executing the block of statements. If the condition is `true`, the block of statements is executed until it is evaluated to false.


```sql
CREATE OR REPLACE FUNCTION fibonacci (n INTEGER)
 RETURNS INTEGER AS $$
DECLARE
   counter INTEGER := 0 ;
         i INTEGER := 0 ;
         j INTEGER := 1 ;
BEGIN

 IF (n < 1) THEN
   RETURN 0 ;
 END IF;

 WHILE counter <= n LOOP
   counter := counter + 1 ;
   SELECT j, i + j INTO i, j ;
 END LOOP ;

 RETURN i ;
END ;
```
### FOR loops

* `FOR` loop for looping through a ranges of integers
```sql
FOR loop_counter IN [ REVERSE ] from.. to [ BY expression ] LOOP
    statements
END LOOP [ label ];
```

First, PostgreSQL creates a integer variable `loop_counter` that exists only inside the loop. By default, the loop counter is added after each iteration, If you use the `REVERSE` keyword, PostgreSQL will subtract the loop counter.

Second, the `FROM` and `TO` are expressions that specify the lower and upper bounds of the range. PostgreSQL evaluates those expressions before entering the loop.

Third, the expression following the `BY` clause specifies the iteration step. If you omit this, the default step is 1. PostgreSQL also evaluates this expression once on loop entry


*  `FOR` loop for looping through a query result

```sql
FOR target IN query LOOP
    statements
END LOOP [ label ];
```

* `FOR` loop for looping through a query result of a dynamic query

```sql
FOR row IN EXECUTE string_expression [ USING query_param [, ... ] ]
LOOP
    statements
END LOOP [ label ];
```

## Pl/pgSQL and PostGIS

It's possible to create Pl/pgSQL functions with PostGIS to predefine a process to modify the geometries of some data. For example, based on [this
example](https://selectoid.wordpress.com/2008/12/22/developing-fun-st_staratpoint-for-postgis/) that creates star polygons from points (read it !! it's detailed and fun), we can create a function like this one:

```sql
CREATE OR REPLACE FUNCTION regularPolygons(
           IN center geometry,
           IN radius numeric,
  IN number_of_sides integer
) RETURNS GEOMETRY AS
$$
DECLARE
  -- the geometry as WKT
  geometry_wkt text := '';
  -- a loop counter
  i integer := 0;
  -- angle to rotate
  angle numeric;
  -- reference line to rotate around
  ref_line geometry;
BEGIN
  -- create reference line to rotate the points
  -- it creates a 12 o'clock lines from the center
  ref_line = ST_MakeLine(
              center,
              ST_Transform(
                ST_SetSRID(
                  ST_MakePoint(ST_X(center),ST_Y(center) + radius),
                  3857)
                ,4326));

  -- calculate angle depending ong number of sides of the
  angle = radians(360::numeric/number_of_sides::numeric);

  -- Start loop to create polygon using the ref_line
  -- and the declared variable i
  WHILE (i < number_of_sides) LOOP
    geometry_wkt = geometry_wkt ||
      (ST_X(ST_EndPoint(ST_RotateZ(ref_line, angle * i))) +
        ST_X(center)) || '  ' ||
      (ST_Y(ST_EndPoint(ST_RotateZ(ref_line, angle * i))) +
        ST_Y(center)) || ',';

    -- increment counter
    i = i + 1;

  END LOOP;

  -- add the first point again so the linestring is closed
  geometry_wkt = geometry_wkt ||
    (ST_X(ST_EndPoint(ref_line)) + ST_X(center))  || ' ' ||
    (ST_Y(ST_EndPoint(ref_line)) + ST_Y(center));

  -- Transform  pair of points into a polygon geometry
  geometry_wkt = 'POLYGON(('|| geometry_wkt ||'))';

  RETURN geometry_wkt::geometry;
END;
$$ LANGUAGE plpgsql;
```

The ``regularPolygons()`` function takes three input arguments: the points that you want to use as a center of the output polygons, length of the radius and number of sides of the output polygon.

Using this function like this

```sql
SELECT regularPolygons(the_geom,100000,6), adm0_a3
  FROM populated_places_spf
 WHERE adm0_a3 = 'PRT' UNION ALL
SELECT regularPolygons(the_geom,100000,4), adm0_a3
  FROM populated_places_spf
 WHERE adm0_a3 = 'ESP' UNION ALL
SELECT regularPolygons(the_geom,100000,3), adm0_a2
  FROM populated_places_spf
 WHERE adm0_a3 = 'FRA'
```

We can create a different way to classify our point data like in [this example](https://team.cartodb.com/u/oboix/viz/63e74f10-27d7-11e6-bad6-0e3ff518bd15/public_map).


## Exercise

### Create a stored procedure + trigger workflow to store changes from any of your tables to a log table

First, we need to create the log table and cartodbfy it. 

```sql
CREATE TABLE version_control();
SELECT CDB_CartodbfyTable('username','version_control');
```

Cartodbfication will add `the_geom`, `the_geom_webmercator' and 'cartodb_id' columns to the table, as well as some triggers to populate these columns automatically when a new row is added (we'll see more about this at the end of the exercise). 

Next step would be adding some extra columns to the log table.

```sql
ALTER TABLE version_control ADD COLUMN data json; -- to store the complete row in JSON format
ALTER TABLE version_control ADD COLUMN source_id integer; -- cartodb_id of the row
ALTER TABLE version_control ADD COLUMN table_name text; -- table name 
ALTER TABLE version_control ADD COLUMN tg_op text; -- writing operation that was performed
ALTER TABLE version_control ADD COLUMN logged_at timestamp; -- timestamp for the operation
```

And create the function itself: 

```sql
CREATE OR REPLACE FUNCTION carto_version_control() RETURNS TRIGGER AS $$
    BEGIN
        IF (TG_OP = 'DELETE') THEN
            INSERT INTO version_control(the_geom, tg_op, data, source_id, table_name, logged_at) 
              SELECT OLD.the_geom, 'DELETE', row_to_json(OLD), OLD.cartodb_id, TG_TABLE_NAME::text, now();
            RETURN OLD;
        ELSIF (TG_OP = 'UPDATE') THEN
            INSERT INTO version_control(the_geom, tg_op, data, source_id, table_name, logged_at)
              SELECT NEW.the_geom, 'UPDATE', row_to_json(NEW), NEW.cartodb_id, TG_TABLE_NAME::text, now();
            RETURN NEW;
        ELSIF (TG_OP = 'INSERT') THEN
            INSERT INTO version_control(the_geom, tg_op, data, source_id, table_name, logged_at) 
              SELECT NEW.the_geom, 'INSERT', row_to_json(NEW), NEW.cartodb_id, TG_TABLE_NAME::text, now();
            RETURN NEW;
        END IF;
        RETURN NULL; 
    END;
$$ LANGUAGE plpgsql;
```

Once we've created the function, we need to add a trigger to any table we want to audit:

```sql
CREATE TRIGGER carto_version_trigger
AFTER UPDATE OR DELETE OR INSERT ON dummy_dataset
    FOR EACH ROW EXECUTE PROCEDURE version_control();
```

The trigger will be fired after each `UPDATE`, `DELETE` or `INSERT` operation that is made to `dummy_dataset` **(remember to update the trigger with the proper table name)**. Then the `carto_version_control()` function will make an `INSERT` to `version_control` table, adding a row with data coming from the operation itself. 

Each `control_version` row will log: 

- The deleted/updated/inserted geometry
- The name of the operation that was made
- The whole modified row as a JSON object
- `cartodb_id` from the modified row
- The name of the table that was affected by the operation (useful in case we add the trigger to several different tables)
- Timestamp of when the trigger was fired

**NOTE:** After each `INSERT` that is made to a cartodbfied table, there is a subsequent `UPDATE` that is triggered to populate either `the_geom` or `the_geom_webmercator` as well as `cartodb_id`. 

This is due to the triggers that are added to every table as part of the `CDB_CartodbfyTable()` proccess, and will result in almost identical rows in our `version_control` table that could help us understand what does it means to 'cartodbfy a table'.

To retrieve information from `version_control` table, we could run a query like: 

```sql
SELECT (json_populate_record(null::dummy_dataset, data)).* FROM version_control LIMIT 1
```

Which will generate a row from the JSON `data` column in `dummy_dataset` table. We could also apply a filter to get more relevant information, for example: 

```sql
SELECT (json_populate_record(null::dummy_dataset, data)).* FROM version_control WHERE table_name LIKE 'dummy_dataset'
```

to get a row for each modified row in `dummy_dataset` table.


## Further reading

* This [tutorial](http://www.postgresqltutorial.com/postgresql-stored-procedures/) explains the basics of the PostgreSQL stored procedure language in a very friendly way. Many explanations from this document have been extracted from there.

* The [Pl/pgSQL section](http://www.postgresql.org/docs/9.4/static/plpgsql.html) on the PostgreSQL documentation is a complete and detailed resource by itself, read it to learn all about pl/pgSQL.

* Look and understand the different PostgreSQL/PostGIS functions that we have in the [cartodb-postgresql](https://github.com/CartoDB/cartodb-postgresql/tree/master/scripts-available) repo or other postgreSQL related repos (dataservices...).

* PostgreSQL, PostGIS blogs, etc; to find different PostGIS functions that you can build with pl/pgSQL. I would recommend looking for ``Fun with PostGIS`` and have some fun looking at what people do :)
