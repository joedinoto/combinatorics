---
title: "Cookout Combinatorics"
author: "Joe DiNoto"
date: "4 Oct 2020"
output: 
  html_document: 
    number_sections: yes
    keep_md: yes
    toc: yes
---



# Question 

 * **For a given food type, what is the most common combination of toppings across two flavor strenghts, limited to people who have two (and only two) flavor strength preferences?**

 * Video link explaining the problem:  https://youtu.be/Wu8Gf963jr8

Scroll down for a peek at the data and an interpretation of the question.


```r
# packages & loading data
library(tidyverse) 
library(ExPanDaR) # allows for easy permutations
cookout <- read.csv("cookout.csv")  # read csv file
cookout <- cookout %>% tibble() %>%  arrange(Name,Food.Type,Flavor.Strength) 
cookout
```

```
## # A tibble: 56 x 4
##    Name   Food.Type Flavor.Strength Toppings    
##    <chr>  <chr>     <chr>           <chr>       
##  1 George hamburger bland           mayo        
##  2 George hamburger bland           tomato      
##  3 George hamburger medium          onions      
##  4 George ice cream medium          cherry      
##  5 George ice cream medium          oreos       
##  6 George ice cream strong          warheads    
##  7 George ice cream strong          sour patch  
##  8 Jerry  hamburger bland           mayo        
##  9 Jerry  hamburger bland           tomato      
## 10 Jerry  hamburger strong          chili flakes
## # ... with 46 more rows
```

 
For instance, George prefers bland and medium hamburger toppings. His hamburger flavor combinations acros those two flavor strenghts would be:

 * mayo & onions
 * tomato & onions

Note that mayo & tomato are not a valid combinations because they are both bland flavors. George likes medium and strong ice cream toppings so his ice cream flavor combinations would be

 * cherry & warheads
 * cherry & sour patch
 * oreos & warheads
 * oreos & sour patch

Similarly, cherry & oreos and warheads & sour patch are invalid combinations because they are within the same flavor strength.

# Solution 

## use expand() to find permutations

```r
# all unique combinations of names, food types and flavor strengths
# nesting() outputs only combinations that already appear in the data
flavor<- cookout %>% expand(nesting(Name, Food.Type, Flavor.Strength))

# list of names who have 2 flavor strength preferences for hamburgers
hamburger_names <- flavor %>% filter(Food.Type == "hamburger") %>%
  count(Name, sort=TRUE) %>%
  filter(n==2) %>%
  count(Name) %>%
  select(Name)

# list of names who have 2 flavor strength preferences for ice cream
ice_cream_names <- flavor %>% filter(Food.Type == "ice cream") %>%
  count(Name, sort=TRUE) %>%
  filter(n==2) %>%
  count(Name) %>%
  select(Name)

#Filter the list to hamburger names with two flavor strength preferences
hamburger_toppings <- cookout %>% 
  filter(Food.Type == "hamburger") %>%
  filter(Name %in% hamburger_names$Name)

# Filter the list to ice cream names with two flavor strength preferences
ice_cream_toppings <- cookout %>%
  filter(Food.Type == "ice cream") %>%
  filter(Name %in% ice_cream_names$Name)
```

## Flavor strength combinations for Hamburgers


```r
hamburger_toppings %>% 
  arrange(Toppings) # sorts toppings a to z so we don't have xy yx problems when counting combinations.
```

```
## # A tibble: 14 x 4
##    Name   Food.Type Flavor.Strength Toppings    
##    <chr>  <chr>     <chr>           <chr>       
##  1 Jerry  hamburger strong          chili flakes
##  2 Tom    hamburger strong          chili flakes
##  3 Wanda  hamburger strong          chili flakes
##  4 Tom    hamburger strong          hotsauce    
##  5 Tom    hamburger bland           katsup      
##  6 Wanda  hamburger bland           katsup      
##  7 Wanda  hamburger bland           lettuce     
##  8 George hamburger bland           mayo        
##  9 Jerry  hamburger bland           mayo        
## 10 Tom    hamburger bland           mayo        
## 11 George hamburger medium          onions      
## 12 George hamburger bland           tomato      
## 13 Jerry  hamburger bland           tomato      
## 14 Wanda  hamburger bland           tomato
```

```r
top_vec2<-c() # initializes blank vector

for(k in 1:(nrow(hamburger_toppings)-1)){
  for(i in (k+1):(nrow(hamburger_toppings))){
  if(
    (hamburger_toppings$Name[k]==hamburger_toppings$Name[i]) && 
    # (hamburger_toppings$Food.Type[k] == hamburger_toppings$Food.Type[i]) && 
    (hamburger_toppings$Flavor.Strength[k] != hamburger_toppings$Flavor.Strength[i])
    ){
    z <- data.frame(
      name = c(hamburger_toppings$Name[k]),
      food = c(hamburger_toppings$Food.Type[k]),
      combo = c(paste(hamburger_toppings$Toppings[k],hamburger_toppings$Toppings[i],sep=",")))
    top_vec2 <- rbind(top_vec2,z)
    }
  }
}
```

## Results for hamburgers


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
## # A tibble: 8 x 3
## # Groups:   food [1]
##   food      combo                count
##   <chr>     <chr>                <int>
## 1 hamburger katsup,chili flakes      2
## 2 hamburger mayo,chili flakes        2
## 3 hamburger tomato,chili flakes      2
## 4 hamburger katsup,hotsauce          1
## 5 hamburger lettuce,chili flakes     1
## 6 hamburger mayo,hotsauce            1
## 7 hamburger mayo,onions              1
## 8 hamburger tomato,onions            1
```

## Flavor strength combinations for Ice Cream


```r
ice_cream_toppings %>% 
  arrange(Toppings) # sorts toppings a to z so we don't have xy yx problems when counting combinations.
```

```
## # A tibble: 26 x 4
##    Name   Food.Type Flavor.Strength Toppings     
##    <chr>  <chr>     <chr>           <chr>        
##  1 George ice cream medium          cherry       
##  2 Jerry  ice cream medium          cherry       
##  3 Tom    ice cream medium          cherry       
##  4 Sam    ice cream medium          hot chocolate
##  5 George ice cream medium          oreos        
##  6 Jerry  ice cream medium          oreos        
##  7 Tom    ice cream medium          oreos        
##  8 Sofia  ice cream strong          pineapples   
##  9 Wanda  ice cream bland           pretzel      
## 10 Sam    ice cream medium          recees pieces
## # ... with 16 more rows
```

```r
top_vec2<-c() # initializes blank vector

for(k in 1:(nrow(ice_cream_toppings)-1)){
  for(i in (k+1):(nrow(ice_cream_toppings))){
  if(
    (ice_cream_toppings$Name[k]==ice_cream_toppings$Name[i]) && 
    # (ice_cream_toppings$Food.Type[k] == ice_cream_toppings$Food.Type[i]) && 
    (ice_cream_toppings$Flavor.Strength[k] != ice_cream_toppings$Flavor.Strength[i])
    ){
    z <- data.frame(
      name = c(ice_cream_toppings$Name[k]),
      food = c(ice_cream_toppings$Food.Type[k]),
      combo = c(paste(ice_cream_toppings$Toppings[k],ice_cream_toppings$Toppings[i],sep=",")))
    top_vec2 <- rbind(top_vec2,z)
    }
  }
}
```

## Results for Ice Cream


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
## # A tibble: 19 x 3
## # Groups:   food [1]
##    food      combo                    count
##    <chr>     <chr>                    <int>
##  1 ice cream sprinkles,cherry             2
##  2 ice cream sprinkles,oreos              2
##  3 ice cream sprinkles,sour patch         2
##  4 ice cream sprinkles,warheads           2
##  5 ice cream whipped cream,cherry         2
##  6 ice cream whipped cream,oreos          2
##  7 ice cream whipped cream,sour patch     2
##  8 ice cream whipped cream,warheads       2
##  9 ice cream cherry,sour patch            1
## 10 ice cream cherry,warheads              1
## 11 ice cream oreos,sour patch             1
## 12 ice cream oreos,warheads               1
## 13 ice cream pretzel,sour patch           1
## 14 ice cream pretzel,warheads             1
## 15 ice cream sprinkles,hot chocolate      1
## 16 ice cream sprinkles,pineapples         1
## 17 ice cream sprinkles,recees pieces      1
## 18 ice cream sprinkles,strawberries       1
## 19 ice cream whipped cream,pineapples     1
```
