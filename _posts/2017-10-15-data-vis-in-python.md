---
layout: post
title: "Data Visualization in Python"
categories: ['Data Visualization']
tags: [data visualization]
comments: true
---

This is just a collection of code I have found very useful when doing data visualization in Python. When making plots, I'd like to use `Seaborn` if possible, which is a Python visualization library based on matplotlib. It provides a high-level interface for drawing attractive statistical graphics and integrates with the functionality provided by Pandas `DataFrame`s.

Some of the code come from online resources which I will mention in the comments of the code.


```python
%matplotlib inline
```


```python
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
import pandas as pd
```

Here are the datasets from `Seaborn` that will be used in the examples.


```python
titanic = sns.load_dataset("titanic")
tips = sns.load_dataset("tips")
iris = sns.load_dataset("iris")
```


```python
titanic.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>survived</th>
      <th>pclass</th>
      <th>sex</th>
      <th>age</th>
      <th>sibsp</th>
      <th>parch</th>
      <th>fare</th>
      <th>embarked</th>
      <th>class</th>
      <th>who</th>
      <th>adult_male</th>
      <th>deck</th>
      <th>embark_town</th>
      <th>alive</th>
      <th>alone</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>7.2500</td>
      <td>S</td>
      <td>Third</td>
      <td>man</td>
      <td>True</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>no</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>71.2833</td>
      <td>C</td>
      <td>First</td>
      <td>woman</td>
      <td>False</td>
      <td>C</td>
      <td>Cherbourg</td>
      <td>yes</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>7.9250</td>
      <td>S</td>
      <td>Third</td>
      <td>woman</td>
      <td>False</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>yes</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>53.1000</td>
      <td>S</td>
      <td>First</td>
      <td>woman</td>
      <td>False</td>
      <td>C</td>
      <td>Southampton</td>
      <td>yes</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>8.0500</td>
      <td>S</td>
      <td>Third</td>
      <td>man</td>
      <td>True</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>no</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>




```python
tips.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>total_bill</th>
      <th>tip</th>
      <th>sex</th>
      <th>smoker</th>
      <th>day</th>
      <th>time</th>
      <th>size</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>16.99</td>
      <td>1.01</td>
      <td>Female</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10.34</td>
      <td>1.66</td>
      <td>Male</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>21.01</td>
      <td>3.50</td>
      <td>Male</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>23.68</td>
      <td>3.31</td>
      <td>Male</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>24.59</td>
      <td>3.61</td>
      <td>Female</td>
      <td>No</td>
      <td>Sun</td>
      <td>Dinner</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>




```python
iris.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>sepal_length</th>
      <th>sepal_width</th>
      <th>petal_length</th>
      <th>petal_width</th>
      <th>species</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5.1</td>
      <td>3.5</td>
      <td>1.4</td>
      <td>0.2</td>
      <td>setosa</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4.9</td>
      <td>3.0</td>
      <td>1.4</td>
      <td>0.2</td>
      <td>setosa</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4.7</td>
      <td>3.2</td>
      <td>1.3</td>
      <td>0.2</td>
      <td>setosa</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4.6</td>
      <td>3.1</td>
      <td>1.5</td>
      <td>0.2</td>
      <td>setosa</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5.0</td>
      <td>3.6</td>
      <td>1.4</td>
      <td>0.2</td>
      <td>setosa</td>
    </tr>
  </tbody>
</table>
</div>




```python
sns.set() # switch to seaborn defaults
```

## Bar plots and Points plot

In `Seaborn`, the `barplot()` function operates on a full dataset and shows an arbitrary estimate, using the mean by default. When there are multiple observations in each category, it also uses bootstrapping to compute a confidence interval around the estimate and plots that using error bars


```python
sns.barplot(x="sex", y="survived", hue="class", data=titanic);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_11_0.png)


If we want to show the number of observations in each category rather than computing a statistic for a second variable, we can use `countplot()`.


```python
sns.countplot(x="sex", hue='class', data=titanic, palette="Greens_d");
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_13_0.png)


An alternative style for visualizing the same information is offered by the `pointplot()` function. This function also encodes the value of the estimate with height on the other axis, but rather than show a full bar it just plots the point estimate and confidence interval.


```python
sns.pointplot(x="sex", y="survived", hue="class", data=titanic);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_15_0.png)



```python
# To make figures that reproduce well in black and white, 
# it can be good to use different markers and line styles for the levels of the hue category:
sns.pointplot(x="class", y="survived", hue="sex", data=titanic,
              palette={"male": "g", "female": "m"},
              markers=["^", "o"], linestyles=["-", "--"]);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_16_0.png)


## Box plots


```python
sns.boxplot(x="day", y="total_bill", data=tips);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_18_0.png)



```python
sns.boxplot(x="total_bill", y="day", hue="time", data=tips);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_19_0.png)


## Multi-panel categorical plots

`factorplot()` is the higher-level function that draws a categorical plot onto a FacetGrid. The default plot that is shown is a point plot, but other `Seaborn` categorical plots can be chosen with the `kind` parameter, including box plots, violin plots, bar plots, or strip plots.


```python
sns.factorplot(x="day", y="total_bill", hue="smoker",
               col="time", data=tips, kind="swarm");
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_22_0.png)



```python
# Because of the way FacetGrid works, to change the size and shape of the 
# figure you need to specify the size and aspect arguments, which apply to each facet:
sns.factorplot(x="time", y="total_bill", hue="smoker",
               col="day", data=tips, kind="box", size=4, aspect=.5);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_23_0.png)



```python
g = sns.PairGrid(tips,
                 x_vars=["smoker", "time", "sex"],
                 y_vars=["total_bill", "tip"],
                 aspect=.75, size=3.5)
g.map(sns.violinplot, palette="pastel");
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_24_0.png)


## Scatter plots


```python
plt.scatter(x="total_bill", y="tip", data=tips);
# Or
# sns.regplot(x="total_bill", y="tip", data=tips, fit_reg=False)
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_26_0.png)


## Line plots


```python
sns.lmplot(x="total_bill", y="tip", hue="smoker", data=tips,
           markers=["o", "x"], palette="Set1");
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_28_0.png)



```python
sns.lmplot(x="total_bill", y="tip", hue="smoker",
           col="time", row="sex", data=tips);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_29_0.png)


## Histogram and Densities

* `plt.hist()` will generate histogram
* `sns.kdeplot()` will generate a smooth estimate of the distribution using a kernel density estimation
* `sns.distplot()` will combine histograms and kernel density estimation
* `sns.jointplot()` will generate both the joint distribution and the marginal distribution


```python
# The code comes from the book "Python Data Science Handbook" by Jake VanderPlas
data = np.random.multivariate_normal([0, 0], [[5, 2], [2, 2]], size=2000)
data = pd.DataFrame(data, columns=['x', 'y'])

for col in 'xy':
    plt.hist(data[col], normed=True, alpha=0.5)
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_32_0.png)



```python
# The code comes from the book "Python Data Science Handbook" by Jake VanderPlas
for col in 'xy':
    sns.kdeplot(data[col], shade=True)
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_33_0.png)


If we pass two variables to `kdeplot()`, it will generate a two-dimensional plot of the kernel density estimation.


```python
sns.kdeplot(data['x'], data['y']);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_35_0.png)



```python
sns.distplot(data['x'])
sns.distplot(data['y']);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_36_0.png)



```python
# # The code comes from the book "Python Data Science Handbook" by Jake VanderPlas
with sns.axes_style('white'):
    sns.jointplot("x", "y", data, kind='kde'); # By dafault, it will the histogram instead of kde
# To generate a hexagonally based histogram, set kind='hex'
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_37_0.png)


## Pair plots

`PairGrid` is for plotting pairwise relationships in a dataset. 


```python
g = sns.PairGrid(iris, hue="species")
g = g.map_diag(plt.hist)
g = g.map_offdiag(plt.scatter)
g = g.add_legend()
# We can also specfy how the how lower triangular part and upper part, e.g.
# g = g.map_upper(plt.scatter)
# g = g.map_lower(sns.kdeplot, cmap="Blues_d")
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_40_0.png)


`pairplot` is a high-level interface for `PairGrid` that is intended to make it easy to draw a few common styles. You should use PairGrid directly if you need more flexibility.


```python
sns.pairplot(iris, hue='species', size=2.5);
```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_42_0.png)


## Facets


```python
kws = dict(s=50, linewidth=.5, edgecolor="w")
g = sns.FacetGrid(tips, col="sex", hue="time", palette="Set1",
                  hue_order=["Dinner", "Lunch"])
g = (g.map(plt.scatter, "total_bill", "tip", **kws)
    .add_legend())

```


![png](/images/2017-10-15-data-vis-in-python_files/2017-10-15-data-vis-in-python_44_0.png)


