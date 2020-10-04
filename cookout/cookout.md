---
title: "Cookout Combinatorics"
author: "Joe DiNoto"
date: "9/27/2020"
output: 
  html_document: 
    number_sections: yes
    keep_md: yes
    toc: yes
---



# Cookout Data

## Packages 

```r
library(tidyverse)
library(ExPanDaR) # allows for easy permutations
```

## load the data

```r
cookout <- read.csv("cookout.csv")  # read csv file
cookout %>% tibble() # convert to tibble
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

```r
cookout<-cookout %>% mutate_at(vars(Food.Type,Flavor.Strength), factor)
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
##    <chr>  <fct>     <fct>          
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
## hamburgers 2 flavor strength preferences

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

## Ice Cream 2 flavor strength preferences


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
#Filter the list to hamburger names with two flavor strength preferences
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

## Two flavor strengths for Hamburgers


```r
top_vec2<-c() # initializes blank vector

cookout_hamburger <-cookout %>% filter(Name %in% hamburger_names$Name) %>%
  arrange(Toppings) # sorts toppings a to z so we don't have xy yx problems.

for(k in 1:(nrow(cookout)-1)){
  for(i in (k+1):(nrow(cookout))){
  if(
    (cookout$Name[k]==cookout$Name[i]) && 
    (cookout$Food.Type[k] == cookout$Food.Type[i]) && 
    (cookout$Flavor.Strength[k] != cookout$Flavor.Strength[i])
    ){
    z <- data.frame(
      food=c(cookout$Food.Type[k]),
      combo=c(paste(cookout$Toppings[k],cookout$Toppings[i],sep=",")))
    top_vec2 <- rbind(top_vec2,z)
    }
  }
}
```

## let's look at our results!


```r
top_vec2 %>% 
  tibble()%>% 
  group_by(food,combo) %>% 
  summarise(count=n()) %>%
  arrange(desc(count))
```

```
## `summarise()` regrouping output by 'food' (override with `.groups` argument)
```

```
## # A tibble: 42 x 3
## # Groups:   food [2]
##     food combo                   count
##    <int> <chr>                   <int>
##  1     1 katsup,hotsauce             3
##  2     1 katsup,chili flakes         2
##  3     1 katsup,onions               2
##  4     1 lettuce,hotsauce            2
##  5     1 mayo,hotsauce               2
##  6     1 onions,hotsauce             2
##  7     1 onions,lettuce              2
##  8     1 onions,mayo                 2
##  9     2 sprinkles,cherry            2
## 10     2 sprinkles,hot chocolate     2
## # ... with 32 more rows
```

```r
cookout %>% arrange(Toppings)
```

```
##      Name Food.Type Flavor.Strength      Toppings
## 1     Tom ice cream          medium        cherry
## 2   Jerry ice cream          medium        cherry
## 3  George ice cream          medium        cherry
## 4     Tom hamburger          strong  chili flakes
## 5   Jerry hamburger          strong  chili flakes
## 6   Wanda hamburger          strong  chili flakes
## 7   Sofia hamburger          strong  chili flakes
## 8     Sam hamburger          medium   horseradish
## 9     Joe ice cream          medium hot chocolate
## 10    Sam ice cream          medium hot chocolate
## 11    Joe hamburger          strong      hotsauce
## 12    Tom hamburger          strong      hotsauce
## 13    Sam hamburger          strong      hotsauce
## 14  Sofia hamburger          strong      hotsauce
## 15    Joe hamburger           bland        katsup
## 16    Tom hamburger           bland        katsup
## 17    Sam hamburger           bland        katsup
## 18  Wanda hamburger           bland        katsup
## 19    Joe hamburger           bland       lettuce
## 20    Sam hamburger           bland       lettuce
## 21  Wanda hamburger           bland       lettuce
## 22    Joe hamburger           bland          mayo
## 23    Tom hamburger           bland          mayo
## 24  Jerry hamburger           bland          mayo
## 25 George hamburger           bland          mayo
## 26    Joe hamburger          medium        onions
## 27    Sam hamburger          medium        onions
## 28 George hamburger          medium        onions
## 29    Tom ice cream          medium         oreos
## 30  Jerry ice cream          medium         oreos
## 31 George ice cream          medium         oreos
## 32  Sofia ice cream          strong    pineapples
## 33  Wanda ice cream           bland       pretzel
## 34    Joe ice cream          medium recees pieces
## 35    Sam ice cream          medium recees pieces
## 36    Joe ice cream          strong    sour patch
## 37 George ice cream          strong    sour patch
## 38  Wanda ice cream          strong    sour patch
## 39  Sofia ice cream          strong    sour patch
## 40    Joe ice cream           bland     sprinkles
## 41    Tom ice cream           bland     sprinkles
## 42    Sam ice cream           bland     sprinkles
## 43  Jerry ice cream           bland     sprinkles
## 44  Wanda ice cream           bland     sprinkles
## 45  Sofia ice cream           bland     sprinkles
## 46    Sam ice cream          medium  strawberries
## 47  Jerry hamburger           bland        tomato
## 48 George hamburger           bland        tomato
## 49  Wanda hamburger           bland        tomato
## 50 George ice cream          strong      warheads
## 51  Wanda ice cream          strong      warheads
## 52  Sofia ice cream          strong      warheads
## 53    Tom ice cream           bland whipped cream
## 54  Jerry ice cream           bland whipped cream
## 55  Wanda ice cream           bland whipped cream
## 56  Sofia ice cream           bland whipped cream
```
## Misc Notes and such 


```r
# sets up counter for names
# unique_names <- unique(cookout$Name)
# n <- length(unique_names) 
# nrow(cookout) # number of rows in the cookout tibble

    #testing purposes
    #x <- paste(cookout$Name[k],cookout$Toppings[k],k,cookout$Toppings[i],i,sep=",")
    #top_vec <- append(top_vec,x)
    # This will give me a vector of (food type, combos within that food type)
    #y <- paste(cookout$Food.Type[k],cookout$Toppings[k],cookout$Toppings[i],sep=",")
    #top_vec <- append(top_vec,y)
    #let's make this a proper tibble?
```

