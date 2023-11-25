---
title: "Master SQL with R"
author: "Edgar Treischl"
date: 2023-11-11
diagram: FALSE
output:
  html_document:
    highlight: kate
bibliography: references.bib
---

This article briefly introduces R packages to master SQL. I will not introduce SQL nor all kind of R packages that are helpful to work with SQL; instead I will focus on packages that support us to learn SQL. First, I show that there is no need to install software, create databases, or get familiar with cloud applications in the beginning. You can focus on and run SQL queries from R with no further ado. Moreover, we will connect to a database and see why especially the `RSQLite` package helps us in the first steps. Second, I introduce the `dbplyr` package which let us combine our R and SQL skills. Finally, I highlight how a document with SQL code and output can be generated from R to summarize our (SQL) knowledge and progress.

You can download this article and the source code from my [GitHub](https://github.com/edgar-treischl/medium) repository.

<figure>
<img src="featured.png" alt="Artwork by @allison_horst" />
<figcaption aria-hidden="true">Artwork by @allison_horst</figcaption>
</figure>

## Run SQL queries from R

Many books and courses introduces SQL and they outline in depth what SQL (structured query language) is. A lot of them go beyond this step, they start to introduce different databases backends (e.g., MySQL, MariaDB), CLI commands to interact with databases, and they show how to setup software or at least introduce a web interface to interact with a database.

Certainly, these topics increase our understanding, but this seems a lot of effort considering your first goal is to familiarize yourself with SQL. We need to practice SQL and the described steps make the learning curve pretty steep. Fortunately, for R users there is no need to install software or create a database to run your first queries. Consider the next console with a simple SQL code snippet. It illustrates that we often start with a select step since we need to retrieve data from the database. We select one, several, or all columns (`*`) from the table. Later I’ll show you that we can use R and translate R code into SQL, so keep this simple SQL snippet in mind:

``` sql
--Example SQL Code
SELECT var FROM data;
```

In further steps we may learn how to filter the data and set a condition (in SQL we apply a `WHERE` clause); we learn how to count (`COUNT`) cases, or do some basic calculations such as calculating the mean (`AVG`). You can write and run all of these queries without leaving R. Let’s keep it simple. Suppose we want to learn how to select a variable and calculate the mean:

``` sql
SELECT AVG(var) FROM data;
```

The `sqldf` package let us apply SQL code snippets to a data frame (Grothendieck 2017). You can build your SQL skills and follow a course without a large setup, without our own database, or any cloud solution in the beginning. Install the package (via `install.packages`) and write your first queries from R. How about the mean consumption of cars with the implemented `mtcars` data:

``` r
#Run SQL code from R
library(sqldf)
sqldf('SELECT AVG(mpg) FROM mtcars;')
```

    #>   AVG(mpg)
    #> 1 20.09062

The `sqldf` package makes it very convenient to run a few SQL snippets and the package outlines how we interact with different SQL databases in its documentation: ” `sqldf()` transparently sets up a database, imports the data frames into that database, performs the SQL select or other statement and returns the result using a heuristic to determine which class to assign to each column of the returned data frame” (Grothendieck 2017).

Again, there is no need to establish a real database in the beginning, we can make use of the *local memory* to simulate a database, save a table (data frame) and run SQL commands directly as a code chunk from the R script exactly as the `sqldf` package does. The `RSQLite` package helps us with this task, it creates the SQLite database on your local machine (Müller et al. 2023). With this approach, it is almost like if we work with a real database, but it is still very convenient for beginners. To do so, we need to establish a connection to the database and this code chunk will also work as a template in the near future when you switch from the local to the real database.

How do we connect R with a database? Use the `DBI` package to connect to many different databases (Wickham and Müller 2022). In a nutshell, we need to establish a connection (`con`) with the `dbConnect()` function. This implies that we need to pick a driver (`drv`) to connect to a specific database (e.g., `RMariaDB::MariaDB()` for MariaDB; or `RSQLServer::SQLServer` for Postgres, etc.); furthermore, we need to provide the properties needed to get access to the database (e.g., host, username, etc.).

The `DBI` package gives us the code to illustrate this step with a `guest` account for a `MariaDB` database.

``` r
#Establish a connection
library(DBI)

con <- dbConnect(
  drv = RMariaDB::MariaDB(),
  dbname = "sakila",
  host = "relational.fit.cvut.cz",
  port = 3306,
  username = "guest",
  password = "relational"
)
```

After we made the connection, check if the approach worked. The `dbListTables()` functions lists all available tables of the connected database.

``` r
#List all available tables/data
dbListTables(con)
#  [1] "actor"         "address"       "category"     
#  [4] "city"          "country"       "customer"     
#  [7] "film"          "film_actor"    "film_category"
# [10] "film_text"     "inventory"     "language"     
# [13] "payment"       "rental"        "staff"        
# [16] "store"     
```

Keep in mind that we do not want to share sensitive information such as the user name or password in the code. Use the `askForPassword()` function from the `rstudioapi` package (Ushey et al. 2023). It forces us to insert the password each time we connect. Or use the `keyring` package to save the key savely in your environment and not in the code (Csárdi 2022). Irrespective of the used approach, we connect to the database, retrieve and wrangle data, and finally disconnect before we can go on.

``` r
#Disconnect after have finished the job
dbDisconnect(con)
```

I claimed that we can run this step without having access to a database. That’s where the `RSQLite` package comes into play. The `SQLite()` function creates the SQLite database on your local machine if you use `:memory:` as database name (`dbname`).

``` r
# Create in-memory RSQLite database
con <- dbConnect(drv = RSQLite::SQLite(), 
                 dbname = ":memory:")
```

This creates an empty database which is why we need to insert a table into the local database first.

``` r
#Write a table into the data base
dbWriteTable(conn = con, 
             name = "mtcars", 
             value = mtcars)
```

The `dbSendQuery()` function let you sent SQL queries to the connected database, `dbFetch` shows the result, and we can clear the result after we have finished.

``` r
#Send queries to the local database
result_DB <- dbSendQuery(con, "SELECT AVG(mpg) FROM mtcars;")
dbFetch(result_DB)
```

    #>   AVG(mpg)
    #> 1 20.09062

``` r
dbClearResult(result_DB)
```

Such R packages increases our learning curve substantially, since we can create a database and write SQL code without any other equipment than our local machine. In addition, the `dbplyr` package will help us to combine our R skills with SQL, it even translate R into SQL.

## The dbplyr package

As outlined on the package website: “`dbplyr` is the database backend for dplyr (Wickham, Girlich, and Ruiz 2023). It allows you to use remote database tables as if they are in-memory data frames by automatically converting dplyr code into SQL.” Thus, we can apply common dplyr verbs to SQL databases. First, we need to create a table from a database with the `tbl()` function.

``` r
#Create a table from the database
library(dplyr)
library(dbplyr)

mtcars_db <- tbl(con, "mtcars")
```

Let us go back to example SQL code snippet. Suppose we didn’t know how to select and calculate the mean in SQL. There are several ways to calculate it with R, but the `tidyverse` approach is not difficult (Wickham et al. 2019); and it let us apply our `dplyr` knowledge thanks to the `dbplyr` package. The `summarise()` function collapses the data and we get the `mean()` by including it in the latter step, however, only if we run the code locally.

``` r
#Get mean with dplyr (locally)
mtcars |> 
  summarise(mean_mpg = mean(mpg))
```

    #>   mean_mpg
    #> 1 20.09062

In order to apply this step in our database, we must assign the data manipulation steps (here as `summary`) and we must execute the query to retrieve results via the `collect()` function.

``` r
#Get mean with dbplyr
summary <- mtcars_db |> 
  summarise(mean_mpg = mean(mpg))

#Collect (execute and retrieve) the result from the db
summary |>  collect()
```

    #> # A tibble: 1 × 1
    #>   mean_mpg
    #>      <dbl>
    #> 1     20.1

What is happening under the hood? How can we apply R code to SQL? The package has a `show_query()` function which shows the underlying SQL code that we sent to the database.

``` r
#Inspect SQL query
summary |>  show_query()
```

    #> <SQL>
    #> SELECT AVG(`mpg`) AS `mean_mpg`
    #> FROM `mtcars`

The packages has more to offer than I possibly can outline. For example, `translate_sql()` let you translate SQL code. This may help with your learning curve and it also highlights that there are different SQL dialects depending on the database you work with. Suppose to need to clean data. How can we manipulate strings to lower case in SQL? In R we may use the `tolower()` function:

``` r
#tolower returns strings in lower case
tolower("HeLLo WoRld")
```

    #> [1] "hello world"

The `translate_sql()` function translate it to SQL and we can see differences between SQL dialects such as SQLite and Access (`LOWER` and `LCASE`) if we simulate different engines (e.g., `simulate_sqlite`).

``` r
#Translate with different SQL engines
translate_sql(tolower("HeLLo WoRld"), con = simulate_sqlite())
```

    #> <SQL> LOWER('HeLLo WoRld')

``` r
translate_sql(tolower("HeLLo WoRld"), con = simulate_access())
```

    #> <SQL> LCASE('HeLLo WoRld')

Go the [website](https://dplyr.tidyverse.org/index.html) to learn more about `dbplyr`; however, I have one last one last advice for the road.

## Document SQL with Rmarkdown and Quarto

If I do not work with a language on a regular base, I forget how it works instantly. Thus, code documentation is an important issue. In R we create documents with `rmarkdown`, but we can also include different languages - including SQL - in an `rmarkdown` or a Quarto document. Go the rmarkdown [website](https://rmarkdown.rstudio.com) if have never created documents with R, because we can create a document with text, (SQL) code, and the output to summarize what you have achieved so far. Moreover, you can be sure that the code contains no mistake since the code runs when the document is created.

Say we want to document the SQL code from the beginning:

``` sql
--Example SQL Code
SELECT AVG(mpg) FROM mtcars;
```

<div class="knitsql-table">

| AVG(mpg) |
|---------:|
| 20.09062 |

<span id="tab:unnamed-chunk-16"></span>Table 1: 1 records

</div>

First, you need to create the connection to the database. Create a new `rmarkdown` document and insert the code to create a connection inside the setup-chunk. Second, insert a code chunk into the document, but this time a SQL code chunk and point to the database via the `connection` chunk-option. This makes it possible to run the SQL code and it returns the output as result. The next code shows how such code chunk looks like in an R Markdown document:

    ```{sql connection=con}
    --Example SQL Code
    SELECT AVG(mpg) FROM mtcars;
    ```

I hope you find the discussed tips helpful, at least, I missed them when I started to learn SQL.

## References

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-keyring" class="csl-entry">

Csárdi, Gábor. 2022. “Keyring: Access the System Credential Store from r.” <https://CRAN.R-project.org/package=keyring>.

</div>

<div id="ref-sqldf" class="csl-entry">

Grothendieck, G. 2017. “Sqldf: Manipulate r Data Frames Using SQL.” <https://CRAN.R-project.org/package=sqldf>.

</div>

<div id="ref-RSQLite" class="csl-entry">

Müller, Kirill, Hadley Wickham, David A. James, and Seth Falcon. 2023. “RSQLite: SQLite Interface for r.” <https://CRAN.R-project.org/package=RSQLite>.

</div>

<div id="ref-rstudioapi" class="csl-entry">

Ushey, Kevin, JJ Allaire, Hadley Wickham, and Gary Ritchie. 2023. “Rstudioapi: Safely Access the RStudio API.” <https://CRAN.R-project.org/package=rstudioapi>.

</div>

<div id="ref-tidyverse" class="csl-entry">

Wickham, Hadley, Mara Averick, Jennifer Bryan, Winston Chang, Lucy D’Agostino McGowan, Romain François, Garrett Grolemund, et al. 2019. “Welcome to the Tidyverse” 4: 1686. <https://doi.org/10.21105/joss.01686>.

</div>

<div id="ref-dbplyr" class="csl-entry">

Wickham, Hadley, Maximilian Girlich, and Edgar Ruiz. 2023. “Dbplyr: A ’Dplyr’ Back End for Databases.” <https://CRAN.R-project.org/package=dbplyr>.

</div>

<div id="ref-DBI" class="csl-entry">

Wickham, Hadley, and Kirill Müller. 2022. “DBI: R Database Interface.” <https://CRAN.R-project.org/package=DBI>.

</div>

</div>
