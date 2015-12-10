---
title: "Plots and figures"
date: "03/03/2015"
layout: topic
minutes: 60 
---

## Learning Objectives 
* Generating simple statistics in R
* Applying functions to multiple rows/columns of a matrix
* Visualizing data using basic plots in R


## Calculating simple statistics

Let's take a closer look at our data. Each column represents a sample in our experiment, and each sample has ~38K values corresponding to the expression of different transcripts. Suppose we wanted to compute the average value for a sample, or the minimum and maximum values? As mentioned previously, R is used for statistical computing therefore many of the base functions involve mathematical operations. The R base package provides many built-in functions such as `mean`, `median`, `min`, `max`, and `range`, just to name a few. Try computing the mean for "sample1" (_Hint: apply what you have learned previously using indexes_)  

	mean(rpkm_ordered[,'sample1'])


> ### Missing values
> By default, all **R functions operating on vectors that contains missing data will return NA**. It's a way to make sure that users know they have missing data, and make a conscious decision on how to deal with it. When dealing with simple statistics like the mean, the easiest way to ignore `NA` (the missing data) is to use `na.rm=TRUE` (`rm` stands for remove). 
> In some cases, it might be useful to remove the missing data from the vector. For this purpose, R comes with the function `na.omit` to generate a vector that has NA's removed. For some applications, it's useful to keep all observations, for others, it might be best to remove all observations that contain missing data. The function
`complete.cases()` returns a logical vector indicating which rows have no missing values. 


Let's test out a few other functions:

```r
# Maximum
max(rpkm_ordered[,'sample1'])

# Minimum
min(rpkm_ordered[,'sample1'])
```


## The `apply` Function
To obtain mean values for all samples we can use `mean` on each column individually, but there is also an easier way to go about it. The `apply` family of functions keep you from having to write loops (R is bad at looping) to perform some sort of operation on every row or column of a data matrix or a data frame. The family includes several functions, each differing slightly on the inputs or outputs.


```r
base::apply             Apply Functions Over Array Margins
base::by                Apply a Function to a Data Frame Split by Factors
base::eapply            Apply a Function Over Values in an Environment
base::lapply            Apply a Function over a List or Vector (returns list)
base::sapply            Apply a Function over a List or Vector (returns vector)
base::mapply            Apply a Function to Multiple List or Vector Arguments
base::rapply            Recursively Apply a Function to a List
base::tapply            Apply a Function Over a Ragged Array
```

We will be using `apply` in our examples today, but do take a moment on your own to explore the many options that are available. The `apply` function returns a vector or array or list of values obtained by applying a function to margins of an array or matrix. We know about vectors/arrays and functions, but what are these “margins”? Margins are referring to either the rows (denoted by 1), the columns (denoted by 2) or both (1:2). By “both”, we mean  apply the function to each individual value. 

Let's try this to obtain mean expression values for each sample in our RPKM matrix:

	samplemeans <- apply(rpkm_ordered, 2, mean) 



## Basic plots in R

The mathematician Richard Hamming once said, "The purpose of computing is insight, not numbers", and the best way to develop insight is often to visualize data. Visualization deserves an entire lecture (or course) of its own, but we can explore a few features of R's base plotting package.

When we are working with large sets of numbers it can be useful to display that information graphically. R has a number of built-in tools for basic graph types such as hisotgrams, scatter plots, bar charts, boxplots and much [more](http://www.statmethods.net/graphs/). We'll test a few of these out here on our `samplemeans` vector, but first we will create a combined data frame that maps our metadata to the sample mean values.


```r
# Create a combined data frame
all(rownames(metadata) == names(samplemeans)) # sanity check for sample order
df <- cbind(metadata, samplemeans) 
```


### Scatterplot
Let's start with a **scatterplot**. A scatter plot provides a graphical view of the relationship between two sets of continuous numbers. We don't have another variable in our metadata that is a continuous variable, so there is nothing to plot `samplemeans` against, but we can plot the values against their index values just to demonstrate the function.


```r
plot(samplemeans)
```

 ![scatter-1](../figure/scatter-plot1-1.png) 

Each point represents a sample and the value on the x-axis is the sample number (independent variable), where the values on the y-axis correspond to the average expression for that sample (dependent variable). For any plot you can customize many features of your graphs (fonts, colors, axes, titles) through [graphic options](http://www.statmethods.net/advgraphs/parameters.html).
We can change the shape of the data point using `pch`.


```r
plot(samplemeans, pch=8)
```

 ![scatter-2](../figure/unnamed-chunk-8-1.png)

We can add a title to the plot by assigning a string to `main`


```r
plot(samplemeans, pch=8, main="Scatter plot of mean values")
```

 ![scatter-3](../figure/unnamed-chunk-9-1.png) 

## Barplot
Barplots are useful for representing the distribution of a quanitative variable (numeric) across categories of a categorical variable. A **barplot**  would be much more useful to compare the samplemeans (numeric variable) for each sample (catagorical variable). We can use `barplot` to draw a single bar representing each sample and the height indicates the average expression level. 


```r
barplot(samplemeans)
```

 ![bar-1](../figure/unnamed-chunk-10-1.png) 

The sample names appear to be too large for the plot, we can change that by changing the `cex.names` value. 


```r
barplot(samplemeans, cex.names=0.5)
```

 ![bar-2](../figure/unnamed-chunk-11-1.png) 

The names are too small to read. Alternatively, we can also just change the names to be numeric values and keep the same size.


```r
barplot(samplemeans, names.arg=c(1:12)) # supply numbers as labels
```

 ![bar-3](../figure/unnamed-chunk-12-1.png) 

We can also flip the axes so that the plot is projected horizontally.


```r
barplot(samplemeans, names.arg=c(1:12), horiz=TRUE) 
```

 ![bar-4](../figure/unnamed-chunk-13-1.png) 

## Histogram
If we are interested in an overall distribution of values, a **histogram** is a plot very commonly used. It plots the frequencies that data appears within certain ranges. To plot a histogram of the data use the `hist` command:


```r
hist(samplemeans)
```

 ![hist-1](../figure/unnamed-chunk-14-1.png) 

The range of values for sample means is 9 to 16. As you can see R will automatically calculate the intervals to use. There are many options to determine how to break up the intervals. Let's increase the number of breaks to see how that changes the plot:


```r
hist(samplemeans, xlab="Mean expression level", main="", breaks=20) 
```

 ![hist-2](../figure/hist-1.png) 

Similar to the other plots we can tweak the aesthetics. Let's color in the bar and remove the borders:


```r
hist(samplemeans, xlab="Mean expression level", main="", col="darkgrey", border=FALSE) 
```

 ![hist-3](../figure/unnamed-chunk-15-1.png) 

##Boxplot

Using addiitonal sample information from our metadata, we can use plots to compare values between the two different celltypes 'typeA' and 'typeB' using a **boxplot**. A boxplot provides a graphical view the center and spread of the distribution, including the median (50th percentile), first and third quartiles (25th and 75th percentiles, respectively), maximum, and minimum of a data set. In a boxplot, the box extends from the first (Q1) to the third quartile (Q3), with the line in the middle marking the median. The whiskers of the plot reach the minimum and maximum values that are not outliers. 

Outliers are determined using the interquartile range (IQR), which is defined as: Q3 - Q1. Any values that exceeds 1.5 x IQR below Q1 or above Q3 are considered outliers and are represented as points above or below the whiskers. These outliers are useful to identify any unexpected observations.


```r
# Boxplot
boxplot(samplemeans~celltype, df)
```

 ![box-1](../figure/boxplot-1.png) 

Similar to the plots above, we can pass in arguments to add in extras like plot title, axis labels and colors.


```r
boxplot(samplemeans~celltype, df,  col=c("blue","red"), main="Average expression differences between celltypes", ylab="Expression")
```

 ![box-2](../figure/unnamed-chunk-16-1.png) 





---
*The materials used in this lesson is adapted from work that is Copyright © Data Carpentry (http://datacarpentry.org/). 
All Data Carpentry instructional material is made available under the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0).*
