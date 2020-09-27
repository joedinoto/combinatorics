---
title: "Cookout Combinatorics"
author: "Joe DiNoto"
date: "9/27/2020"
output: 
  html_document: 
    number_sections: yes
    keep_md: yes
---



# Cookout Data

## Packagees 

```r
library(tidyverse)
library(dplyr)
library(tibble)
library(ExPanDaR) # allows for easy permutations
```

## load the data

```r
cookout <- read.csv("cookout.csv")  # read csv file
cookout %>% as.tibble() # convert to tibble
```

```
## # A tibble: 56 x 4
##    Name  Food.Type Flavor.Strength Toppings     
##    <chr> <chr>     <chr>           <chr>        
##  1 Joe   hamburger bland           katsup       
##  2 Joe   hamburger medium          onions       
##  3 Joe   hamburger bland           mayo         
##  4 Joe   hamburger bland           lettuce      
##  5 Joe   hamburger strong          hotsauce     
##  6 Joe   ice cream bland           sprinkles    
##  7 Joe   ice cream medium          hot chocolate
##  8 Joe   ice cream strong          sour patch   
##  9 Joe   ice cream medium          recees pieces
## 10 Tom   hamburger bland           katsup       
## # ... with 46 more rows
```

## use expand() to find permutations

```r
# all unique combinations of names, food types and flavor strengths
# nesting() outputs only combinations that already appear in the data
flavor<- cookout %>% expand(nesting(Name, Food.Type, Flavor.Strength))
flavor
```

```
## # A tibble: 30 x 3
##    Name   Food.Type Flavor.Strength
##    <chr>  <chr>     <chr>          
##  1 George hamburger bland          
##  2 George hamburger medium         
##  3 George ice cream medium         
##  4 George ice cream strong         
##  5 Jerry  hamburger bland          
##  6 Jerry  hamburger strong         
##  7 Jerry  ice cream bland          
##  8 Jerry  ice cream medium         
##  9 Joe    hamburger bland          
## 10 Joe    hamburger medium         
## # ... with 20 more rows
```
## hamburgers

```r
# list of names who have 2 flavor strength preferences for hamburgers
hamburger_names <- flavor %>% filter(Food.Type == "hamburger") %>%
  count(Name, sort=TRUE) %>%
  filter(n==2) %>%
  count(Name) %>%
  select(Name)
hamburger_names
```

```
## # A tibble: 4 x 1
##   Name  
##   <chr> 
## 1 George
## 2 Jerry 
## 3 Tom   
## 4 Wanda
```

## Ice Cream


```r
# list of names who have 2 flavor strength preferences for ice cream
ice_cream_names <- flavor %>% filter(Food.Type == "ice cream") %>%
  count(Name, sort=TRUE) %>%
  filter(n==2) %>%
  count(Name) %>%
  select(Name)
ice_cream_names
```

```
## # A tibble: 6 x 1
##   Name  
##   <chr> 
## 1 George
## 2 Jerry 
## 3 Sam   
## 4 Sofia 
## 5 Tom   
## 6 Wanda
```


