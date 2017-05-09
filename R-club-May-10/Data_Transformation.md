# Chapter 5: Data Transformation


Note: Variable Types  
`int` = integers  
`dbl` = real numbers  
`chr` = strings  
`dttm` = date-time  
`lgl` = logical
`fctr` = categorical variables with fixed possible values  
`date` = dates  

Note: dplyr functions  
*Pick observations by value with `filter()`.
*Reorder rows with `select()`.
*Create new variables with functions of existing variables with `mutate()`.
*Collapse many values into a single summary with `summarise()`.
*Change the scope of the above functions from operating on entire dataset to group-by-group with `group()`.

These funtions all work similarly.  
1. first argument is a data frame  
2. subsequent arguments describe what to do with the data frame using variable names without quotes  
3. result is a new data frame  

### 5.2.4 Exercises  
**1. Find all the flights that  
> 1. Had an arrival delay of two or more hours  
> 2. Flew to Houston (`IAH` or `HOU`)  
> 3. Were operated by United, American, or Delta  
> 4. Departed in summer (July, August, and September)  
> 5. Arrived more than two hours late, but didn't leave late  
> 6. Were delayed by at least an hour, but made up over 30 minutes in flight  
> 7. Departed between midnight and 6am (inclusive)**

**2. Another useful dplyr filtering helper is `between()`. What does it do? Can you use it to simplify the code needed to answer the previous challenges?**

**3. How many flights have a missing `dep_time`? What other variables are missing? What might these rows represent?**

**4. Why is `NA^0` not missing? Why is `NA | TRUE` not missing? Why is `FALSE & NA` not missing? Can you figure out the general rule?**

### 5.3.1 Exercises
**1. How could use `arrange()` to sort all missing values to the start?**

**2. Sort `flights` to find the most delayed flights. Find the flights that left the earliest.**

**3. Sort `flights` to find the fastest flights.**

**4. Which flights travelled the longest? Which travelled the shortest?**

### 5.4.1 Exercises
**1. Brainstorm as many ways as possible to select `dep_time`, `dep_delay`, `arr_time`, and `arr_delay` from `flights`.**

**2. What happens if you include the name of a variable multiple times in a `select()` call?**

**3. What does `one_of()` do? Why might it be helpful in conjunction with this vector?**

```r
vars <- c("year", "month", "day", "dep_delay", "arr_delay")
```

**4. Does the result of running the following code surprise you? How do the select helpers deal with case by default? How can you change that default?**

```r
select(flights, contains("TIME"))
```

```
## # A tibble: 336,776 × 6
##    dep_time sched_dep_time arr_time sched_arr_time air_time
##       <int>          <int>    <int>          <int>    <dbl>
## 1       517            515      830            819      227
## 2       533            529      850            830      227
## 3       542            540      923            850      160
## 4       544            545     1004           1022      183
## 5       554            600      812            837      116
## 6       554            558      740            728      150
## 7       555            600      913            854      158
## 8       557            600      709            723       53
## 9       557            600      838            846      140
## 10      558            600      753            745      138
## # ... with 336,766 more rows, and 1 more variables: time_hour <dttm>
```

### 5.5.2 Exercises
**1. Currently `dep_time` and `sched_dep_time` are convenient to look at, but hard to compute because they're not really continuous numbers. Convert them to a more convenient representation of number of minutes since midnight.**

**2. Compare `air_time` with `arr_time - dep_time`. What do you expect to see? What do you see? What do you need to do to fix it?**

**3. Compare `dep_time`, `sched_dep_time`, and `dep_delay`. How would you expect those three numbers to be related?**

**4. Find the 10 most delayed flights using a ranking function. How do you want to handle ties? Carefully read the documentation for `min_rank()`.**

**5. What does `1:3 + 1:10` return? Why?**

**6. What trigonometric functions does R provide?**
