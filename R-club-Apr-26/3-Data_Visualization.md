# R for Data Science, Chapter 3

# Data Visualization 


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
_The color option doesn't actually take a color argument. It takes another set of date, like class, and colors the points on the graph according to the different classes the points fall into. Since there are 7 classes, there would be 7 different colors on the graph. To properly get the blue color, one sets color = "blue" outside of aes()._  

**2. Which variables in mpg are categorical? Which variables are continuous? Hint: type ?mpg to read the documentation for the dataset. How can you see this information when you run mpg?**  
_Manufacturer, model, trans, fl, and class are all categorical. When you run mpg, the columns (variables) are classified as <int> or <dbl> for numbers and <chr> for character, or letters/words._  

**3. Map a continous variable to color, size, and shape. How do these aesthetics behave differently for categorical vs continous variables?**  
_First, r throws an error that a continous variable cannot be mapped to shape. So let's try it without giving a shape._  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, color = year, size = cty))
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-7-1.png)<!-- -->
_If a continous variable is given for color, it appears that the color is then based on a gradient instead of each year being it's own distinct color. As for size, a continous variable seems to have the same effect. Luckily, cty had consistent increments so the size distribution looks constant._  

**4. WHat happens if you map the same variable to multiple aesthetics?**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = cty, y = cty, color = cty, size = cty))
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
_As an extreme test, I made each option in aesthetics the same variable cty. The resulting plot is a straight line with a continous range of colors and sizes for the data points._  

**5. What does the stroke aesthetic do? What shapes does it work with? Hint: use ?geom_point.**  
_The stroke aesthetic modifies the width of the border of outlined data points._  

**6. What happens if you map an aesthetic to something other than a variable name, like aes(color = displ < 5)?**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, color = displ < 5))
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
_It appears that this sets a rule for the color. If the displ is less than 5, the data point is colored one way and another way if it is not less than 5. However, this particular example only appears to be binary._  

### 3.5.1 Exercises  

_Note: A facet is part of the main plot. It split up the plot into subplots that display one subset of the data. To facet by 1 variable, use facet_wrap(). To use two variables, use facet_grid(). It is similar to wrap, except the two variable names are separated by ~ instead of that ~ preceeding a single variable name._  

**1. What happens if you facet on a continuous variable?**  
_The text indicates that the variable passed to facet_wrap() should be discrete. So my guess is that giving it a continuous variable will throw an error._  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_wrap(~ cty, nrow = 3)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

_Well, upon running the code, it clearly does not throw an error. Instead we just get many many plots._  

**2. What do the empty cells in plot with facet_grid(drv ~ cyl) mean? How do they relate to this plot?**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = drv, y = cyl)) + 
  facet_grid(drv ~ cyl)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

_The empty cells indicate that there is no data there. For example, there are no cars that have 4 cylinders that are also rear-wheel drive._  

**3. What plots does the following code make? What does . do?**  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(drv ~ .)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

_The texts says to use . instead of a variable name to not facet in the rows or columns dimension. Because the . was the second variable, we faceted into a column instead of a row._  

**4. Take the first faceted plot in this section:**

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_wrap(~ class, nrow = 2)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

**What are the advantages to using faceting instead of the colour aesthetic? What are the disadvantages? How might the balance change if you had a larger dataset?**  
_Faceting clearly arranges the data by multiple variables, allowing for a nice visualization of a lot of data. However, with more data, the color aesthetic is better suited; faceting would just make a mess of it all._  

**5. Read ?facet_wrap. What does nrow do? What does ncol do? WHat other options control the layout of the individual panels? Why doesn't facet_grid() have nrow and ncol variables?**  
_nrow and ncol designate the number of rows and columns. The facet_grip() command doesn't have this because the number of rows and columns depends on the number of categories in each variable given. The other options that control the layout are dir, strip.position, switch, as.table, scales, and shrink._  


**6. When using facet_grid() you should usually put the variable with more unique levels in the columns. Why?**  
_It makes the layout visually more appealing. Here is an example of the convention flipped, where the variable with more unique levels is in the rows._  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(cyl ~ drv)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

_And here is having the more unique variable in the columns._  

```r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(drv ~ cyl)
```

![](3-Data_Visualization_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

