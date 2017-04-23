# R for Data Science, Chapter 3

# Data Visualization 

We will be using the _tidyverse_ dataset:


### 3.2.4 Exercises 
**1. Run ggplot(data = mpg) what do you see?**  

```r
ggplot(data = mpg)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-2-1.png)<!-- -->
_Well, nothing happens. I don't get a plot. But according to the text under 3.2.2, that's what's supposed to happen._  

**2. How many rows are in mtcars? How many columns?**   
_To look at the number of rows and columns in mtcars, I can run the code below._  

```r
nrow(mtcars)
```

```
## [1] 32
```

```r
ncol(mtcars)
```

```
## [1] 11
```
_There are 32 rows and 11 columns._  

**3. What does the drv variable describe? Read the help for ?mpg to find out.**  
_The drv variable describes the kind of drive the car has, as in front-wheel drive (f), rear wheel drive (r), or 4 wheel drive (4wd)._  

**4. Make a scatterplot of hwy vs cyl.**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = hwy, y = cyl))
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

**5. What happens if you make a scatterplot of class vs drv. Why is the plot not useful?**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = class, y = drv))
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

_This plot isn't useful because these are qualitative variables. It doesn't provide a count of how many compacts there are with front wheel drive versus 4 wheel drive; it just provides the information that compacts have fall into both of those categories._  

### 3.3.1 Exercises 
**1. What's wrong with this code? Why are the points not blue?**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, color = "blue"))
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

**2. Which variables in mpg are categorical? Which variables are continuous? Hint: type ?mpg to read the documentation for the dataset. How can you see this information when you run mpg?**  
**3. Map a continous variable to color, size, and shape. How do these aesthetics behave differently for categorical vs continous variables?**  
**4. WHat happens if you map the same variable to multiple aesthetics?**  
**5. What does the stroke aesthetic do? What shapes does it work with? Hint: use ?geom_point.**  
**6. What happens if you map an aesthetic to something other than a variable name, like aes(color = displ < 5)?**  

### 3.5.1 Exercises  
**1. What happens if you facet on a continuous variable?**  
**2. What do the empty cells in plot with facet_grid(drv ~ cyl) mean? How do they relate to this plot?**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = drv, y = cyl))
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

**3. What plots does the following code make? What does . do?**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(drv ~ .)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

**4. Take the first faceted plot in this section:**

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_wrap(~ class, nrow = 2)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
**What are the advantages to using faceting instead of the colour aesthetic? What are the disadvantages? How might the balance change if you had a larger dataset?**  
**5. Read ?facet_wrap. What does nrow do? What does ncol do? WHat other options control the layout of the individual panels? Why doesn't facet_grid() have nrow and ncol variables?**  
**6. When using facet_grid() you should usually put the variable with more unique levels in the columns. Why?**  
