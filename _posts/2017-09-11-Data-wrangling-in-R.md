---
layout: post
title: "Data Wrangling in R"
categories: ['Data Wrangling']
tags: [data wrangling]
comments: true
---


{% highlight r %}
library(tidyr)
library(mdsr)
library(babynames)
library(dplyr)
{% endhighlight %}

Some of the code are from book "Modern Data Science with R". 

Here is a list of five important verbs to work with data frame in R.

* **select()** take a subset of columns
* **filter()** take a subset of the rows
* **mutate()** add or modify existing columns
* **arrange()** sort the rows
* **summarize()** aggregate the data across rows
* **spread()** convert a data table from narrow to wide
* **gather()** convert a data table from wide to narrow

_Each of these functions takes a data frame as its first argument, and returns a data
frame._

### select() and filter()

{% highlight r %}
head(presidential, 2)
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 4
##         name      start        end      party
##        <chr>     <date>     <date>      <chr>
## 1 Eisenhower 1953-01-20 1961-01-20 Republican
## 2    Kennedy 1961-01-20 1963-11-22 Democratic
{% endhighlight %}


{% highlight r %}
select(presidential, name, party)
{% endhighlight %}



{% highlight text %}
## # A tibble: 11 x 2
##          name      party
##         <chr>      <chr>
##  1 Eisenhower Republican
##  2    Kennedy Democratic
##  3    Johnson Democratic
##  4      Nixon Republican
##  5       Ford Republican
##  6     Carter Democratic
##  7     Reagan Republican
##  8       Bush Republican
##  9    Clinton Democratic
## 10       Bush Republican
## 11      Obama Democratic
{% endhighlight %}


{% highlight r %}
filter(presidential, party == "Republican")
{% endhighlight %}



{% highlight text %}
## # A tibble: 6 x 4
##         name      start        end      party
##        <chr>     <date>     <date>      <chr>
## 1 Eisenhower 1953-01-20 1961-01-20 Republican
## 2      Nixon 1969-01-20 1974-08-09 Republican
## 3       Ford 1974-08-09 1977-01-20 Republican
## 4     Reagan 1981-01-20 1989-01-20 Republican
## 5       Bush 1989-01-20 1993-01-20 Republican
## 6       Bush 2001-01-20 2009-01-20 Republican
{% endhighlight %}


{% highlight r %}
select(filter(presidential, start > 1973 & party == "Democratic"), name)
{% endhighlight %}



{% highlight text %}
## # A tibble: 3 x 1
##      name
##     <chr>
## 1  Carter
## 2 Clinton
## 3   Obama
{% endhighlight %}
To get the same result, we can also use the **%>%** (pipe) operation, which is more readable than above syntax. 

{% highlight r %}
presidential %>%
  filter(start > 1973 & party == "Democratic") %>%
  select(name)
{% endhighlight %}



{% highlight text %}
## # A tibble: 3 x 1
##      name
##     <chr>
## 1  Carter
## 2 Clinton
## 3   Obama
{% endhighlight %}


### mutate() and rename()

{% highlight r %}
# We are going to use the interval() and eyears() in lubridate to calculate the term
library(lubridate)
{% endhighlight %}
#### 1. Add a new column

{% highlight r %}
mypresidents <- presidential %>%
mutate(term.length = interval(start, end) / eyears(1))
head(mypresidents, 2)
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 5
##         name      start        end      party term.length
##        <chr>     <date>     <date>      <chr>       <dbl>
## 1 Eisenhower 1953-01-20 1961-01-20 Republican    8.005479
## 2    Kennedy 1961-01-20 1963-11-22 Democratic    2.838356
{% endhighlight %}
#### 2. Change column name

{% highlight r %}
mypresidents <- mypresidents %>% rename(term_length = term.length)
head(mypresidents, 2)
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 5
##         name      start        end      party term_length
##        <chr>     <date>     <date>      <chr>       <dbl>
## 1 Eisenhower 1953-01-20 1961-01-20 Republican    8.005479
## 2    Kennedy 1961-01-20 1963-11-22 Democratic    2.838356
{% endhighlight %}

### arrange()

**sort()** will sort a vector and **arrange()** will sort a data frame. 

{% highlight r %}
mypresidents %>% arrange(desc(term_length), party)
{% endhighlight %}



{% highlight text %}
## # A tibble: 11 x 5
##          name      start        end      party term_length
##         <chr>     <date>     <date>      <chr>       <dbl>
##  1    Clinton 1993-01-20 2001-01-20 Democratic    8.005479
##  2      Obama 2009-01-20 2017-01-20 Democratic    8.005479
##  3 Eisenhower 1953-01-20 1961-01-20 Republican    8.005479
##  4     Reagan 1981-01-20 1989-01-20 Republican    8.005479
##  5       Bush 2001-01-20 2009-01-20 Republican    8.005479
##  6      Nixon 1969-01-20 1974-08-09 Republican    5.553425
##  7    Johnson 1963-11-22 1969-01-20 Democratic    5.167123
##  8     Carter 1977-01-20 1981-01-20 Democratic    4.002740
##  9       Bush 1989-01-20 1993-01-20 Republican    4.002740
## 10    Kennedy 1961-01-20 1963-11-22 Democratic    2.838356
## 11       Ford 1974-08-09 1977-01-20 Republican    2.452055
{% endhighlight %}

### summarize() with group_by()
For most of the cases, **summarize()** is used with **group_by()**. **summarize()** collapses a data frame into a single row.

{% highlight r %}
mypresidents %>%
summarize(N = n(), first_year = min(year(start)), last_year = max(year(end)),
num_dems = sum(party == "Democratic"),
years = sum(term_length),
avg_term_length = mean(term_length))
{% endhighlight %}



{% highlight text %}
## # A tibble: 1 x 6
##       N first_year last_year num_dems    years avg_term_length
##   <int>      <dbl>     <dbl>    <int>    <dbl>           <dbl>
## 1    11       1953      2017        5 64.04384        5.822167
{% endhighlight %}

To have a summary row for each of the party, 

{% highlight r %}
mypresidents %>%
  group_by(party) %>%
summarize(
N = n(), first_year = min(year(start)), last_year = max(year(end)),
num_dems = sum(party == "Democratic"),
years = sum(term_length),
avg_term_length = mean(term_length))
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 7
##        party     N first_year last_year num_dems    years avg_term_length
##        <chr> <int>      <dbl>     <dbl>    <int>    <dbl>           <dbl>
## 1 Democratic     5       1961      2017        5 28.01918        5.603836
## 2 Republican     6       1953      2009        0 36.02466        6.004110
{% endhighlight %}
### Combine multiple tables

{% highlight r %}
library(nycflights13)
head(flights, 2)
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 19
##    year month   day dep_time sched_dep_time dep_delay arr_time
##   <int> <int> <int>    <int>          <int>     <dbl>    <int>
## 1  2013     1     1      517            515         2      830
## 2  2013     1     1      533            529         4      850
## # ... with 12 more variables: sched_arr_time <int>, arr_delay <dbl>,
## #   carrier <chr>, flight <int>, tailnum <chr>, origin <chr>, dest <chr>,
## #   air_time <dbl>, distance <dbl>, hour <dbl>, minute <dbl>,
## #   time_hour <dttm>
{% endhighlight %}


{% highlight r %}
head(airlines, 2)
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 2
##   carrier                   name
##     <chr>                  <chr>
## 1      9E      Endeavor Air Inc.
## 2      AA American Airlines Inc.
{% endhighlight %}
#### 1. inner_join()

{% highlight r %}
flightsJoined <- flights %>%
inner_join(airlines, by = c("carrier" = "carrier"))
glimpse(flightsJoined)
{% endhighlight %}



{% highlight text %}
## Observations: 336,776
## Variables: 20
## $ year           <int> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013,...
## $ month          <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,...
## $ day            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,...
## $ dep_time       <int> 517, 533, 542, 544, 554, 554, 555, 557, 557, 55...
## $ sched_dep_time <int> 515, 529, 540, 545, 600, 558, 600, 600, 600, 60...
## $ dep_delay      <dbl> 2, 4, 2, -1, -6, -4, -5, -3, -3, -2, -2, -2, -2...
## $ arr_time       <int> 830, 850, 923, 1004, 812, 740, 913, 709, 838, 7...
## $ sched_arr_time <int> 819, 830, 850, 1022, 837, 728, 854, 723, 846, 7...
## $ arr_delay      <dbl> 11, 20, 33, -18, -25, 12, 19, -14, -8, 8, -2, -...
## $ carrier        <chr> "UA", "UA", "AA", "B6", "DL", "UA", "B6", "EV",...
## $ flight         <int> 1545, 1714, 1141, 725, 461, 1696, 507, 5708, 79...
## $ tailnum        <chr> "N14228", "N24211", "N619AA", "N804JB", "N668DN...
## $ origin         <chr> "EWR", "LGA", "JFK", "JFK", "LGA", "EWR", "EWR"...
## $ dest           <chr> "IAH", "IAH", "MIA", "BQN", "ATL", "ORD", "FLL"...
## $ air_time       <dbl> 227, 227, 160, 183, 116, 150, 158, 53, 140, 138...
## $ distance       <dbl> 1400, 1416, 1089, 1576, 762, 719, 1065, 229, 94...
## $ hour           <dbl> 5, 5, 5, 5, 6, 5, 6, 6, 6, 6, 6, 6, 6, 6, 6, 5,...
## $ minute         <dbl> 15, 29, 40, 45, 0, 58, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ time_hour      <dttm> 2013-01-01 05:00:00, 2013-01-01 05:00:00, 2013...
## $ name           <chr> "United Air Lines Inc.", "United Air Lines Inc....
{% endhighlight %}
#### 2. left_join()
For **left_join()**, the rows of the first table are always returned, regardless of whether there is a match in the second table. 

### Convert wide to narrow and vice versa
The **spread(key = , value = )** converts a data table from narrow to wide. The **value** is the variable in the narrow format that is to be divided up into multiple variables in the resulting side format. The **key** is the name of the variable in the narrow format that identifies for each case individually which column in the wide format will receive the value.  The **gather(key = , value = )** converts a data table from wide to narrow.



{% highlight r %}
babynames %>%
group_by(name, sex) %>%
summarise(total = sum(n)) %>%
head(6)
{% endhighlight %}



{% highlight text %}
## # A tibble: 6 x 3
## # Groups:   name [6]
##        name   sex total
##       <chr> <chr> <int>
## 1     Aaban     M    87
## 2     Aabha     F    28
## 3     Aabid     M     5
## 4 Aabriella     F    15
## 5      Aada     F     5
## 6     Aadam     M   218
{% endhighlight %}
Suppose you want to find the most gender-neutral names
from all 93,889 names in babynames? For this, it would be useful to have the results in a
wide format, like the one shown below.

{% highlight r %}
BabyWide <- babynames %>%
group_by(sex, name) %>%
summarize(total = sum(n)) %>%
spread(key = sex, value = total, fill = 0)
head(BabyWide, 3)
{% endhighlight %}



{% highlight text %}
## # A tibble: 3 x 3
##    name     F     M
##   <chr> <dbl> <dbl>
## 1 Aaban     0    87
## 2 Aabha    28     0
## 3 Aabid     0     5
{% endhighlight %}
Sometimes **spread()** and **gather()** need to be used together to create the format you want.

{% highlight r %}
df1 <- data.frame(grp = c("A", "A", "B", "B"), sex = c("F", "M", "F", "M"), 
                 meanL = c(0.22, 0.47, 0.33, 0.55), sdL = c(0.11, 0.33, 0.11, 0.31))
df1
{% endhighlight %}



{% highlight text %}
##   grp sex meanL  sdL
## 1   A   F  0.22 0.11
## 2   A   M  0.47 0.33
## 3   B   F  0.33 0.11
## 4   B   M  0.55 0.31
{% endhighlight %}


{% highlight r %}
df1 %>% gather(variable, value, -(grp:sex)) %>%
  unite(temp, sex, variable, sep = ".") %>%
  spread(temp, value)
{% endhighlight %}



{% highlight text %}
##   grp F.meanL F.sdL M.meanL M.sdL
## 1   A    0.22  0.11    0.47  0.33
## 2   B    0.33  0.11    0.55  0.31
{% endhighlight %}

