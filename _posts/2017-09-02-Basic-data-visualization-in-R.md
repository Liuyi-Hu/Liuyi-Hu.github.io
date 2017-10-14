---
layout: post
title: "Data Visualization in R"
categories: ['Data Visualization']
tags: [data visualization]
comments: true
---

The code comes from the book "Modern Data Science with R". 

{% highlight r %}
library(ggplot2)
library(mdsr)
{% endhighlight %}

## Scatter plots
We use the 'CIACountries' dataset to illustrate. 

{% highlight r %}
head(CIACountries)
{% endhighlight %}



{% highlight text %}
##          country      pop    area oil_prod   gdp educ   roadways net_users
## 1    Afghanistan 32564342  652230        0  1900   NA 0.06462444       >5%
## 2        Albania  3029278   28748    20510 11900  3.3 0.62613051      >35%
## 3        Algeria 39542166 2381741  1420000 14500  4.3 0.04771929      >15%
## 4 American Samoa    54343     199        0 13000   NA 1.21105528      <NA>
## 5        Andorra    85580     468       NA 37200   NA 0.68376068      >60%
## 6         Angola 19625353 1246700  1742000  7300  3.5 0.04125211      >15%
{% endhighlight %}
#### 1. Basic scatter plots of 'educ' versus 'gdp'

{% highlight r %}
g <- ggplot(data=CIACountries, aes(y = gdp, x = educ))
g + geom_point(size=3) + ylab("GDP") + xlab("Education")
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/images/unnamed-chunk-3-1.svg)
#### 2. Color the points using 'net_users'

{% highlight r %}
g + geom_point(aes(color = net_users), size = 3)
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/images/unnamed-chunk-4-1.svg)
#### 3.Use 'country' to label the points

{% highlight r %}
g + geom_text(aes(label = country, color = net_users), size = 3)
{% endhighlight %}

![plot of chunk unnamed-chunk-5](/images/unnamed-chunk-5-1.svg)
#### 4. Use 'roadways' to size the points


{% highlight r %}
g + geom_point(aes(color = net_users, size = roadways))
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/images/unnamed-chunk-6-1.svg)
## Facets
Facets are multiple sidebyside graphs used to display levels of a categorical variable. 
There are two functions that create facets: **facet wrap()** and **facet grid()**.
The former creates a facet for each level of a single categorical variable, whereas the latter
creates a facet for each combination of two categorical variables, arranging them in a grid.

{% highlight r %}
g + geom_point(alpha = 0.9, aes(size = roadways)) + coord_trans(y="log10") +
facet_wrap(~net_users, nrow = 1) + theme(legend.position = "top") 
{% endhighlight %}

![plot of chunk unnamed-chunk-7](/images/unnamed-chunk-7-1.svg)

{% highlight r %}
## The following code will generate the same plots
# g + geom_point(alpha = 0.9, aes(size = roadways)) + coord_trans(y="log10") +
# facet_grid(. ~ net_users) + theme(legend.position = "top") 
{% endhighlight %}
## Box-plots
A box-and-whisker plot showing the distribution of foot length by gender for
39 children.

{% highlight r %}
ggplot(data = KidsFeet, aes(x = sex, y = length)) + geom_boxplot()
{% endhighlight %}

![plot of chunk unnamed-chunk-8](/images/unnamed-chunk-8-1.svg)


## Histogram and density plots

{% highlight r %}
g <- ggplot(data = SAT_2010, aes(x = math))
g + geom_histogram(binwidth = 10)
{% endhighlight %}

![plot of chunk unnamed-chunk-9](/images/unnamed-chunk-9-1.svg)
## Bar plots
A stacked bar plot showing the distribution of substance of abuse for participants
in the HELP study. 

{% highlight r %}
ggplot(data = HELPrct, aes(x = homeless)) +
geom_bar(aes(fill = substance), position = "fill") +
coord_flip()
{% endhighlight %}

![plot of chunk unnamed-chunk-10](/images/unnamed-chunk-10-1.svg)


{% highlight r %}
data(MedicareCharges)
ChargesNJ <- MedicareCharges %>% filter(stateProvider == "NJ")
head(ChargesNJ, 2)
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 4
## # Groups:   drg [2]
##     drg stateProvider num_charges mean_charge
##   <chr>        <fctr>       <int>       <dbl>
## 1   039            NJ          31    35103.81
## 2   057            NJ          55    45692.07
{% endhighlight %}
How do the charges in New Jersey compare to those in other states? To inspect, two
tables, one for New Jersey and one for the whole country, can be plotted with different
glyph types: bars for New Jersey and dots for the states across the whole country

{% highlight r %}
p <- ggplot(
data = ChargesNJ,
aes(x = reorder(drg, mean_charge), y = mean_charge)) +
geom_bar(fill = "gray", stat = "identity") +
ylab("Statewide Average Charges ($)") + xlab("Medical Procedure (DRG)") +
theme(axis.text.x = element_text(angle = 90, hjust = 1))
p + geom_point(data = MedicareCharges, size = 1, alpha = 0.3)
{% endhighlight %}

![plot of chunk unnamed-chunk-12](/images/unnamed-chunk-12-1.svg)


## Lines
Plot the relationship between the average SAT math score
and the expenditure per pupil (in thousands of United States dollars) among states in 2010.

{% highlight r %}
g <- ggplot(data = SAT_2010, aes(x = expenditure, y = math)) + geom_point()
g <- g + geom_smooth(method = "lm", se = 0) +
xlab("Average expenditure per student ($1000)") +
ylab("Average score on math SAT")
g
{% endhighlight %}

![plot of chunk unnamed-chunk-13](/images/unnamed-chunk-13-1.svg)
A third (categorical) variable can be added through faceting and/or layering. In this case,
we use the **mutate()** function  to create a new variable called SAT rate that places states into bins (e.g., high, medium, low) based on the percentage of students taking
the SAT. Additionally, in order to include that new variable in our plots, we use the %+%
operator to update the data frame that is bound to our plot.

{% highlight r %}
SAT_2010 <- SAT_2010 %>%
mutate(SAT_rate = cut(sat_pct, breaks = c(0,30,60,100),
labels = c("low", "medium", "high")))
g <- g %+% SAT_2010
g + aes(color = SAT_rate)
{% endhighlight %}

![plot of chunk unnamed-chunk-14](/images/unnamed-chunk-14-1.svg)
A time series showing the change in temperature at the MacLeish field station
in 2015.

{% highlight r %}
library(macleish)
ggplot(data = whately_2015, aes(x = when, y = temperature)) +
geom_line(color = "darkgray") + geom_smooth() +
xlab(NULL) + ylab("Temperature (degrees Fahrenheit)")
{% endhighlight %}

![plot of chunk unnamed-chunk-15](/images/unnamed-chunk-15-1.svg)

## Extended examples
#### 1. Historical baby names

{% highlight r %}
library('babynames')
BabynamesDist = make_babynames_dist()
head(BabynamesDist, 2)
{% endhighlight %}



{% highlight text %}
## # A tibble: 2 x 9
##    year   sex  name     n       prop alive_prob count_thousands age_today
##   <dbl> <chr> <chr> <int>      <dbl>      <dbl>           <dbl>     <dbl>
## 1  1900     F  Mary 16707 0.05257494          0          16.707       114
## 2  1900     F Helen  6343 0.01996066          0           6.343       114
## # ... with 1 more variables: est_alive_today <dbl>
{% endhighlight %}


{% highlight r %}
joseph <- BabynamesDist %>% filter(name == "Joseph" & sex == "M")
name_plot <- ggplot(data = joseph, aes(x = year))
name_plot <- ggplot(data = joseph, aes(x = year))

## bind joseph and aethetic year
# The geom bar() function adds bars, which are flled with a light blue color and a white
# border. The height of the bars is an aesthetic that is mapped to the estimated number of
# people alive today who were born in each year. The stat argument is set to identity,
#since we want the actual y values to be used|not the number of each (which is the default).
name_plot <- name_plot +
  geom_bar(stat = "identity", aes(y = count_thousands * alive_prob),
           fill = "#b2d7e9", colour = "white")
name_plot <- name_plot + geom_line(aes(y = count_thousands), size = 2)
name_plot <- name_plot +
  ylab("Number of People (thousands)") + xlab(NULL)

## Compute median
library(Hmisc)
wtd.quantile <- Hmisc::wtd.quantile
?wtd.quantile
median_yob <-
  with(joseph, wtd.quantile(year, est_alive_today, probs = 0.5))

## Plot the median 
name_plot <- name_plot +
geom_bar(stat = "identity", colour = "white", fill = "#008fd5",
aes(y = ifelse(year == median_yob, est_alive_today / 1000, 0)))

## Add a title, annotated text, and an arrow providing focus to a specific element of the plot
name_plot +
  ggtitle("Age Distribution of American Boys Named Joseph") +
  geom_text(x = 1935, y = 40, label = "Number of Josephs\nborn each year") +
  geom_text(x = 1915, y = 13, label =
              "Number of Josephs\nborn each year\nestimated to be alive\non 1/1/2014",
            colour = "#b2d7e9") +
  geom_text(x = 2003, y = 40,
            label = "The median\nliving Joseph\nis 37 years old",
            colour = "darkgray") +
  geom_curve(x = 1995, xend = 1974, y = 40, yend = 24,
             arrow = arrow(length = unit(0.3,"cm")), curvature = 0.5) + ylim(0, 42)
{% endhighlight %}

![plot of chunk unnamed-chunk-17](/images/unnamed-chunk-17-1.svg)
If we make analogous plot for anohter name, we do not need to update the **name_plot** object with contextual informaiton. Instead, we just need to update the **data** information by using the special **%+%** operator

{% highlight r %}
many_names_plot <- name_plot + facet_grid(name ~ sex)
mnp <- many_names_plot %+% filter(BabynamesDist, name %in% c("Jessie", "Marion", "Jackie"))
mnp
{% endhighlight %}

![plot of chunk unnamed-chunk-18](/images/unnamed-chunk-18-1.svg)
#### 2. Most common women's names
We need to figure out
what the 25 most common female names are among those estimated to be alive today. We
can do this by counting the estimated number of people alive today for each name, filtering
for women, sorting by the number estimated to be alive, and then taking the top 25 results.
We also need to know the median age, as well as the first and third quartiles for age among
people having each name.

{% highlight r %}
## Extract the summary statistics for plots
com_fem <- BabynamesDist %>%
filter(sex == "F") %>%
group_by(name) %>%
summarise(
N = n(), est_num_alive = sum(est_alive_today),
q1_age = wtd.quantile(age_today, est_alive_today, probs = 0.25),
median_age = wtd.quantile(age_today, est_alive_today, probs = 0.5),
q3_age = wtd.quantile(age_today, est_alive_today, probs = 0.75)) %>%
arrange(desc(est_num_alive)) %>%
head(25)
{% endhighlight %}


{% highlight r %}
## Make the plot
w_plot <- ggplot(data = com_fem, aes(x = reorder(name, -median_age),
y = median_age)) + xlab(NULL) + ylab("Age (in years)") +
ggtitle("Median ages for females with the 25 most common names")

# Add gold rectangles
w_plot <- w_plot + geom_linerange(aes(ymin = q1_age, ymax = q3_age),
color = "#f3d478", size = 10, alpha = 0.8)

# Add red dot with white boarder indicating the median age for each of these names
w_plot <- w_plot +
geom_point(fill = "#ed3324", colour = "white", size = 4, shape = 21)

# Add context and flip the plot
w_plot +
geom_point(aes(y = 55, x = 24), fill = "#ed3324", colour = "white",
size = 4, shape = 21) +
geom_text(aes(y = 58, x = 24, label = "median")) +
geom_text(aes(y = 26, x = 16, label = "25th")) +
geom_text(aes(y = 51, x = 16, label = "75th percentile")) +
geom_point(aes(y = 24, x = 16), shape = 17) +
geom_point(aes(y = 56, x = 16), shape = 17) +
coord_flip()
{% endhighlight %}

![plot of chunk unnamed-chunk-20](/images/unnamed-chunk-20-1.svg)

