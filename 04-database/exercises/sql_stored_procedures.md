# PostgreSQL stored procedures

## Contents
<!-- MarkdownTOC -->

- What is a stored procedure?
- Basic structure of a PL/pgSQL function
  - Input and output arguments
  - Variables and constants
- Functions that doesn't return any row
- Function that returns a table
- Functions with Security Definer
- Trigger functions
- Control structures
  - IF statement
  - CASE statement
  - LOOP statement
  - WHILE loops
  - FOR loops
- Pl/pgSQL and PostGIS
- Exercises
  - Create a function that inserts a point into a table.
  - Create a trigger function that writes a string in a column each time a new point is inserted
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


## Exercises
### Create a function that inserts a point into a table.

* First, let's define the function. It should take Longitude and Latitude as parameters, as well as the table name where we want to insert the points. It should also return a table with `cartodb_id` that will be generated in the inserted row:

  ```
  CREATE OR REPLACE FUNCTION insertpoint(
          lon numeric,
          lat numeric,
    tablename text,
    )
  RETURNS TABLE(cartodb_id INT)
  LANGUAGE 'plpgsql' SECURITY DEFINER
  RETURNS NULL ON NULL INPUT
  ```

* This part specifies the language we are using and allows to grant specific permission to the function:

  ```sql
  LANGUAGE 'plpgsql' SECURITY DEFINER
  ```

* We need to declare a `text` variable that will store the function itself so we can execute it afterwards. We need to follow this logic in order to being able to use the table name and different column names as arguments/variables within the function.

  ```sql
  DECLARE
    sql text;
  ```

* This is the body of the function and where most of the logic relies:

  ```sql
  'WITH do_insert AS (
      INSERT INTO '||quote_ident(tablename)||'(the_geom)
      VALUES '
        ||'('
        ||'ST_SetSRID(ST_MakePoint('||lon||','||lat||'), 4326)'
        ||')'
      ||'RETURNING cartodb_id)'
  ||'SELECT cartodb_id FROM do_insert';
  ```
  Apart from the string-variable concatenation (`'string'||variable||'string'`) that may produce some confusing syntax, the query is quite straight forward.

  We are going to insert a geometry with its alphanumerical data, using the function arguments as values.

  The main difficulty here is correctly parsing the arguments so we don't duplicate, miss or change the expected quoting.

  The name of the table needs to be used inside the [`quote_ident()` function](https://www.postgresql.org/docs/9.5/static/functions-string.html). That means that it is going to be interpreted as an identifier for the table (double quoted), instead of as a plain string.

* We are wrapping the `INSERT INTO` query inside a `WITH do_insert AS` statement that returns `cartodb_id` for the newly inserted row. After that, we do a `SELECT cartodb_id FROM do_insert`, according with what our function returns:

  ```sql
  RETURNS TABLE(cartodb_id INT)
  ```

* The last step is executing the query we have dinamically crafted into the `sql` variable and returning its output:

  ```sql
  RETURN QUERY EXECUTE sql;
  ```

* Grant permission to the function

  In order to make the function executable for the `publicuser` with the same privileges as the function owner, we need to grant execution permissions to it:

  ```sql
  GRANT EXECUTE ON FUNCTION insertpoint(lon numeric, lat numeric, tablename text) TO publicuser;
  ```

  Remember that a function is defined by its name and input arguments.

* Calling the function:

  We could execute the function just by including it in a `SELECT` statement, providing the necessary parameters:

  ```sql
  SELECT insertpoint(-4.565,33.294,'tableName');
  ```

  A more orthodox way to call it, since the function returns a table would be:

  ```sql
  SELECT * FROM insertpoint(-4.565,33.294,'tableName');
  ```

* Revoking permission / removing the function

  If at some point we need to remove the function's privileges, we could run:

  ```sql
  REVOKE EXECUTE ON FUNCTION insertpoint(lon numeric, lat numeric, name text, description text, category text, tablename text) TO publicuser;
  ```

  For removing the function, we would run:

  ```sql
  DROP FUNCTION insertpoint(lon numeric, lat numeric, name text, description text, category text, tablename text)
  ```

### Create a trigger function that writes a string in a column each time a new point is inserted

* First, we'd need to create an easy SQL function that will update the table, writing a string into `column_name`

  ```sql
  CREATE OR REPLACE FUNCTION write_string()
  RETURNS TRIGGER
  AS $$
  BEGIN
    UPDATE dummy_dataset
       SET column_name = 'Point inserted at: '||
             ST_X(the_geom) || ',' || ST_Y(the_geom);
    RETURN null;
  END;
  $$
  LANGUAGE 'plpgsql';
  ```
* Next step would be creating the trigger itself for `table_name`

  ```sql
  CREATE TRIGGER writer
  AFTER INSERT ON table_name
  FOR EACH ROW EXECUTE PROCEDURE write_string();
  ```
* Finally, check that everything works. **Tip:** You could use this trigger function along with the function from the first exercise to create a complete automated workflow.

## Further reading

* This [tutorial](http://www.postgresqltutorial.com/postgresql-stored-procedures/) explains the basics of the PostgreSQL stored procedure language in a very friendly way. Many explanations from this document have been extracted from there.

* The [Pl/pgSQL section](http://www.postgresql.org/docs/9.4/static/plpgsql.html) on the PostgreSQL documentation is a complete and detailed resource by itself, read it to learn all about pl/pgSQL.

* Look and understand the different PostgreSQL/PostGIS functions that we have in the [cartodb-postgresql](https://github.com/CartoDB/cartodb-postgresql/tree/master/scripts-available) repo or other postgreSQL related repos (dataservices...).

* PostgreSQL, PostGIS blogs, etc; to find different PostGIS functions that you can build with pl/pgSQL. I would recommend writing ``Fun with PostGIS`` in Google (or Bing, Yahioo,etc) and have fun looking at what people do. :)

