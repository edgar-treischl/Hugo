---
title: "Learning SQL the Badass Way (1)"
author: "Edgar Treischl"
date: 2020-03-12
diagram: FALSE
categories: ["SQL"]
---







The blog *learning SQL the badass way* shows you the basic commands to manage tables and data with SQL. I assume that you are familiar with R (or other programming languages) to manipulate data. For this reason, this page only summarizes main idiosyncrasies of SQL, not concepts that you probably know from any other programming experience. I made the experience that learning new things always takes a lot of time and often I have the impression that many courses are designed without taking your personal experience into account. What a surprise, I can read and write some SQL code even I have no idea what SQL does and I guess you can too! This blog is the result of this experience and gives a quick and dirty introduction, assuming that you have some previous coding experience and I do not have to explain why we want to wrangle data in the first place. If you do not have this impression or if you want to learn SQL in a proper way, close this blog. Go and find your way to a real SQL course or some other resources. However, if you are bored from long introduction what data is, why we need to learn how to wrangle data or other common aspects that come along the data science journey, feel free to join my SQL journey.

The biggest mistake trying to learn SQL first. If you ever opened a book that introduces SQL (or any other programming language) to will find several chapters that outline you what SQL (structured query language) is. An explanation from the Wikipedia page should be sufficient: "SQL a domain-specific language used in programming and designed for managing data held in a relational database management system (RDBMS), or for stream processing in a relational data stream management system (RDSMS)". Thus, in a nutshell we can remember that SQL is a language to manage data (tables) in databases. The next chapters shows you how to setup the corresponding software and the database. I'll not do that in this blog because setting up a database on your own computer does not make much sense because there are easier ways for the first steps in SQL especially if you are a R user.

In case you do not have access to a SQL database: This page was built with `RMarkdown` and includes some SQL snippets with the `DBI` package. All you have to do is install the package, setup the connection and include your SQL code directly in your RMarkdown document or your R script. Thus, in our case there is no database installed, we can make use of the the local memory to simulate a database, save a table (data frame) and run SQL commands directly as a code chunk via our R script. Checkout the databases page from [RStudio](https://db.rstudio.com/dbi/) for more information. Furthermore, I hope you are familiar with the `mtcars` and the `iris` data if you want to reproduce these first SQL steps. Both are implemented in R, but it does not matter if you not familiar with these data sets. I picked them because a lot of people know them and this blog shows you the first basic SQL commands how retrieve and work with data.

## Select

-   Use select to retrieve a table or a column from a table
-   You can select a single column from a table
-   Or select the entire table (data frame) with the wildcard `*`


```{.sql .klippy}
SELECT * FROM mtcars;
```


<div class="knitsql-table">


Table: Table 1: Displaying records 1 - 10

|  mpg| cyl|  disp|  hp| drat|    wt|  qsec| vs| am| gear| carb|
|----:|---:|-----:|---:|----:|-----:|-----:|--:|--:|----:|----:|
| 21.0|   6| 160.0| 110| 3.90| 2.620| 16.46|  0|  1|    4|    4|
| 21.0|   6| 160.0| 110| 3.90| 2.875| 17.02|  0|  1|    4|    4|
| 22.8|   4| 108.0|  93| 3.85| 2.320| 18.61|  1|  1|    4|    1|
| 21.4|   6| 258.0| 110| 3.08| 3.215| 19.44|  1|  0|    3|    1|
| 18.7|   8| 360.0| 175| 3.15| 3.440| 17.02|  0|  0|    3|    2|
| 18.1|   6| 225.0| 105| 2.76| 3.460| 20.22|  1|  0|    3|    1|
| 14.3|   8| 360.0| 245| 3.21| 3.570| 15.84|  0|  0|    3|    4|
| 24.4|   4| 146.7|  62| 3.69| 3.190| 20.00|  1|  0|    4|    2|
| 22.8|   4| 140.8|  95| 3.92| 3.150| 22.90|  1|  0|    4|    2|
| 19.2|   6| 167.6| 123| 3.92| 3.440| 18.30|  1|  0|    4|    4|

</div>

-   Limit the output by providing the number of lines


```{.sql .klippy}
SELECT mpg, disp FROM mtcars LIMIT 5;
```


<div class="knitsql-table">


Table: Table 2: 5 records

|  mpg| disp|
|----:|----:|
| 21.0|  160|
| 21.0|  160|
| 22.8|  108|
| 21.4|  258|
| 18.7|  360|

</div>

-   You can also insert a starting point that skips some observations. For instance, `OFFSET 10` will skip the first ten table entries
-   You must use quotations marks if the column contains special characters (like `'Petal.Width'` from iris data)

## Where

-   Define what you want to select with the `Where` option (SQL folks say clause)
-   For instance, the variable `am` is a binary indicator (0/1) and you can use *where* to select data only if `am = 0`


```{.sql .klippy}
SELECT * FROM mtcars WHERE am = 0 LIMIT 5;
```


<div class="knitsql-table">


Table: Table 3: 5 records

|  mpg| cyl|  disp|  hp| drat|    wt|  qsec| vs| am| gear| carb|
|----:|---:|-----:|---:|----:|-----:|-----:|--:|--:|----:|----:|
| 21.4|   6| 258.0| 110| 3.08| 3.215| 19.44|  1|  0|    3|    1|
| 18.7|   8| 360.0| 175| 3.15| 3.440| 17.02|  0|  0|    3|    2|
| 18.1|   6| 225.0| 105| 2.76| 3.460| 20.22|  1|  0|    3|    1|
| 14.3|   8| 360.0| 245| 3.21| 3.570| 15.84|  0|  0|    3|    4|
| 24.4|   4| 146.7|  62| 3.69| 3.190| 20.00|  1|  0|    4|    2|

</div>

-   Remember to use quotation marks if you try to use *where* with non-numerical values from: e.g. `!= 'label'`


```{.sql .klippy}
SELECT * FROM iris WHERE Species = "virginica"  LIMIT 5;
```


<div class="knitsql-table">


Table: Table 4: 5 records

| Sepal.Length| Sepal.Width| Petal.Length| Petal.Width|Species   |
|------------:|-----------:|------------:|-----------:|:---------|
|          6.3|         3.3|          6.0|         2.5|virginica |
|          5.8|         2.7|          5.1|         1.9|virginica |
|          7.1|         3.0|          5.9|         2.1|virginica |
|          6.3|         2.9|          5.6|         1.8|virginica |
|          6.5|         3.0|          5.8|         2.2|virginica |

</div>

## Count

-   Count counts cases!


```{.sql .klippy}
SELECT COUNT(*) FROM mtcars;
```


<div class="knitsql-table">


Table: Table 5: 1 records

| COUNT(*)|
|--------:|
|       32|

</div>

-   We can count subgroups with the `WHERE` clause:


```{.sql .klippy}
SELECT COUNT(am) FROM mtcars WHERE am != 0;
```


<div class="knitsql-table">


Table: Table 6: 1 records

| COUNT(am)|
|---------:|
|        13|

</div>

## DISTINCT

-   `Distinct` can be used to find distinct values. For instance, there are three different Species in the iris data, and distinct helps you to distract them:


```{.sql .klippy}
SELECT DISTINCT Species FROM iris
```


<div class="knitsql-table">


Table: Table 7: 3 records

|Species    |
|:----------|
|setosa     |
|versicolor |
|virginica  |

</div>

-   As in other programming languages, we can combine several commands. For instance, we can `COUNT` how many `Distinct` species the iris data has:


```{.sql .klippy}
SELECT COUNT (DISTINCT Species) FROM iris
```


<div class="knitsql-table">


Table: Table 8: 1 records

| COUNT (DISTINCT Species)|
|------------------------:|
|                        3|

</div>

## Insert Values

-   Next, I use a small data set (df) to illustrate how to *insert values*, *make updates*, and *delete cases*

-   My toy data set `df` has two observations with three variables: `x,y,z`

-   Never mind if you do not know what a `tribble` is, it is just a command to create data


```{.r .klippy}
library(tidyverse)

df <- tribble(
  ~x, ~y,  ~z,
  "a", 2,  3.6,
  "b", 1,  8.5
)
df
```

```
## # A tibble: 2 × 3
##   x         y     z
##   <chr> <dbl> <dbl>
## 1 a         2   3.6
## 2 b         1   8.5
```

-   Now, we can insert new values into `df` by providing a list of the columns you want to fill in with values for each column:


```{.sql .klippy}
INSERT INTO df (x, y, z) VALUES('c', 3, 1);
```

Let's see whether it worked:


```{.sql .klippy}
SELECT * FROM df;
```


<div class="knitsql-table">


Table: Table 9: 3 records

|x  |  y|   z|
|:--|--:|---:|
|a  |  2| 3.6|
|b  |  1| 8.5|
|c  |  3| 1.0|

</div>

## Updates

-   Make updates for single (or multiple) values
-   For instance, we can update the variable z and set `z = 77` for a certain level of another variable:


```{.sql .klippy}
UPDATE df SET z = 77 WHERE x ='b';
```

-   Take care, without the `WHERE` clause all observation would get the new value!


```{.sql .klippy}
SELECT * FROM df;
```


<div class="knitsql-table">


Table: Table 10: 3 records

|x  |  y|    z|
|:--|--:|----:|
|a  |  2|  3.6|
|b  |  1| 77.0|
|c  |  3|  1.0|

</div>

## Delete

-   We can drop or delete observations, but of course we should take care since we probably do not want to delete the entire table, just for some implausible values

-   For this reason we use the `WHERE` clause again, for instance, to get rid of second row of the toy data set:


```{.sql .klippy}
DELETE from df WHERE x = 'b';
```


```{.sql .klippy}
SELECT * FROM df;
```


<div class="knitsql-table">


Table: Table 11: 2 records

|x  |  y|   z|
|:--|--:|---:|
|a  |  2| 3.6|
|c  |  3| 1.0|

</div>

In summary, in SQL we have to select a table from the database, specify conditions with the where clause. We can use count and distinct to get a first impression of the data. Furthermore, it is all about table. Sometimes we have to insert values, a really vague concept if you are use to work with data, but from a SQL you give your database a update, you are really right in front of the data or imagine that stream of new data needs an update. If that something you have to get used to it, don't be afraid, me too. 






