---
title: "The PythoneeR: Intro"
author: "Edgar Treischl"
date: 2021-03-21
diagram: FALSE
categories: ["Python"]
outout:
  html_document:
    highlight: kate
---






I tried to learn Python several times. Once I had a very peculiar class in which we should learn Python for processing natural language. Well, it was more about text mining than applying Python. I tried to learn Python with the help of several books which introduced and ins and out of Python. Before I started to learn Python, I fluently spoke several other statistics software like R, Stata, and please forgive me, my personal journey started with SPSS. To reestablish my reputation, I did not used SPSS for a hundered years and most of time I work with R. 

Anyway, what I am trying to say is that I was very familiar with statistical software programing. I had basic skills in other languages like HTML, CSS, JavaScript. I wrote my PhD with LaTeX. With all of this background, I still failed to learn Python. It is a shame, don't you think?

It is really a shame, since I consider myself a tech-savvy without having the heavy programing background, but with a lot of fun learning new things and enjoy coding. Well, at least, if we get some results. For this reason I started this block. I was sick of reading books about Python without having the impression that I learn what I should learn to improve my skills. This impression could also be due to my educational background. I know what a function is, what a loop does, and have some background in statistics, even though the latter is limited since I am home in the social sciences.


Anyway, this blog is the results of many disappointing books with many pages that could not explain what I was seeking. If you want to learn Python to automate processes, learn some statistical procedures from the scratch, or use Python to program some games, this block will not help you in any way. However, you do wanna transfer your knowledge from other languages like R, if you are familiar with multivarite (non-) linear regression techniques or do you have background in empirical social science, than this blog might help you transfer your knowledge to Python. Let be give you an example why I started this blog. Hopefully, this illustrates my point and gives you some guidance whether its worth following this blog or if I am just another weirdo. 

## How (not) to calculate some basic statistics in Python

The last Python book I read, introduced probably over a hunderd pages about the different data structure, and of course, we need to know that to handle Python. I certainly don't argue about this point, but instead of providing me meaningful examples to illstrate this point, I am reading about about articifical examples that not even work when I copied the code and run it in my own console. Well, shit happens, don't you think? I really started to get the creep as I reached a statistics section. Let's see what I learned from this chapter.

In Python, you have to import the according libraries and install first, in case you work with a library the very first time. We will cover these points later, for the moment it is enough to look at the Python code and output. So, I import the seaborn package in order to have some data at hand. We can save the mpg data under the name cars and use the print function to see a bit of the data.


```python
#import seaborn to get some data

import seaborn as sns
cars = sns.load_dataset("mpg") 
print(cars)
```

```
##       mpg  cylinders  ...  origin                       name
## 0    18.0          8  ...     usa  chevrolet chevelle malibu
## 1    15.0          8  ...     usa          buick skylark 320
## 2    18.0          8  ...     usa         plymouth satellite
## 3    16.0          8  ...     usa              amc rebel sst
## 4    17.0          8  ...     usa                ford torino
## ..    ...        ...  ...     ...                        ...
## 393  27.0          4  ...     usa            ford mustang gl
## 394  44.0          4  ...  europe                  vw pickup
## 395  32.0          4  ...     usa              dodge rampage
## 396  28.0          4  ...     usa                ford ranger
## 397  31.0          4  ...     usa                 chevy s-10
## 
## [398 rows x 9 columns]
```

So, what did I learn in the chapter? Well, how can we calculate an mean with Python? Shouldn't be that though. I know how to calculate a mean by hand since my early days of school and how it is done in a statistic software. The authors offered me the following solution:


```python
def mean (array): 
    n = len(array)
    return sum(array) / n

mean(cars.mpg)
```

```
## 23.514572864321615
```

In the code snippet we define the function mean to create a mean, within this function we define n as the length(len) of the array and the functions returns a value, which divides the sum of the array by the length n. Fine. That's exactly a cool way to show us how a functions works. We have to provide a name of a function, tell Python what the function should do, in this case with an array of numbers.

In my experience, I always learned how functions and other concepts work in much more artifical way. Look how I learned what a function in Python does:


```python
#Create your now function
def hello(name):
    return (f"Hallo, {name}! How are you?")

# Call the function and give value for the input name
hello("Edgar")
```

```
## 'Hallo, Edgar! How are you?'
```

Thus, we create the hello function that returns a sentence and inserts the name of the input value. Nothing worng about that, even though calculating a mean seems a little bit more realistic to illustrate why one needs such a function. 

Anyway, in the next steps the author gives us a function for the mean, a function for the median, a function for the modus, a function to calculate the variance and, I am dead serious, other functions for others measure of central tendency. Come on, this guy can't be serious, we should learn how this works in practice. 

Use NumPy to get the scientific toolkit and pandas for tabular processing and the presentation of data. The latter comes with a lot of functions we can use them in order to calcuate basic statistics and more advanced stuff. Use the describe function to get an overview about the data. Let's see how it works. 


```python
import numpy as np
import pandas as pd

cars.describe()
```

```
##               mpg   cylinders  ...  acceleration  model_year
## count  398.000000  398.000000  ...    398.000000  398.000000
## mean    23.514573    5.454774  ...     15.568090   76.010050
## std      7.815984    1.701004  ...      2.757689    3.697627
## min      9.000000    3.000000  ...      8.000000   70.000000
## 25%     17.500000    4.000000  ...     13.825000   73.000000
## 50%     23.000000    4.000000  ...     15.500000   76.000000
## 75%     29.000000    8.000000  ...     17.175000   79.000000
## max     46.600000    8.000000  ...     24.800000   82.000000
## 
## [8 rows x 7 columns]
```
So, we have to append the describe() function to the saved cars data in order to get an overview of the central tendency measures. You can do the same with all other functions that calculate other measures, such as the mean or standard deviance: 


```python
cars.mean()
```

```
## mpg               23.514573
## cylinders          5.454774
## displacement     193.425879
## horsepower       104.469388
## weight          2970.424623
## acceleration      15.568090
## model_year        76.010050
## dtype: float64
## 
## <string>:1: FutureWarning: The default value of numeric_only in DataFrame.mean is deprecated. In a future version, it will default to False. In addition, specifying 'numeric_only=None' is deprecated. Select only valid columns or specify the value of numeric_only to silence this warning.
```

```python
cars.std()
```

```
## mpg               7.815984
## cylinders         1.701004
## displacement    104.269838
## horsepower       38.491160
## weight          846.841774
## acceleration      2.757689
## model_year        3.697627
## dtype: float64
## 
## <string>:1: FutureWarning: The default value of numeric_only in DataFrame.std is deprecated. In a future version, it will default to False. In addition, specifying 'numeric_only=None' is deprecated. Select only valid columns or specify the value of numeric_only to silence this warning.
```
So, what you do think? Shall we learn how to define our own function to calculate the mean or choose the easy way and let a package calcuate the "hard" stuff for us. I would say both. We learned on this entry how to write an own function by solving a real-world problem. In case we didn't know how to calculate a mean in Python we could use a function and I provided you on purpose with a second example to show you how many others explain a function. However, after learning what a function is, we need to know how we could work and spent our time more efficiently. 

This point becomes bothersome if we spent too much of our time with things that have a simple solution. For instance, guess what the next code does. And I guess you will skip the code after line 2, well that's at least what recommend if you know what a correlation is. 


```python
import math
def correlation(x, y):
    n = len(x) 
    
    # Means
    x_mn = sum(x) / n 
    y_mn = sum(y) / n
    
    # Variance
    var_x = (1 / (n-1)) * sum(map(lambda xi: (xi - x_mn) ** 2 , x)) 
    var_y = (1 / (n-1)) * sum(map(lambda yi: (yi - y_mn) ** 2 , y))
    
    # Std
    std_x, std_y = math.sqrt(var_x), math.sqrt(var_y)
    
    # Covariance
    xy_var = map(lambda xi, yi: (xi - x_mn) * (yi - y_mn), x, y) 
    cov = (1 / (n-1)) * sum(xy_var)
    
    # Pearson's R
    r = cov / (std_x * std_y) 
    return float(f"{r:.3f}")

# Some data
size = [20, 15, 40, 25, 35]
cost = [300, 400, 600, 700, 666]

print(correlation(size, cost))
```

```
## 0.666
```

It's the "devil's code"! Of course, I am just kidding, but please don't get me wrong. I think it is very useful to see how a correlation coefficients is calculated. You should do it at least once per hand if you learn the concept for the first time. But in my case I don't have to learn what a correlation is. Let's learn how Python works, but in the "edgar-bad-ass" way:


```python
np.corrcoef(size, cost)
```

```
## array([[1.        , 0.66645893],
##        [0.66645893, 1.        ]])
```

And we should at least look at the scatter plot in case we talk about correlations. The matplotlib provides a lot of different graphs for us.


```python
import matplotlib
import matplotlib.pyplot as plt
matplotlib.style.use('ggplot')

plt.scatter(cars.mpg, cars.horsepower)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="672" />

And in case you are an R user, you can even use the ggplot2 style, just to let you show some possibilities. But of course, we have to think more systematically how we can reach our goal the next time.
