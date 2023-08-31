---
title: "Learning SQL the Badass Way III"
author: "Edgar Treischl"
date: 2021-10-09
diagram: FALSE
categories: ["SQL"]
---







Do you really want to "learn SQL the badass way"? I outlined the scope of the blog in the first blog entry where I explained why I set the scope to R users and people with some programming languages. You may not find what you are looking for if you are not familiar with R or if you do not have any other experience to work with data, because I focus on the data management part in this blog. Thus, I hope you find some helpful ressources to learn SQL, but I focus on data wrangling aspects, without explaining basic concepts to handle data.


In this session we learn how to use string patterns and ranges to search data. We will learn how to sort and group data to display result. Moreover, we practice composing nested queries and execute select statements to access data from multiple tables. For this reason I created a simple table that contains some attributes (y, z, id) about countries: 


```{.sql .klippy}
SELECT * FROM df ;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-1"></span>Table 1: 4 records

|country |  y|   z| id|
|:-------|--:|---:|--:|
|Germany |  2| 3.6|  1|
|Austria |  1| 8.5|  2|
|Brazil  |  4| 2.5|  3|
|Brazil  |  3| 3.5|  3|

</div>


# String values, ranges and set of values

We can use strings and the WHERE clause to search for specific observations. For instance,  `WHERE countryname LIKE 'A%'` means that we search for country name column that start with the corresponding string. And we can use `%` as a wildcard character:


```{.sql .klippy}
SELECT * FROM df WHERE country LIKE 'A%';
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-2"></span>Table 2: 1 records

|country |  y|   z| id|
|:-------|--:|---:|--:|
|Austria |  1| 8.5|  2|

</div>



Use a range to select entries that depending on some criteria (> and <). In SQL, we specify `WHERE values are between 100 and 200`. Keep in mind that values are inclusive within the range. For instance, we can use the mtcars dataset and restrict the table with cars that have a horsepower (hp) between 100 and 200, we can even use an `AND` to restrict to cars with a manual transmission (AM = 1)



```{.sql .klippy}
select * from mtcars
where (hp BETWEEN 100 and 200) and AM = 1 ;

```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-3"></span>Table 3: 5 records

|  mpg| cyl|  disp|  hp| drat|    wt|  qsec| vs| am| gear| carb|
|----:|---:|-----:|---:|----:|-----:|-----:|--:|--:|----:|----:|
| 21.0|   6| 160.0| 110| 3.90| 2.620| 16.46|  0|  1|    4|    4|
| 21.0|   6| 160.0| 110| 3.90| 2.875| 17.02|  0|  1|    4|    4|
| 30.4|   4|  95.1| 113| 3.77| 1.513| 16.90|  1|  1|    5|    2|
| 19.7|   6| 145.0| 175| 3.62| 2.770| 15.50|  0|  1|    5|    6|
| 21.4|   4| 121.0| 109| 4.11| 2.780| 18.60|  1|  1|    4|    2|

</div>


Another option gives us the `IN` operator. We can select columns by providing a list and the `IN` operator. As the next example shows, we select only those observations that match the provided list of the `IN` operator:


```{.sql .klippy}
SELECT * FROM df WHERE country IN ('Brazil');
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-4"></span>Table 4: 2 records

|country |  y|   z| id|
|:-------|--:|---:|--:|
|Brazil  |  4| 2.5|  3|
|Brazil  |  3| 3.5|  3|

</div>

  
  
## Sorting Result Sets

Sometimes we need to sort the entries alphabetically and we can do that with the `ORDER BY` clause. For instance, `ORDER BY country`:


```{.sql .klippy}
SELECT * FROM df ORDER BY country;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-5"></span>Table 5: 4 records

|country |  y|   z| id|
|:-------|--:|---:|--:|
|Austria |  1| 8.5|  2|
|Brazil  |  4| 2.5|  3|
|Brazil  |  3| 3.5|  3|
|Germany |  2| 3.6|  1|

</div>


- By default, the entries are ordered in an ascending order, but we can sort in a descending with `DESC` option as well:


```{.sql .klippy}
SELECT * FROM df ORDER BY country DESC;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-6"></span>Table 6: 4 records

|country |  y|   z| id|
|:-------|--:|---:|--:|
|Germany |  2| 3.6|  1|
|Brazil  |  4| 2.5|  3|
|Brazil  |  3| 3.5|  3|
|Austria |  1| 8.5|  2|

</div>

Sometimes we have several observations per unit or any kind of structural order, which is why we may want to order a specific variable. We can sort the data by providing the number of the column we want to use a sort. As the next example shows, we can use y (or 2) to sort the data:


```{.sql .klippy}
SELECT * FROM df ORDER BY y;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-7"></span>Table 7: 4 records

|country |  y|   z| id|
|:-------|--:|---:|--:|
|Austria |  1| 8.5|  2|
|Germany |  2| 3.6|  1|
|Brazil  |  3| 3.5|  3|
|Brazil  |  4| 2.5|  3|

</div>


## Grouping result sets

To work with data, we have to get rid of duplicates and often it is much more easier if we restrict result set (data frame). To exclude duplicates we can use the `distinct()` command, which returns only distinct countries in our example:


```{.sql .klippy}
SELECT distinct(country) FROM df ;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-8"></span>Table 8: 3 records

|country |
|:-------|
|Germany |
|Austria |
|Brazil  |

</div>

In a similar fashion, maybe we have to clarify how many observations do we have per group? Or in our case, how many entries come from the same country and how often appears each level?
In such a case we can count the county column and use the `group by` clause:


```{.sql .klippy}
SELECT country, count (country) from df group by country;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-9"></span>Table 9: 3 records

|country | count (country)|
|:-------|---------------:|
|Austria |               1|
|Brazil  |               2|
|Germany |               1|

</div>


As the last output showed, the count functions literally counts, but SQL does not give it a name, it simply displays what it does. We can change this ugly behaviour by providing a variable name and tell SQL how the column should be listed.  


```{.sql .klippy}
SELECT country, count (country) AS Count_Variable from df group by country;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-10"></span>Table 10: 3 records

|country | Count_Variable|
|:-------|--------------:|
|Austria |              1|
|Brazil  |              2|
|Germany |              1|

</div>

Certainly, counting is not the only function. We can estimate the mean average with `AVG()`. And now the average, little SQL monkey!


```{.sql .klippy}
SELECT country, AVG(z) as Mean from df group by country;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-11"></span>Table 11: 3 records

|country | Mean|
|:-------|----:|
|Austria |  8.5|
|Brazil  |  3.0|
|Germany |  3.6|

</div>


We can set a further conditions with a grouped by clause and add the `HAVING` option. As the next output shows, the `group by country HAVING count (country) > 1` returns only countries with more than one observation counted:


```{.sql .klippy}
SELECT country, count (country) AS Count from df group by country having count (country) > 1;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-12"></span>Table 12: 1 records

|country | Count|
|:-------|-----:|
|Brazil  |     2|

</div>


Let us try to remember that the `WHERE`clause is for entire result set; while `HAVING` works only for the `GROUPED BY` clause.


Congratulations! At this point you are able to:

- Use the `WHERE` clause to refine your query results
- Use the wildcard character (`%`) as a substitute for unknown characters in a pattern
- Use `BETWEEN` ... `AND`  to specify a range of numbers
- We can sort query results into ascending or descending order, by using the `ORDER BY` clause
- And we can group query results by using the `GROUP BY` clause. 


## Built-in Database Functions

We saw in the last section that we can aggregate (count, avg) data and use column functions. Most of the basic statistics functions (sum, avg, min, max) are available and we can specify further conditions, for instance, if we want to summarize groups:


```{.sql .klippy}
select sum(mpg) as sum_mpg from mtcars where hp > 100
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-13"></span>Table 13: 1 records

| sum_mpg|
|-------:|
|   401.4|

</div>

Or we may use the scalar function and round to the nearest integer:


```{.sql .klippy}
select round(drat, 1) as round_drat from mtcars
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-14"></span>Table 14: Displaying records 1 - 10

| round_drat|
|----------:|
|        3.9|
|        3.9|
|        3.9|
|        3.1|
|        3.2|
|        2.8|
|        3.2|
|        3.7|
|        3.9|
|        3.9|

</div>

In SQL, there is a class of scalar functions. For instance, we can calculate the length of a string:



```{.sql .klippy}
select length(country) from df
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-15"></span>Table 15: 4 records

| length(country)|
|---------------:|
|               7|
|               7|
|               6|
|               6|

</div>

Depending the SQL database you use, in db2 you can use the upper (UCASE) and lower case (LCASE) function for strings.



```{.sql .klippy}
select upper(country) from df
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-16"></span>Table 16: 4 records

|upper(country) |
|:--------------|
|GERMANY        |
|AUSTRIA        |
|BRAZIL         |
|BRAZIL         |

</div>

In case of Oracle the functions are called lower and upper. 


## Date and Time Built-in Functions 

Talking about SQL databases, there are three different possibilities to work with date and time DB2.

  - Date: *YYYYMMDD* (Year/Month/Day)
  
  - Time: *HHMMSS* (Hours/Min/Sec)
  
  - Timestamp: *YYYYMMDDHHMMSSZZZZZZ* (Date/Time/Microseconds)
  
Depending on what you are up to do, there are functions to extract the day, month, day of month, day of week, day of year, week, hour, minute, and second. You can also extract the `current_date` and the `current_time`. Unfortunately, this does not work in Oracle the same way as in DB2, but to give you an example how to extract the day:



```{.sql .klippy}
select day(date) from df where country = 'Germany'
```

## Sub-Queries and Nested Selects

Sub-queries or sub selects are like regular queries but placed within parentheses and nested inside another query. This allows you to form more powerful queries than would have been otherwise possible. An example:


```{.sql .klippy}
select avg(mpg) from mtcars
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-18"></span>Table 17: 1 records

| avg(mpg)|
|--------:|
| 20.09062|

</div>

Let's say we want to select only the observations with higher values than the average of mpg:


```{.sql .klippy}
select * from mtcars where mpg > avg(mpg)
```

This would produce the following error: `misuse of aggregate function avg() Failed to execute SQL chunk`. One of the limitations of built in aggregate functions, like avg(), is that they cannot be evaluated in the WHERE clause always. Thus, we have to use sub-queries.


```{.sql .klippy}
select * from mtcars where mpg >
  (select avg(mpg) from mtcars);
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-20"></span>Table 18: Displaying records 1 - 10

|  mpg| cyl|  disp|  hp| drat|    wt|  qsec| vs| am| gear| carb|
|----:|---:|-----:|---:|----:|-----:|-----:|--:|--:|----:|----:|
| 21.0|   6| 160.0| 110| 3.90| 2.620| 16.46|  0|  1|    4|    4|
| 21.0|   6| 160.0| 110| 3.90| 2.875| 17.02|  0|  1|    4|    4|
| 22.8|   4| 108.0|  93| 3.85| 2.320| 18.61|  1|  1|    4|    1|
| 21.4|   6| 258.0| 110| 3.08| 3.215| 19.44|  1|  0|    3|    1|
| 24.4|   4| 146.7|  62| 3.69| 3.190| 20.00|  1|  0|    4|    2|
| 22.8|   4| 140.8|  95| 3.92| 3.150| 22.90|  1|  0|    4|    2|
| 32.4|   4|  78.7|  66| 4.08| 2.200| 19.47|  1|  1|    4|    1|
| 30.4|   4|  75.7|  52| 4.93| 1.615| 18.52|  1|  1|    4|    2|
| 33.9|   4|  71.1|  65| 4.22| 1.835| 19.90|  1|  1|    4|    1|
| 21.5|   4| 120.1|  97| 3.70| 2.465| 20.01|  1|  0|    3|    1|

</div>

Colum expressions help to set sub queries as a list of columns. Say we select variable Z:


```{.sql .klippy}
select country, z from df
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-21"></span>Table 19: 4 records

|country |   z|
|:-------|---:|
|Germany | 3.6|
|Austria | 8.5|
|Brazil  | 2.5|
|Brazil  | 3.5|

</div>

And in the next step we add the average of all countries:


```{.sql .klippy}
select country, z, avg(z) as avg_Z from df
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-22"></span>Table 20: 1 records

|country |   z| avg_Z|
|:-------|---:|-----:|
|Germany | 3.6| 4.525|

</div>

This is obviously wrong. We cannot calculate on micro and macro level the same time, but we could use a sub-query (also called table expressions) to achieve it:


```{.sql .klippy}
select country, z, (select avg(z) from df group by country) as avg_Z from df
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-23"></span>Table 21: 4 records

|country |   z| avg_Z|
|:-------|---:|-----:|
|Germany | 3.6|   8.5|
|Austria | 8.5|   8.5|
|Brazil  | 2.5|   8.5|
|Brazil  | 3.5|   8.5|

</div>

Sub-queries can also be applied in the from clause. They are called derived tables or table expressions, because the outer query uses the results of the sub-query as a data source


```{.sql .klippy}
select * from (select hp, vs from mtcars);
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-24"></span>Table 22: Displaying records 1 - 10

|  hp| vs|
|---:|--:|
| 110|  0|
| 110|  0|
|  93|  1|
| 110|  1|
| 175|  0|
| 105|  1|
| 245|  0|
|  62|  1|
|  95|  1|
| 123|  1|

</div>


## Working with Multiple Tables

There are several ways to access multiple tables in the same query. Namely, using sub-queries, implicit join, and join operators, such as `INNER JOIN` and `OUTER JOIN`. For instance:


```{.sql .klippy}
select * from df2;
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-25"></span>Table 23: 2 records

|country | valid| id|
|:-------|-----:|--:|
|Germany |     1|  1|
|Austria |     0|  2|

</div>

Let's say we want only observations from df that are listed in df2. In such a situation we can use a sub-queries:


```{.sql .klippy}
select * from df
  where country in
  (select country from df2)
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-26"></span>Table 24: 2 records

|country |  y|   z| id|
|:-------|--:|---:|--:|
|Germany |  2| 3.6|  1|
|Austria |  1| 8.5|  2|

</div>

Of course, you could add also information of the second table and include only countries with a certain value:


```{.sql .klippy}
select * from df
  where country in
  (select country from df2 where valid = 1)
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-27"></span>Table 25: 1 records

|country |  y|   z| id|
|:-------|--:|---:|--:|
|Germany |  2| 3.6|  1|

</div>

Implicit joins implies that we can access multiple tables by specifying them in the `FROM` clause of the query. This leads to a CROSS JOIN (also known as Cartesian Join).


```{.sql .klippy}
select * from df, df2
```


<div class="knitsql-table">


Table: <span id="tab:unnamed-chunk-28"></span>Table 26: 8 records

|country |  y|   z| id|country | valid| id|
|:-------|--:|---:|--:|:-------|-----:|--:|
|Germany |  2| 3.6|  1|Germany |     1|  1|
|Germany |  2| 3.6|  1|Austria |     0|  2|
|Austria |  1| 8.5|  2|Germany |     1|  1|
|Austria |  1| 8.5|  2|Austria |     0|  2|
|Brazil  |  4| 2.5|  3|Germany |     1|  1|
|Brazil  |  4| 2.5|  3|Austria |     0|  2|
|Brazil  |  3| 3.5|  3|Germany |     1|  1|
|Brazil  |  3| 3.5|  3|Austria |     0|  2|

</div>

In DBL2 we can use the where clause to match data (see code); in Oracle there are other matching operaters


```{.sql .klippy}
select * from df, df2 where df.id = df.id;
```

In case of long names, we can use shorter aliases for table names (or use column names with aliases in the SELECT clause):


```{.sql .klippy}
select * from df A, df2 B where A.id = B.id;
```



## Summary

- Most databases come with built-in functions that you can use in SQL statements to perform operations on data within the database itself.

- When you work with large datasets, you may save time by using built-in functions rather than first retrieving the data into your application and then executing functions on the retrieved data.

- Use sub-queries to form more powerful queries.

- A sub-select expression helps to evaluate some built-in aggregate functions like the average function.

- Derived tables or table expressions are sub-queries where the outer query uses the results of the sub-query as a data source.







