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

## Packages 

```r
library(tidyverse)
library(ExPanDaR) # allows for easy permutations
```


```r
foo <- tibble(x = letters[1:3], y = LETTERS[4:6], z=1:3)
foo %>% 
  mutate_at(vars(x, y), factor)
```

```
## # A tibble: 3 x 3
##   x     y         z
##   <fct> <fct> <int>
## 1 a     D         1
## 2 b     E         2
## 3 c     F         3
```


## load the data

```r
cookout <- read.csv("cookout.csv")  # read csv file
cookout %>% mutate_at(vars(Food.Type,Flavor.Strength), factor)
```

```
##      Name Food.Type Flavor.Strength      Toppings
## 1     Joe hamburger           bland        katsup
## 2     Joe hamburger          medium        onions
## 3     Joe hamburger           bland          mayo
## 4     Joe hamburger           bland       lettuce
## 5     Joe hamburger          strong      hotsauce
## 6     Joe ice cream           bland     sprinkles
## 7     Joe ice cream          medium hot chocolate
## 8     Joe ice cream          strong    sour patch
## 9     Joe ice cream          medium recees pieces
## 10    Tom hamburger           bland        katsup
## 11    Tom hamburger           bland          mayo
## 12    Tom hamburger          strong  chili flakes
## 13    Tom hamburger          strong      hotsauce
## 14    Tom ice cream           bland     sprinkles
## 15    Tom ice cream           bland whipped cream
## 16    Tom ice cream          medium        cherry
## 17    Tom ice cream          medium         oreos
## 18    Sam hamburger           bland        katsup
## 19    Sam hamburger          medium        onions
## 20    Sam hamburger          medium   horseradish
## 21    Sam hamburger           bland       lettuce
## 22    Sam hamburger          strong      hotsauce
## 23    Sam ice cream           bland     sprinkles
## 24    Sam ice cream          medium hot chocolate
## 25    Sam ice cream          medium  strawberries
## 26    Sam ice cream          medium recees pieces
## 27  Jerry hamburger          strong  chili flakes
## 28  Jerry hamburger           bland          mayo
## 29  Jerry hamburger           bland        tomato
## 30  Jerry ice cream           bland     sprinkles
## 31  Jerry ice cream           bland whipped cream
## 32  Jerry ice cream          medium        cherry
## 33  Jerry ice cream          medium         oreos
## 34 George hamburger          medium        onions
## 35 George hamburger           bland          mayo
## 36 George hamburger           bland        tomato
## 37 George ice cream          strong      warheads
## 38 George ice cream          strong    sour patch
## 39 George ice cream          medium        cherry
## 40 George ice cream          medium         oreos
## 41  Wanda hamburger           bland        tomato
## 42  Wanda hamburger           bland        katsup
## 43  Wanda hamburger           bland       lettuce
## 44  Wanda hamburger          strong  chili flakes
## 45  Wanda ice cream           bland     sprinkles
## 46  Wanda ice cream           bland whipped cream
## 47  Wanda ice cream          strong    sour patch
## 48  Wanda ice cream           bland       pretzel
## 49  Wanda ice cream          strong      warheads
## 50  Sofia hamburger          strong      hotsauce
## 51  Sofia hamburger          strong  chili flakes
## 52  Sofia ice cream          strong      warheads
## 53  Sofia ice cream          strong    sour patch
## 54  Sofia ice cream          strong    pineapples
## 55  Sofia ice cream           bland     sprinkles
## 56  Sofia ice cream           bland whipped cream
```

```r
# cookout %>% as.tibble() # convert to tibble
cookout
```

```
##      Name Food.Type Flavor.Strength      Toppings
## 1     Joe hamburger           bland        katsup
## 2     Joe hamburger          medium        onions
## 3     Joe hamburger           bland          mayo
## 4     Joe hamburger           bland       lettuce
## 5     Joe hamburger          strong      hotsauce
## 6     Joe ice cream           bland     sprinkles
## 7     Joe ice cream          medium hot chocolate
## 8     Joe ice cream          strong    sour patch
## 9     Joe ice cream          medium recees pieces
## 10    Tom hamburger           bland        katsup
## 11    Tom hamburger           bland          mayo
## 12    Tom hamburger          strong  chili flakes
## 13    Tom hamburger          strong      hotsauce
## 14    Tom ice cream           bland     sprinkles
## 15    Tom ice cream           bland whipped cream
## 16    Tom ice cream          medium        cherry
## 17    Tom ice cream          medium         oreos
## 18    Sam hamburger           bland        katsup
## 19    Sam hamburger          medium        onions
## 20    Sam hamburger          medium   horseradish
## 21    Sam hamburger           bland       lettuce
## 22    Sam hamburger          strong      hotsauce
## 23    Sam ice cream           bland     sprinkles
## 24    Sam ice cream          medium hot chocolate
## 25    Sam ice cream          medium  strawberries
## 26    Sam ice cream          medium recees pieces
## 27  Jerry hamburger          strong  chili flakes
## 28  Jerry hamburger           bland          mayo
## 29  Jerry hamburger           bland        tomato
## 30  Jerry ice cream           bland     sprinkles
## 31  Jerry ice cream           bland whipped cream
## 32  Jerry ice cream          medium        cherry
## 33  Jerry ice cream          medium         oreos
## 34 George hamburger          medium        onions
## 35 George hamburger           bland          mayo
## 36 George hamburger           bland        tomato
## 37 George ice cream          strong      warheads
## 38 George ice cream          strong    sour patch
## 39 George ice cream          medium        cherry
## 40 George ice cream          medium         oreos
## 41  Wanda hamburger           bland        tomato
## 42  Wanda hamburger           bland        katsup
## 43  Wanda hamburger           bland       lettuce
## 44  Wanda hamburger          strong  chili flakes
## 45  Wanda ice cream           bland     sprinkles
## 46  Wanda ice cream           bland whipped cream
## 47  Wanda ice cream          strong    sour patch
## 48  Wanda ice cream           bland       pretzel
## 49  Wanda ice cream          strong      warheads
## 50  Sofia hamburger          strong      hotsauce
## 51  Sofia hamburger          strong  chili flakes
## 52  Sofia ice cream          strong      warheads
## 53  Sofia ice cream          strong    sour patch
## 54  Sofia ice cream          strong    pineapples
## 55  Sofia ice cream           bland     sprinkles
## 56  Sofia ice cream           bland whipped cream
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



```r
#Filter the list to ice cream names with two flavor strength preferences
hamburger_toppings <- cookout %>% filter(Food.Type == "hamburger") %>%
  filter(Name %in% ice_cream_names$Name)
hamburger_toppings
```

```
##      Name Food.Type Flavor.Strength     Toppings
## 1     Tom hamburger           bland       katsup
## 2     Tom hamburger           bland         mayo
## 3     Tom hamburger          strong chili flakes
## 4     Tom hamburger          strong     hotsauce
## 5     Sam hamburger           bland       katsup
## 6     Sam hamburger          medium       onions
## 7     Sam hamburger          medium  horseradish
## 8     Sam hamburger           bland      lettuce
## 9     Sam hamburger          strong     hotsauce
## 10  Jerry hamburger          strong chili flakes
## 11  Jerry hamburger           bland         mayo
## 12  Jerry hamburger           bland       tomato
## 13 George hamburger          medium       onions
## 14 George hamburger           bland         mayo
## 15 George hamburger           bland       tomato
## 16  Wanda hamburger           bland       tomato
## 17  Wanda hamburger           bland       katsup
## 18  Wanda hamburger           bland      lettuce
## 19  Wanda hamburger          strong chili flakes
## 20  Sofia hamburger          strong     hotsauce
## 21  Sofia hamburger          strong chili flakes
```

```r
# Currently working on... how to find permutations of toppings across flavor strengths, by name. 
# Desired output: George: onions, mayo | onions, tomato
# Need to review expand() documentation
# or write a script?
hamburger_toppings %>% 
  filter(Food.Type=="hamburger") %>%
  expand(nesting(Name,Flavor.Strength, Toppings))
```

```
## # A tibble: 21 x 3
##    Name   Flavor.Strength Toppings    
##    <chr>  <chr>           <chr>       
##  1 George bland           mayo        
##  2 George bland           tomato      
##  3 George medium          onions      
##  4 Jerry  bland           mayo        
##  5 Jerry  bland           tomato      
##  6 Jerry  strong          chili flakes
##  7 Sam    bland           katsup      
##  8 Sam    bland           lettuce     
##  9 Sam    medium          horseradish 
## 10 Sam    medium          onions      
## # ... with 11 more rows
```

