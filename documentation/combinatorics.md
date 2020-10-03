---
title: "Combinatorics reference"
author: "Joe DiNoto"
date: "9/27/2020"
output: 
  html_document: 
    toc: yes
    number_sections: yes
    keep_md: yes
---





# Combinatorics Experiments

## Loading libraries 

```r
library(tidyverse)
library(ExPanDaR) # allows for combinations and permutations
```

## using expand() with fruits

https://tidyr.tidyverse.org/reference/expand.html


```r
fruits <- tibble(
  type   = c("apple", "orange", "apple", "orange", "orange", "orange"),
  year   = c(2010, 2010, 2012, 2010, 2010, 2012),
  size  =  factor(
    c("XS", "S",  "M", "S", "S", "M"),
    levels = c("XS", "S", "M", "L")
  ),
  weights = rnorm(6, as.numeric(size) + 2)
)
fruits
```

```
## # A tibble: 6 x 4
##   type    year size  weights
##   <chr>  <dbl> <fct>   <dbl>
## 1 apple   2010 XS       3.88
## 2 orange  2010 S        4.34
## 3 apple   2012 M        5.71
## 4 orange  2010 S        6.18
## 5 orange  2010 S        3.97
## 6 orange  2012 M        6.95
```
### All possible combinations, whether they appear in the data or not.

```r
# All possible combinations ---------------------------------------
# Note that all defined, but not necessarily present, levels of the
# factor variable `size` are retained.
fruits %>% expand(type)
```

```
## # A tibble: 2 x 1
##   type  
##   <chr> 
## 1 apple 
## 2 orange
```


```r
fruits %>% expand(type, size)
```

```
## # A tibble: 8 x 2
##   type   size 
##   <chr>  <fct>
## 1 apple  XS   
## 2 apple  S    
## 3 apple  M    
## 4 apple  L    
## 5 orange XS   
## 6 orange S    
## 7 orange M    
## 8 orange L
```



```r
fruits %>% expand(type, size, year)
```

```
## # A tibble: 16 x 3
##    type   size   year
##    <chr>  <fct> <dbl>
##  1 apple  XS     2010
##  2 apple  XS     2012
##  3 apple  S      2010
##  4 apple  S      2012
##  5 apple  M      2010
##  6 apple  M      2012
##  7 apple  L      2010
##  8 apple  L      2012
##  9 orange XS     2010
## 10 orange XS     2012
## 11 orange S      2010
## 12 orange S      2012
## 13 orange M      2010
## 14 orange M      2012
## 15 orange L      2010
## 16 orange L      2012
```

### Only combinations that appear in the data. 

```r
# Only combinations that already appear in the data ---------------
fruits %>% expand(nesting(type))
```

```
## # A tibble: 2 x 1
##   type  
##   <chr> 
## 1 apple 
## 2 orange
```


```r
fruits %>% expand(nesting(type, size))
```

```
## # A tibble: 4 x 2
##   type   size 
##   <chr>  <fct>
## 1 apple  XS   
## 2 apple  M    
## 3 orange S    
## 4 orange M
```


```r
fruits %>% expand(nesting(type, size, year))
```

```
## # A tibble: 4 x 3
##   type   size   year
##   <chr>  <fct> <dbl>
## 1 apple  XS     2010
## 2 apple  M      2012
## 3 orange S      2010
## 4 orange M      2012
```

### filling in values 


```r
# Other uses -------------------------------------------------------
# Use with `full_seq()` to fill in values of continuous variables
fruits %>% expand(type, size, full_seq(year, 1))
```

```
## # A tibble: 24 x 3
##    type  size  `full_seq(year, 1)`
##    <chr> <fct>               <dbl>
##  1 apple XS                   2010
##  2 apple XS                   2011
##  3 apple XS                   2012
##  4 apple S                    2010
##  5 apple S                    2011
##  6 apple S                    2012
##  7 apple M                    2010
##  8 apple M                    2011
##  9 apple M                    2012
## 10 apple L                    2010
## # ... with 14 more rows
```


```r
fruits %>% expand(type, size, 2010:2012)
```

```
## # A tibble: 24 x 3
##    type  size  `2010:2012`
##    <chr> <fct>       <int>
##  1 apple XS           2010
##  2 apple XS           2011
##  3 apple XS           2012
##  4 apple S            2010
##  5 apple S            2011
##  6 apple S            2012
##  7 apple M            2010
##  8 apple M            2011
##  9 apple M            2012
## 10 apple L            2010
## # ... with 14 more rows
```

### find missing observations


```r
# Use `anti_join()` to determine which observations are missing
all <- fruits %>% expand(type, size, year)
all
```

```
## # A tibble: 16 x 3
##    type   size   year
##    <chr>  <fct> <dbl>
##  1 apple  XS     2010
##  2 apple  XS     2012
##  3 apple  S      2010
##  4 apple  S      2012
##  5 apple  M      2010
##  6 apple  M      2012
##  7 apple  L      2010
##  8 apple  L      2012
##  9 orange XS     2010
## 10 orange XS     2012
## 11 orange S      2010
## 12 orange S      2012
## 13 orange M      2010
## 14 orange M      2012
## 15 orange L      2010
## 16 orange L      2012
```


```r
all %>% dplyr::anti_join(fruits)
```

```
## Joining, by = c("type", "size", "year")
```

```
## # A tibble: 12 x 3
##    type   size   year
##    <chr>  <fct> <dbl>
##  1 apple  XS     2012
##  2 apple  S      2010
##  3 apple  S      2012
##  4 apple  M      2010
##  5 apple  L      2010
##  6 apple  L      2012
##  7 orange XS     2010
##  8 orange XS     2012
##  9 orange S      2012
## 10 orange M      2010
## 11 orange L      2010
## 12 orange L      2012
```


```r
# Use with `right_join()` to fill in missing rows
fruits %>% dplyr::right_join(all)
```

```
## Joining, by = c("type", "year", "size")
```

```
## # A tibble: 18 x 4
##    type    year size  weights
##    <chr>  <dbl> <fct>   <dbl>
##  1 apple   2010 XS       3.88
##  2 orange  2010 S        4.34
##  3 apple   2012 M        5.71
##  4 orange  2010 S        6.18
##  5 orange  2010 S        3.97
##  6 orange  2012 M        6.95
##  7 apple   2012 XS      NA   
##  8 apple   2010 S       NA   
##  9 apple   2012 S       NA   
## 10 apple   2010 M       NA   
## 11 apple   2010 L       NA   
## 12 apple   2012 L       NA   
## 13 orange  2010 XS      NA   
## 14 orange  2012 XS      NA   
## 15 orange  2012 S       NA   
## 16 orange  2010 M       NA   
## 17 orange  2010 L       NA   
## 18 orange  2012 L       NA
```

## using expand() on mtcars

https://www.rdocumentation.org/packages/tidyr/versions/0.8.3/topics/expand


```r
# NOT RUN {
library(dplyr)
# All possible combinations of vs & cyl, even those that aren't
# present in the data
expand(mtcars, vs, cyl)
```

```
## # A tibble: 6 x 2
##      vs   cyl
##   <dbl> <dbl>
## 1     0     4
## 2     0     6
## 3     0     8
## 4     1     4
## 5     1     6
## 6     1     8
```


```r
# Only combinations of vs and cyl that appear in the data
expand(mtcars, nesting(vs, cyl))
```

```
## # A tibble: 5 x 2
##      vs   cyl
##   <dbl> <dbl>
## 1     0     4
## 2     0     6
## 3     0     8
## 4     1     4
## 5     1     6
```



```r
# Implicit missings ---------------------------------------------------------
df <- tibble(
  year   = c(2010, 2010, 2010, 2010, 2012, 2012, 2012),
  qtr    = c(   1,    2,    3,    4,    1,    2,    3),
  return = rnorm(7)
)
df %>% expand(year, qtr)
```

```
## # A tibble: 8 x 2
##    year   qtr
##   <dbl> <dbl>
## 1  2010     1
## 2  2010     2
## 3  2010     3
## 4  2010     4
## 5  2012     1
## 6  2012     2
## 7  2012     3
## 8  2012     4
```

```r
df %>% expand(year = 2010:2012, qtr)
```

```
## # A tibble: 12 x 2
##     year   qtr
##    <int> <dbl>
##  1  2010     1
##  2  2010     2
##  3  2010     3
##  4  2010     4
##  5  2011     1
##  6  2011     2
##  7  2011     3
##  8  2011     4
##  9  2012     1
## 10  2012     2
## 11  2012     3
## 12  2012     4
```

```r
df %>% expand(year = full_seq(year, 1), qtr)
```

```
## # A tibble: 12 x 2
##     year   qtr
##    <dbl> <dbl>
##  1  2010     1
##  2  2010     2
##  3  2010     3
##  4  2010     4
##  5  2011     1
##  6  2011     2
##  7  2011     3
##  8  2011     4
##  9  2012     1
## 10  2012     2
## 11  2012     3
## 12  2012     4
```

```r
df %>% complete(year = full_seq(year, 1), qtr)
```

```
## # A tibble: 12 x 3
##     year   qtr return
##    <dbl> <dbl>  <dbl>
##  1  2010     1 -0.474
##  2  2010     2  1.24 
##  3  2010     3  0.283
##  4  2010     4 -1.94 
##  5  2011     1 NA    
##  6  2011     2 NA    
##  7  2011     3 NA    
##  8  2011     4 NA    
##  9  2012     1 -1.85 
## 10  2012     2 -1.36 
## 11  2012     3  2.36 
## 12  2012     4 NA
```

## combinaions of treatments among patients


```r
# Nesting -------------------------------------------------------------------
# Each person was given one of two treatments, repeated three times
# But some of the replications haven't happened yet, so we have
# incomplete data:
experiment <- tibble(
  name = rep(c("Alex", "Robert", "Sam"), c(3, 2, 1)),
  trt  = rep(c("a", "b", "a"), c(3, 2, 1)),
  rep = c(1, 2, 3, 1, 2, 1),
  measurment_1 = runif(6),
  measurment_2 = runif(6)
)
experiment
```

```
## # A tibble: 6 x 5
##   name   trt     rep measurment_1 measurment_2
##   <chr>  <chr> <dbl>        <dbl>        <dbl>
## 1 Alex   a         1       0.0482        0.987
## 2 Alex   a         2       0.802         0.316
## 3 Alex   a         3       0.612         0.632
## 4 Robert b         1       0.711         0.315
## 5 Robert b         2       0.614         0.559
## 6 Sam    a         1       0.529         0.290
```



```r
# We can figure out the complete set of data with expand()
# Each person only gets one treatment, so we nest name and trt together:
all <- experiment %>% expand(nesting(name, trt), rep)
all
```

```
## # A tibble: 9 x 3
##   name   trt     rep
##   <chr>  <chr> <dbl>
## 1 Alex   a         1
## 2 Alex   a         2
## 3 Alex   a         3
## 4 Robert b         1
## 5 Robert b         2
## 6 Robert b         3
## 7 Sam    a         1
## 8 Sam    a         2
## 9 Sam    a         3
```


```r
# We can use anti_join to figure out which observations are missing
all %>% anti_join(experiment)
```

```
## Joining, by = c("name", "trt", "rep")
```

```
## # A tibble: 3 x 3
##   name   trt     rep
##   <chr>  <chr> <dbl>
## 1 Robert b         3
## 2 Sam    a         2
## 3 Sam    a         3
```



```r
# And use right_join to add in the appropriate missing values to the
# original data
experiment %>% right_join(all)
```

```
## Joining, by = c("name", "trt", "rep")
```

```
## # A tibble: 9 x 5
##   name   trt     rep measurment_1 measurment_2
##   <chr>  <chr> <dbl>        <dbl>        <dbl>
## 1 Alex   a         1       0.0482        0.987
## 2 Alex   a         2       0.802         0.316
## 3 Alex   a         3       0.612         0.632
## 4 Robert b         1       0.711         0.315
## 5 Robert b         2       0.614         0.559
## 6 Sam    a         1       0.529         0.290
## 7 Robert b         3      NA            NA    
## 8 Sam    a         2      NA            NA    
## 9 Sam    a         3      NA            NA
```

```r
# Or use the complete() short-hand
experiment %>% complete(nesting(name, trt), rep)
```

```
## # A tibble: 9 x 5
##   name   trt     rep measurment_1 measurment_2
##   <chr>  <chr> <dbl>        <dbl>        <dbl>
## 1 Alex   a         1       0.0482        0.987
## 2 Alex   a         2       0.802         0.316
## 3 Alex   a         3       0.612         0.632
## 4 Robert b         1       0.711         0.315
## 5 Robert b         2       0.614         0.559
## 6 Robert b         3      NA            NA    
## 7 Sam    a         1       0.529         0.290
## 8 Sam    a         2      NA            NA    
## 9 Sam    a         3      NA            NA
```


```r
# Generate all combinations with expand():
formulas <- list(
  formula1 = Sepal.Length ~ Sepal.Width,
  formula2 = Sepal.Length ~ Sepal.Width + Petal.Width,
  formula3 = Sepal.Length ~ Sepal.Width + Petal.Width + Petal.Length
)
data <- split(iris, iris$Species)
crossing(formula = formulas, data)
```

```
## # A tibble: 9 x 2
##   formula      data             
##   <named list> <named list>     
## 1 <formula>    <df[,5] [50 x 5]>
## 2 <formula>    <df[,5] [50 x 5]>
## 3 <formula>    <df[,5] [50 x 5]>
## 4 <formula>    <df[,5] [50 x 5]>
## 5 <formula>    <df[,5] [50 x 5]>
## 6 <formula>    <df[,5] [50 x 5]>
## 7 <formula>    <df[,5] [50 x 5]>
## 8 <formula>    <df[,5] [50 x 5]>
## 9 <formula>    <df[,5] [50 x 5]>
```

```r
# }
```
