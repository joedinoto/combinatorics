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



# Cookout Combinatorics Problem

## Question 
For a given food type, what is the most common combination of toppings across two flavor strenghts, limited to people who have two (and only two) flavor strength preferences?

Video link explaining the problem:  https://youtu.be/Wu8Gf963jr8

## Packages & loading data

```r
library(tidyverse) 
library(ExPanDaR) # allows for easy permutations
# load the data
cookout <- read.csv("cookout.csv")  # read csv file
cookout %>% tibble() %>%  arrange(Name,Food.Type,Flavor.Strength) # convert to tibble
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

## use expand() to find permutations

```r
# all unique combinations of names, food types and flavor strengths
# nesting() outputs only combinations that already appear in the data
flavor<- cookout %>% expand(nesting(Name, Food.Type, Flavor.Strength))
```


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
hamburger_toppings <- cookout %>% 
  filter(Food.Type == "hamburger") %>%
  filter(Name %in% hamburger_names$Name)
hamburger_toppings
```

```
##      Name Food.Type Flavor.Strength     Toppings
## 1     Tom hamburger           bland       katsup
## 2     Tom hamburger           bland         mayo
## 3     Tom hamburger          strong chili flakes
## 4     Tom hamburger          strong     hotsauce
## 5   Jerry hamburger          strong chili flakes
## 6   Jerry hamburger           bland         mayo
## 7   Jerry hamburger           bland       tomato
## 8  George hamburger          medium       onions
## 9  George hamburger           bland         mayo
## 10 George hamburger           bland       tomato
## 11  Wanda hamburger           bland       tomato
## 12  Wanda hamburger           bland       katsup
## 13  Wanda hamburger           bland      lettuce
## 14  Wanda hamburger          strong chili flakes
```


```r
# Filter the list to ice cream names with two flavor strength preferences
ice_cream_toppings <- cookout %>%
  filter(Food.Type == "ice cream") %>%
  filter(Name %in% ice_cream_names$Name)
ice_cream_toppings
```

```
##      Name Food.Type Flavor.Strength      Toppings
## 1     Tom ice cream           bland     sprinkles
## 2     Tom ice cream           bland whipped cream
## 3     Tom ice cream          medium        cherry
## 4     Tom ice cream          medium         oreos
## 5     Sam ice cream           bland     sprinkles
## 6     Sam ice cream          medium hot chocolate
## 7     Sam ice cream          medium  strawberries
## 8     Sam ice cream          medium recees pieces
## 9   Jerry ice cream           bland     sprinkles
## 10  Jerry ice cream           bland whipped cream
## 11  Jerry ice cream          medium        cherry
## 12  Jerry ice cream          medium         oreos
## 13 George ice cream          strong      warheads
## 14 George ice cream          strong    sour patch
## 15 George ice cream          medium        cherry
## 16 George ice cream          medium         oreos
## 17  Wanda ice cream           bland     sprinkles
## 18  Wanda ice cream           bland whipped cream
## 19  Wanda ice cream          strong    sour patch
## 20  Wanda ice cream           bland       pretzel
## 21  Wanda ice cream          strong      warheads
## 22  Sofia ice cream          strong      warheads
## 23  Sofia ice cream          strong    sour patch
## 24  Sofia ice cream          strong    pineapples
## 25  Sofia ice cream           bland     sprinkles
## 26  Sofia ice cream           bland whipped cream
```


## Flavor strength combinations for Hamburgers


```r
hamburger_toppings %>% 
  arrange(Toppings) # sorts toppings a to z so we don't have xy yx problems when counting combinations.
```

```
##      Name Food.Type Flavor.Strength     Toppings
## 1     Tom hamburger          strong chili flakes
## 2   Jerry hamburger          strong chili flakes
## 3   Wanda hamburger          strong chili flakes
## 4     Tom hamburger          strong     hotsauce
## 5     Tom hamburger           bland       katsup
## 6   Wanda hamburger           bland       katsup
## 7   Wanda hamburger           bland      lettuce
## 8     Tom hamburger           bland         mayo
## 9   Jerry hamburger           bland         mayo
## 10 George hamburger           bland         mayo
## 11 George hamburger          medium       onions
## 12  Jerry hamburger           bland       tomato
## 13 George hamburger           bland       tomato
## 14  Wanda hamburger           bland       tomato
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

## let's look at our results for hamburgers!


```r
top_vec2 %>% 
  tibble()%>% 
  group_by(food,combo) %>% 
  summarise(count=n()) %>%
  arrange(desc(count)) # %>%
```

```
## `summarise()` regrouping output by 'food' (override with `.groups` argument)
```

```
## # A tibble: 10 x 3
## # Groups:   food [1]
##    food      combo                count
##    <chr>     <chr>                <int>
##  1 hamburger katsup,chili flakes      2
##  2 hamburger chili flakes,mayo        1
##  3 hamburger chili flakes,tomato      1
##  4 hamburger katsup,hotsauce          1
##  5 hamburger lettuce,chili flakes     1
##  6 hamburger mayo,chili flakes        1
##  7 hamburger mayo,hotsauce            1
##  8 hamburger onions,mayo              1
##  9 hamburger onions,tomato            1
## 10 hamburger tomato,chili flakes      1
```

```r
  # filter(count>1) # Filter any combination that appears more than once
```

## Flavor strength combinations for Ice Cream


```r
ice_cream_toppings %>% 
  arrange(Toppings) # sorts toppings a to z so we don't have xy yx problems when counting combinations.
```

```
##      Name Food.Type Flavor.Strength      Toppings
## 1     Tom ice cream          medium        cherry
## 2   Jerry ice cream          medium        cherry
## 3  George ice cream          medium        cherry
## 4     Sam ice cream          medium hot chocolate
## 5     Tom ice cream          medium         oreos
## 6   Jerry ice cream          medium         oreos
## 7  George ice cream          medium         oreos
## 8   Sofia ice cream          strong    pineapples
## 9   Wanda ice cream           bland       pretzel
## 10    Sam ice cream          medium recees pieces
## 11 George ice cream          strong    sour patch
## 12  Wanda ice cream          strong    sour patch
## 13  Sofia ice cream          strong    sour patch
## 14    Tom ice cream           bland     sprinkles
## 15    Sam ice cream           bland     sprinkles
## 16  Jerry ice cream           bland     sprinkles
## 17  Wanda ice cream           bland     sprinkles
## 18  Sofia ice cream           bland     sprinkles
## 19    Sam ice cream          medium  strawberries
## 20 George ice cream          strong      warheads
## 21  Wanda ice cream          strong      warheads
## 22  Sofia ice cream          strong      warheads
## 23    Tom ice cream           bland whipped cream
## 24  Jerry ice cream           bland whipped cream
## 25  Wanda ice cream           bland whipped cream
## 26  Sofia ice cream           bland whipped cream
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

## let's look at our results for Ice Cream!


```r
top_vec2 %>% 
  tibble()%>% 
  group_by(food,combo) %>% 
  summarise(count=n()) %>%
  arrange(desc(count)) # %>%
```

```
## `summarise()` regrouping output by 'food' (override with `.groups` argument)
```

```
## # A tibble: 23 x 3
## # Groups:   food [1]
##    food      combo                    count
##    <chr>     <chr>                    <int>
##  1 ice cream sprinkles,cherry             2
##  2 ice cream sprinkles,oreos              2
##  3 ice cream whipped cream,cherry         2
##  4 ice cream whipped cream,oreos          2
##  5 ice cream pineapples,sprinkles         1
##  6 ice cream pineapples,whipped cream     1
##  7 ice cream pretzel,warheads             1
##  8 ice cream sour patch,cherry            1
##  9 ice cream sour patch,oreos             1
## 10 ice cream sour patch,pretzel           1
## # ... with 13 more rows
```

```r
  # filter(count>1) # Filter any combination that appears more than once
```
