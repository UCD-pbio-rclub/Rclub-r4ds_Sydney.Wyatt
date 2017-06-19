# More Tidy Data



## 12.5 Missing values

A value can be missing explicitly (marked with `NA`) or implicitly (just not there). Example:  

```r
stocks <- tibble(
  year   = c(2015, 2015, 2015, 2015, 2016, 2016, 2016),
  qtr    = c(   1,    2,    3,    4,    2,    3,    4),
  return = c(1.88, 0.59, 0.35,   NA, 0.92, 0.17, 2.66)
)
```

There are two missing values in the above dataset:  
* The return for the 4th quarter of 2015 contains `NA`.  
* The return for the 1st quarter of 2016 just doesn't contain anything.  

Can make implicit values explicit:  

```r
stocks %>% 
  spread(year, return)
```

```
## # A tibble: 4 × 3
##     qtr `2015` `2016`
## * <dbl>  <dbl>  <dbl>
## 1     1   1.88     NA
## 2     2   0.59   0.92
## 3     3   0.35   0.17
## 4     4     NA   2.66
```

And vice versa:  

```r
stocks %>% 
  spread(year, return) %>% 
  gather(year, return, `2015`:`2016`, na.rm = TRUE)
```

```
## # A tibble: 6 × 3
##     qtr  year return
## * <dbl> <chr>  <dbl>
## 1     1  2015   1.88
## 2     2  2015   0.59
## 3     3  2015   0.35
## 4     2  2016   0.92
## 5     3  2016   0.17
## 6     4  2016   2.66
```

Also to make missing values explicit, can use `complete()`:  

```r
stocks %>% 
  complete(year, qtr)
```

```
## # A tibble: 8 × 3
##    year   qtr return
##   <dbl> <dbl>  <dbl>
## 1  2015     1   1.88
## 2  2015     2   0.59
## 3  2015     3   0.35
## 4  2015     4     NA
## 5  2016     1     NA
## 6  2016     2   0.92
## 7  2016     3   0.17
## 8  2016     4   2.66
```

This function finds all the unique combinations of a set of columns. It ensures the original dataset contains all those values and fills in `NA` as needed. Can fill in missing values with `fill()`:  


```r
treatment <- tribble(
  ~ person,           ~ treatment, ~response,
  "Derrick Whitmore", 1,           7,
  NA,                 2,           10,
  NA,                 3,           9,
  "Katherine Burke",  1,           4
)

treatment %>% 
  fill(person)
```

```
## # A tibble: 4 × 3
##             person treatment response
##              <chr>     <dbl>    <dbl>
## 1 Derrick Whitmore         1        7
## 2 Derrick Whitmore         2       10
## 3 Derrick Whitmore         3        9
## 4  Katherine Burke         1        4
```

### 12.5.1 Exercises

**1. Compare and contrast the `fill` arguments to `spread()` and `complete()`.**  

```r
stocks %>% 
  complete(year, qtr, fill = list(qtr = 3))
```

```
## # A tibble: 8 × 3
##    year   qtr return
##   <dbl> <dbl>  <dbl>
## 1  2015     1   1.88
## 2  2015     2   0.59
## 3  2015     3   0.35
## 4  2015     4     NA
## 5  2016     1     NA
## 6  2016     2   0.92
## 7  2016     3   0.17
## 8  2016     4   2.66
```

```r
stocks %>% 
  spread(year, return, fill = NA)
```

```
## # A tibble: 4 × 3
##     qtr `2015` `2016`
## * <dbl>  <dbl>  <dbl>
## 1     1   1.88     NA
## 2     2   0.59   0.92
## 3     3   0.35   0.17
## 4     4     NA   2.66
```

_They seem to do similar things although for `complete()` the documentation implied you could set your own value or maybe designate values you wanted to become `NA`._

**2. What does the direction argument to `fill()` do?**  

```r
#treatment %>% 
#  fill(person, direction = c("up"))
```

_Direction is the direction in which to fill missing values. I could not get this to work at all. The documentation says that it takes either down or up. The error: "All select() inputs must resolve to integer column positions. The following do not: * c("up")."_

## 12.6 Case Study

Using the `tidyr::who` dataset, tackle a data tidying problems! This dataset has redundant columns, odd variable codes, and lost of missing values. What columns are not variables?  
* `country`, `iso2`, and `iso3` redundantly specify country.  
* `year` is clearly a variable.  
* Given the structure of variable names like `new_sp_m014`, these are porbably values not variables.  

First gather all the columns from `new_spm014` to `newrel_f65`. Not sure what they represent, so `key = "key"` and that the cells will represent a count of cases. Let's also ignore missing values.  

```r
who1 <- who %>% 
  gather(new_sp_m014:newrel_f65, key = "key", value = "cases", na.rm = TRUE)
```

Get some structure help about the values in the `key` column.  

```r
who1 %>% 
  count(key)
```

```
## # A tibble: 56 × 2
##             key     n
##           <chr> <int>
## 1   new_ep_f014  1032
## 2  new_ep_f1524  1021
## 3  new_ep_f2534  1021
## 4  new_ep_f3544  1021
## 5  new_ep_f4554  1017
## 6  new_ep_f5564  1017
## 7    new_ep_f65  1014
## 8   new_ep_m014  1038
## 9  new_ep_m1524  1026
## 10 new_ep_m2534  1020
## # ... with 46 more rows
```

Use the data dictionary to get more info! Need to fix some of the column names to make it easier to work with.  

```r
who2 <- who1 %>% 
  mutate(key = stringr::str_replace(key, "newrel", "new_rel"))
```

Now we can separate in two stages: first at each underscore and then into sex and age by splitting after the first character.  

```r
who3 <- who2 %>% 
  separate(key, c("new", "type", "sexage"), sep = "_")
who3 %>% 
  count(new)
```

```
## # A tibble: 1 × 2
##     new     n
##   <chr> <int>
## 1   new 76046
```

```r
who4 <- who3 %>% 
  select(-new, -iso2, -iso3)
who5 <- who4 %>% 
  separate(sexage, c("sex", "age"), sep = 1)
who5
```

```
## # A tibble: 76,046 × 6
##        country  year  type   sex   age cases
## *        <chr> <int> <chr> <chr> <chr> <int>
## 1  Afghanistan  1997    sp     m   014     0
## 2  Afghanistan  1998    sp     m   014    30
## 3  Afghanistan  1999    sp     m   014     8
## 4  Afghanistan  2000    sp     m   014    52
## 5  Afghanistan  2001    sp     m   014   129
## 6  Afghanistan  2002    sp     m   014    90
## 7  Afghanistan  2003    sp     m   014   127
## 8  Afghanistan  2004    sp     m   014   139
## 9  Afghanistan  2005    sp     m   014   151
## 10 Afghanistan  2006    sp     m   014   193
## # ... with 76,036 more rows
```

It is now tidy! Here is the pipe version:  

```r
who %>%
  gather(code, value, new_sp_m014:newrel_f65, na.rm = TRUE) %>% 
  mutate(code = stringr::str_replace(code, "newrel", "new_rel")) %>%
  separate(code, c("new", "var", "sexage")) %>% 
  select(-new, -iso2, -iso3) %>% 
  separate(sexage, c("sex", "age"), sep = 1)
```

```
## # A tibble: 76,046 × 6
##        country  year   var   sex   age value
## *        <chr> <int> <chr> <chr> <chr> <int>
## 1  Afghanistan  1997    sp     m   014     0
## 2  Afghanistan  1998    sp     m   014    30
## 3  Afghanistan  1999    sp     m   014     8
## 4  Afghanistan  2000    sp     m   014    52
## 5  Afghanistan  2001    sp     m   014   129
## 6  Afghanistan  2002    sp     m   014    90
## 7  Afghanistan  2003    sp     m   014   127
## 8  Afghanistan  2004    sp     m   014   139
## 9  Afghanistan  2005    sp     m   014   151
## 10 Afghanistan  2006    sp     m   014   193
## # ... with 76,036 more rows
```

### 12.6.1 Exercises

**1. In this case study I set `na.rm = TRUE` just to make it easier to check that we had the correct values. Is this reasonable? Think about how missing values are represented in this dataset. Are there implicit missing values? What’s the difference between an `NA` and zero?**  
_I think this is reasonable enough for this exercise but it would miss the implicit missing values which could possibly still screw up checking your work. A zero is a value whereas `NA` is a marker._  

**2. What happens if you neglect the `mutate()` step? (`mutate(key = stringr::str_replace(key, "newrel", "new_rel"))`)**  

```r
who %>%
  gather(code, value, new_sp_m014:newrel_f65, na.rm = TRUE) %>% 
  separate(code, c("new", "var", "sexage")) %>% 
  select(-new, -iso2, -iso3) %>% 
  separate(sexage, c("sex", "age"), sep = 1)
```

```
## Warning: Too few values at 2580 locations: 73467, 73468, 73469, 73470,
## 73471, 73472, 73473, 73474, 73475, 73476, 73477, 73478, 73479, 73480,
## 73481, 73482, 73483, 73484, 73485, 73486, ...
```

```
## # A tibble: 76,046 × 6
##        country  year   var   sex   age value
## *        <chr> <int> <chr> <chr> <chr> <int>
## 1  Afghanistan  1997    sp     m   014     0
## 2  Afghanistan  1998    sp     m   014    30
## 3  Afghanistan  1999    sp     m   014     8
## 4  Afghanistan  2000    sp     m   014    52
## 5  Afghanistan  2001    sp     m   014   129
## 6  Afghanistan  2002    sp     m   014    90
## 7  Afghanistan  2003    sp     m   014   127
## 8  Afghanistan  2004    sp     m   014   139
## 9  Afghanistan  2005    sp     m   014   151
## 10 Afghanistan  2006    sp     m   014   193
## # ... with 76,036 more rows
```

**3. I claimed that `iso2` and `iso3` were redundant with country. Confirm this claim.**

```r
who %>% 
  select(country, iso2, iso3)
```

```
## # A tibble: 7,240 × 3
##        country  iso2  iso3
##          <chr> <chr> <chr>
## 1  Afghanistan    AF   AFG
## 2  Afghanistan    AF   AFG
## 3  Afghanistan    AF   AFG
## 4  Afghanistan    AF   AFG
## 5  Afghanistan    AF   AFG
## 6  Afghanistan    AF   AFG
## 7  Afghanistan    AF   AFG
## 8  Afghanistan    AF   AFG
## 9  Afghanistan    AF   AFG
## 10 Afghanistan    AF   AFG
## # ... with 7,230 more rows
```

_`iso2` is a two letter code for country and `iso3` is a three letter code for country. This makes these columns redundant with `country`._

**4. For each country, year, and sex compute the total number of cases of TB. Make an informative visualization of the data.**  

```r
q4data <- who5 %>% select(country, year, sex, cases)
```

## 12.7 Non-tidy data

# 13 Relational data  

## 13.1 Introduction  

Relational data = multiple tables of data; relations in the data are important not just the individual datasets. Relations are defined between a pair of tables. Three verb families that are going to be used:  
* Mutating joins: add new variables to one data frame from matching observations in another.  
* Filtering joins: filter observations from one data frame based on whether or not they match an observation in the other table.  
* Set operations: which treat observations as if they were set elements.  



## 13.2 nycflights13  

nycflights13 contains four tibbles that are related to the `flights` table.  
* `airlines` looks up full carrier name from abbreviated code.  
* `airports` gives info about each airport identified by the `faa` airport code.  
* planes gives info about each plane identified by its `tailnum`.  
* `weather` gives the weather at each NYC airport for each hour.  

Chain of relations:  
* `flights` connects to `planes` via `tailnum`.  
* `flights` connects to `airlines` through `carrier`.  
* `flights` connects to `airports` through `origin` and `dest`.  
* `flights` connects to `weather` via `origin` and `year`, `month`, `day`, and `hour`.  

### 13.2.1 Exercises  

 **1. Imagine you wanted to draw the route each plane flies from its origin to its destination. What variables would you need? What tables would you need to combine?**  
 _TO draw the route, I would need several variables from different tables. From `flights` I would need `origin`, `dest`, `year`, `month`, `day`, and `hour`. From `airports` I would need to match the `origin` and `dest` variables to their respective airports and get `lat` and `lon` in order to map a route. Finally from `weather` I would need to match the `year:hour` information to relevant weather variables because different weather patterns can result in route changes to a flight. _

**2. I forgot to draw the relationship between `weather` and `airports`. What is the relationship and how should it appear in the diagram?**  
_`weather` and `airports` relate at the `faa` code in `airports` and `origin` in `weather`._  

**3. `weather` only contains information for the origin (NYC) airports. If it contained weather records for all airports in the USA, what additional relation would it define with `flights`?**  
_I believe it would also relate at the `dest` variable in `flights` as well._  

**4. We know that some days of the year are “special”, and fewer people than usual fly on them. How might you represent that data as a data frame? What would be the primary keys of that table? How would it connect to the existing tables?**  
_I would represent it in a special dates data frame. It would be almost like `flights` but instead include just the extracted special dates (like holidays). It would connect to existing tables in the same way as `flights` does._
