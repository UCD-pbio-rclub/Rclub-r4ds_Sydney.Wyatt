# Chapter 11 Data Import
## 11.2 Getting Started  


Most of readr functions turn flat files into data frames:  

* `read_csv()` reads comma delimited files, `read_csv2()` reads semicolon separated files, `read_tsv()` reads tab delimited files, `read_delim()` reads files with any delimiter  
* `read_fwf()` reads fixed width files, `read_table()` reads fixed width files where columns are separated by white space  

All functions have similar syntax. The first argument is the path to the file to read.`read_csv()` prints out a column specification that gives the name and the type of each column. Or you can supply an inline csv file:  

```r
read_csv("a,b,c
1,2,3
4,5,6")
```

```
## # A tibble: 2 × 3
##       a     b     c
##   <int> <int> <int>
## 1     1     2     3
## 2     4     5     6
```

The first line of data is the column names. Two cases where you want to change this:  
1. What if there is metadata at the top of a file? Use `skip=n` to skip first `n` lines or use `comment="#"` to drop all lines that start with, for example, `#`.  

```r
read_csv("The first line of metadata
  The second line of metadata
  x,y,z
  1,2,3", skip = 2)
```

```
## # A tibble: 1 × 3
##       x     y     z
##   <int> <int> <int>
## 1     1     2     3
```

```r
read_csv("# A comment I want to skip
  x,y,z
  1,2,3", comment = "#")
```

```
## # A tibble: 1 × 3
##       x     y     z
##   <int> <int> <int>
## 1     1     2     3
```

2. The data might not have column names. use `col_names = FALSE` to tell `read_csv()` not to treat the first row as headings and instead label them from `X1`to `Xn` or with a designated character vector or tweak `na` to specify things to be used to represent missing values in the file:  

```r
read_csv("1,2,3\n4,5,6", col_names = FALSE)
```

```
## # A tibble: 2 × 3
##      X1    X2    X3
##   <int> <int> <int>
## 1     1     2     3
## 2     4     5     6
```

```r
read_csv("1,2,3\n4,5,6", col_names = c("x", "y", "z"))
```

```
## # A tibble: 2 × 3
##       x     y     z
##   <int> <int> <int>
## 1     1     2     3
## 2     4     5     6
```

```r
read_csv("a,b,c\n1,2,.", na = ".")
```

```
## # A tibble: 1 × 3
##       a     b     c
##   <int> <int> <chr>
## 1     1     2  <NA>
```

Note: `\n` is a shortcut for adding a new line.

### 11.2.2 Exercises  

**1. What function would you use to read a file where fields were separated with
“|”?**  
_I would probably use the_ `read_delim()` _function because it will read files with any delimiter._

**2. Apart from file, skip, and comment, what other arguments do `read_csv()` and `read_tsv()` have in common?**  
_They have_ `col_names`, `col_types`, `locale`, `na`, `quoted_na`, `quote`, `trim_ws`, `n_max`, `guess_max` _and_ `progress`.

**3. What are the most important arguments to `read_fwf()`?**  
_The most important arguments describe the field structure. Such arguments would include_ `file`, `col_positions`, _and_ `col_types`. `col_positions` _can be further described with_ `fwf_empty`, `fwf_widths` _or_ `fwf_positions`.

**4. Sometimes strings in a CSV file contain commas. To prevent them from causing problems they need to be surrounded by a quoting character, like `"` or `'`. By convention, `read_csv()` assumes that the quoting character will be `"`, and if you want to change it you’ll need to use `read_delim()` instead. What arguments do you need to specify to read the following text into a data frame?**

```r
read_csv("x,y
         1,'a,b'", quote = "\'")
```

```
## # A tibble: 1 × 2
##       x     y
##   <int> <chr>
## 1     1   a,b
```

**5. Identify what is wrong with each of the following inline CSV files. What happens when you run the code?**

```r
#read_csv("a,b\n1,2,3\n4,5,6")
#This produces 2 parsing failures. It expected 2 columns (a and b) and then got three (1,2,3 and 4,5,6)
#Solution:
read_csv("a,b\n1,2,3\n4,5,6", skip=1)
```

```
## # A tibble: 1 × 3
##     `1`   `2`   `3`
##   <int> <int> <int>
## 1     4     5     6
```

```r
#read_csv("a,b,c\n1,2\n1,2,3,4")
#This produces 2 parsing failures. It expected 3 columns (a,b and c) and then got two and four (1,2 and 1,2,3,4)

#read_csv("a,b\n\"1")
#Another parsing failure. There was a closing quote where it shouldn't have been.
#Solution:
read_csv("a,b\n.,1", na=".")
```

```
## # A tibble: 1 × 2
##       a     b
##   <chr> <int>
## 1  <NA>     1
```

```r
read_csv("a,b\n1,2\na,b")
```

```
## # A tibble: 2 × 2
##       a     b
##   <chr> <chr>
## 1     1     2
## 2     a     b
```

```r
#So this ran. But this probably not the data frame we wanted when we made this.

read_csv("a;b\n1;3")
```

```
## # A tibble: 1 × 1
##   `a;b`
##   <chr>
## 1   1;3
```

```r
#This also ran. But it made an incorrect data frame because it was expected comma delimited but instead we had semicolon delimited.
#Solution:
read_csv2("a;b\n1;3")
```

```
## Using ',' as decimal and '.' as grouping mark. Use read_delim() for more control.
```

```
## # A tibble: 1 × 2
##       a     b
##   <int> <int>
## 1     1     3
```

```r
read_csv("a,b\n1,3")
```

```
## # A tibble: 1 × 2
##       a     b
##   <int> <int>
## 1     1     3
```

## 11.3 Parsing a vector  

`parse_*()` functions take a character vector and return a specialized vector like a logical, integer or date:  

```r
str(parse_logical(c("TRUE", "FALSE", "NA")))
```

```
##  logi [1:3] TRUE FALSE NA
```

```r
str(parse_integer(c("1", "2", "3")))
```

```
##  int [1:3] 1 2 3
```

```r
str(parse_date(c("2010-01-01", "1979-10-14")))
```

```
##  Date[1:2], format: "2010-01-01" "1979-10-14"
```

The first argument is a character vector to parse and the `na` argument specifies which strings should be treated as missing. Additionally it gives a warning if parsing fails:  

```r
parse_integer(c("1", "231", ".", "456"), na = ".")
```

```
## [1]   1 231  NA 456
```

```r
x <- parse_integer(c("123", "345", "abc", "123.45"))
```

```
## Warning: 2 parsing failures.
## row col               expected actual
##   3  -- an integer                abc
##   4  -- no trailing characters    .45
```

```r
#Failures will be missing in the output:
x
```

```
## [1] 123 345  NA  NA
## attr(,"problems")
## # A tibble: 2 × 4
##     row   col               expected actual
##   <int> <int>                  <chr>  <chr>
## 1     3    NA             an integer    abc
## 2     4    NA no trailing characters    .45
```

```r
#Lots of failures? Use problems()
problems(x)
```

```
## # A tibble: 2 × 4
##     row   col               expected actual
##   <int> <int>                  <chr>  <chr>
## 1     3    NA             an integer    abc
## 2     4    NA no trailing characters    .45
```

Eight important parsers:  
1. `parse_logical()` and `parse_integer()` parse logicals and integers.  
2. `parse_double()` is a strict numeric parser while `parse_number()` is a flexible numeric parser. Complicated because different parts of the world write numbers differently.  
3. `parse_character()` gets more complicated because of character encodings.  
4. `parse_factor()` creates factors which is how R represents categorical variables with fixed/known values.  
5. `parse_datetime()`, `parse_date()`, and `parse_time()` parse different date and time specifications.  

### 11.3.1 Numbers  

Three problems that make parsing numbers tricky: `.` vs `,` as decimals; `$` or `%` that provide context to the numbers; and grouping characters like "1,000,000" that make large numbers easier to read and also varies around the world. Locale is an object that specifies parsing options that differ between places. Default is US. Example of changing the decimal mark:

```r
parse_double("1.23")
```

```
## [1] 1.23
```

```r
parse_double("1,23", locale = locale(decimal_mark = ","))
```

```
## [1] 1.23
```

`parse_number` addresses the second problem. It ignores non-numeric characters before and after the number. It also extracts numbers embedded in text.  

```r
parse_number("$100")
```

```
## [1] 100
```

```r
parse_number("20%")
```

```
## [1] 20
```

```r
parse_number("It cost $123.45")
```

```
## [1] 123.45
```

Finally, `parse_number()` and locale as `parse_number()` will ignore grouping marks.  

```r
parse_number("$123,456,789")
```

```
## [1] 123456789
```

```r
# Used in many parts of Europe
parse_number("123.456.789", locale = locale(grouping_mark = "."))
```

```
## [1] 123456789
```

```r
# Used in Switzerland
parse_number("123'456'789", locale = locale(grouping_mark = "'"))
```

```
## [1] 123456789
```

### 11.3.2 Strings  

Can use `charToRaw()` to get the hexadecimal representation of a string. This encoding is called ASCII for American Standard Code for Information Interchange. To correctly interpret a string, used to need to know values and encoding. Now there is one standard encoding UTF-8 which can encode almost every character. This is the default for readr but this fails for data produced by older systems that don't understand UTF-8. This is clearly apparant when strings are printed as they will have weird things in them. Examples:  

```r
x1 <- "El Ni\xf1o was particularly bad this year"
x2 <- "\x82\xb1\x82\xf1\x82\xc9\x82\xbf\x82\xcd"

x1
```

```
## [1] "El Niño was particularly bad this year"
```

```r
x2
```

```
## [1] "±ñÉ¿Í"
```

Fix by specifying the encoding:  

```r
parse_character(x1, locale = locale(encoding = "Latin1"))
```

```
## [1] "El Niño was particularly bad this year"
```

```r
parse_character(x2, locale = locale(encoding = "Shift-JIS"))
```

```
## [1] "<U+3053><U+3093><U+306B><U+3061><U+306F>"
```

Correct encoding might be included in data documentation. Use `guess_encoding()` to help figure it out.  

```r
guess_encoding(charToRaw(x1))
```

```
## # A tibble: 2 × 2
##     encoding confidence
##        <chr>      <dbl>
## 1 ISO-8859-1       0.46
## 2 ISO-8859-9       0.23
```

```r
guess_encoding(charToRaw(x2))
```

```
## # A tibble: 1 × 2
##   encoding confidence
##      <chr>      <dbl>
## 1   KOI8-R       0.42
```

The first argument to `guess_encoding()` can be a file path or a raw vector if the string is already in R.  

### 11.3.3 Factors  

Warnings are generated whenever unexpected values are present in a vector of known `levels` is given to `parse_factor()`:  

```r
fruit <- c("apple", "banana")
parse_factor(c("apple", "banana", "bananana"), levels = fruit)
```

```
## Warning: 1 parsing failure.
## row col           expected   actual
##   3  -- value in level set bananana
```

```
## [1] apple  banana <NA>  
## attr(,"problems")
## # A tibble: 1 × 4
##     row   col           expected   actual
##   <int> <int>              <chr>    <chr>
## 1     3    NA value in level set bananana
## Levels: apple banana
```

If there are too many problems it might be easier to leave these as charater vectors and use string or factor tools to clean them.

### 11.3.4 Dates, date-times, and times  

Pick based on what you want. `parse_datetime()` expects international standard year-month-day-hour-minute-second format:  

```r
parse_datetime("2010-10-01T2010")
```

```
## [1] "2010-10-01 20:10:00 UTC"
```

```r
# If time is omitted, it will be set to midnight
parse_datetime("20101010")
```

```
## [1] "2010-10-10 UTC"
```

`parse_date()` expects a four digit year, `-` or `/` as separaters, then month and then day:  

```r
parse_date("2010-10-01")
```

```
## [1] "2010-10-01"
```

`parse_time()` expects hour, then `:`, then minutes, and optional seconds and am/pm:  

```r
library(hms)
parse_time("01:10 am")
```

```
## 01:10:00
```

```r
parse_time("20:10:01")
```

```
## 20:10:01
```


Can also supply own date-time format built from year (`%Y` is 4 digits, `%y` is 2 digits), month (`%m` is 2 digits, `%b` is abbreviated name, `%B` is full name), day (`%d` is 2 digits, `%e` is optional leading space), time (`%H` 0-23 hours, `%I` 0-12 hours used with `%p` AM/PM indicator, `%M` minutes, `%s` integer seconds, `%OS` real seconds, `%Z` time zone, `%z` time zone as offset from UTC), and non-digits (`%.` skips one non-digit, `%*` skipps any number of non-digits). Best way to figure out how to represent data is make examples and test:  

```r
parse_date("01/02/15", "%m/%d/%y")
```

```
## [1] "2015-01-02"
```

```r
parse_date("01/02/15", "%d/%m/%y")
```

```
## [1] "2015-02-01"
```

```r
parse_date("01/02/15", "%y/%m/%d")
```

```
## [1] "2001-02-15"
```

If using `%b` or `%B` with non-English month names you need to set the `lang` argument to `locale()`. Built-in languages are in `date_names_lang()` and create your own with `date_names()`.  

```r
parse_date("1 janvier 2015", "%d %B %Y", locale = locale("fr"))
```

```
## [1] "2015-01-01"
```

### 11.3.5 Exercises  

**1. What are the most important arguments to `locale()`?**  
_When parsing numbers, the most important arguments specify the decimal mark and grouping mark. For parsing strings, the encoding argument is most important. Finally for parsing dates and times, the language argument is most important._

**2. What happens if you try and set `decimal_mark` and `grouping_mark` to the same character? What happens to the default value of `grouping_mark` when you set `decimal_mark` to “,”? What happens to the default value of `decimal_mark` when you set the `grouping_mark` to “.”?**  

```r
#parse_number("123,456,789", locale = locale(grouping_mark = ",", decimal_mark = ","))
#This gives an error: decimal_mark and grouping_mark must be different

parse_number("123,456,789", locale = locale(decimal_mark = ","))   #This makes it seem that the decimal_mark is determined before the grouping_mark when parsing.
```

```
## [1] 123.456
```

```r
parse_number("123,456,789", locale = locale(grouping_mark = "."))    #This is also weird as it has the same output as above. However it appears that it changed the default decimal mark to be ",".
```

```
## [1] 123.456
```


**3. I didn’t discuss the `date_format` and `time_format` options to `locale()`. What do they do? Construct an example that shows when they might be useful.**  
_These options are the default date and time formats. Formatting the date can be useful for American dates. Formatting time can be useful for 24hr time._  

```r
#American dates with date_format:
str(parse_guess("01/02/2013", locale = locale(date_format = "%d/%m/%Y")))
```

```
##  Date[1:1], format: "2013-02-01"
```

```r
#24hr time with time_format:
parse_time("1705", locale = locale(time_format = "%H%M"))
```

```
## 17:05:00
```


**4. If you live outside the US, create a new locale object that encapsulates the settings for the types of file you read most commonly.**  
_Well, I live in the US. But I would assign mine new locale format to an object and then when using it to parse data, `locale = "my locale object"`._

**5. What’s the difference between `read_csv()` and `read_csv2()`?**  
`read_csv()` _reads comma delimited files and_ `read_csv2()` _reads semicolon delimited files. These are useful in cases where decimal marks are commas._

**6. What are the most common encodings used in Europe? What are the most common encodings used in Asia? Do some googling to find out.**  
_Common encodings used in Europe are under ISO 8859 (like ISO 8859-1 is used mainly in Western Europe). Asian countries each seem to have their own system. Korea uses KS X 1001, EUC-KR or ISO-2022-KR. Taiwan uses Big5. Hong Kong uses HKSCS. China uses Gubobiao in a few different formats. Japan uses different forms of JIS X 0208. Vietnam uses Windows-1258. Thailand uses ISO 8859-11._

**7. Generate the correct format string to parse each of the following dates and times:**

```r
str(parse_guess("January, 1, 2010", locale = locale(date_format = "%B %d, %Y")))
```

```
##  chr "January, 1, 2010"
```

```r
#%m and %B here both give Mar, but %b gives 03. Weird.
str(parse_guess("2015-Mar-07", locale = locale(date_format = "%Y-%m-%d")))
```

```
##  chr "2015-Mar-07"
```

```r
#Same here
str(parse_guess("06-Jun-2017", locale = locale(date_format = "%d-%B-%Y")))
```

```
##  chr "06-Jun-2017"
```

```r
#Here %m gives the full name as does %b. %B gives default of 2015-08-19.
str(parse_guess(c("August 19 (2015)", "July 1 (2015)"), locale = locale(date_format = "%b %d (%Y)")))
```

```
##  chr [1:2] "August 19 (2015)" "July 1 (2015)"
```

```r
str(parse_guess("12/30/14", locale = locale(date_format = "%B/%d/%y")))
```

```
##  chr "12/30/14"
```

```r
str(parse_guess("1705", locale = locale(time_format = "%H%M")))
```

```
##  int 1705
```

```r
str(parse_guess("11:15:10.12 PM", locale = locale(time_format = "%I:%M:%S.%OS %p")))
```

```
##  chr "11:15:10.12 PM"
```

_Strangely I wasn't able to use the specific date and time parse functions. They would just spit out a default instead of what I wanted._

## 11.4 Parsing a file

readr uses a heuristic approach to figure out the type of each column: it reads the first 1000 rows and uses heuristics to figure out the type of each column. Use `guess_parser()` to emulate this and return readr's best guess and then `parse_guess()` to parse the column based on that guess. Heuristic tries each and stops when it finds a match:  

* logical: contains only F, T, RALSE, or TRUE  
* integer: contains only numeric characters and -  
* double: contains only valid doubles including things like 4.5e-5  
* number: contains valid doubles with the grouping marks inside  
* time: matches the default `time_format`  
* date: matches the default `date_format`  
* date-time: any ISO8601 date  

If none of these match, then the column stays as a vector of strings. The defaults don't always work for larger files which face two basic problems: that the first thousand rows are a special case and the type guessed is not general enough, or that the column might contain a lot of missing values NA so it will be guessed as a character vector. A good strategy to deal with these problems is going column by column until there are no problems remaining. In `read_csv()`, use `col_types` to manually tell readr how to parse the data. If there are major parsing problems, read into a character vector of lines with `read_lines()` or a  character vector of length one with `read_file()`. Then you can do string parsing which is later in the book.  

## 11.5 Writing to a file  

Can use `write_csv()` or `write_tsv()`. Can also write to Excel with `wite_excel_csv()`. There are two arguments: `x` is the data frame to save, and `path` is the location. Can also specify how many missing values there are and if it is to be appended to an existing file.  

## 11.6 Other types of data  

haven reads SPSS, Stata, and SAS files. readxl reads excel files. DBI with a database specific backend (like RMySQL, RSQLite, RPostgreSQL...) runs SQL queries against a database and returns a data frame. Hierarchical data can be read with jsonlite for json files and xml2 for XML files.
