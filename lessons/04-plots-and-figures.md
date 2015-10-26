---
title: "Plots and figures"
date: "03/03/2015"
layout: topic
minutes: 30 
---

## Learning Objectives 
* Generating simple statistics in R
* Applying functions to multiple rows/columns of a matrix
* Basic plots in R
* Advanced plots (introducing `ggplot`)
* Writing images (and other things) to file


## Calculating simple statistics

Let's get a closer look at our data. Each column represents a sample in our experiment, and each sample has ~38K values corresponding to the expression of different transcripts. Suppose we wanted to compute the average value for a sample, or the minimum and maximum values? The R base package provides many built-in functions such as `mean`, `median`, `min`, `max`, and `range`. Try computing the mean for "sample1" (_Hint: apply what you have learned previously on indexing_)  


```r
mean(data_ordered[,'sample1'])
```

> ### Missing values
> By default, all **R functions operating on vectors that contains missing data will return NA**. It's a way to make sure that users know they have missing data, and make a conscious decision on how to deal with it. When dealing with simple statistics like the mean, the easiest way to ignore `NA` (the missing data) is to use `na.rm=TRUE` (`rm` stands for remove). 
> In some cases, it might be useful to remove the missing data from the vector. For this purpose, R comes with the function `na.omit` to generate a vector that has NA's removed. For some applications, it's useful to keep all observations, for others, it might be best to remove all observations that contain missing data. The function
`complete.cases()` returns a logical vector indicating which rows have no missing values. 


Let's test out a few other functions:

```r
# Maximum
max(data_ordered[,'sample1'])

# Minimum
min(data_ordered[,'sample1'])
```


> ### Challenge
> Compute the [standard error](http://en.wikipedia.org/wiki/Standard_error) for "sample1". (hints: there is no built-in function to compute standard errors, but there may be functions for the different components of the formula)



## The `apply` Function
To obtain mean values for all samples we can use `mean` on each column individually, but there is also an easier way to go about it. The `apply` family of functions keep you from having to write loops (R is bad at looping) to perform some sort of operation on every row or column of a data matrix or a data frame. The family includes several functions, each differing slightly on the inputs or outputs.


```r
base::apply             Apply Functions Over Array Margins
base::by                Apply a Function to a Data Frame Split by Factors
base::eapply            Apply a Function Over Values in an Environment
base::lapply            Apply a Function over a List or Vector
base::mapply            Apply a Function to Multiple List or Vector Arguments
base::rapply            Recursively Apply a Function to a List
base::tapply            Apply a Function Over a Ragged Array
```

We will be using `apply` in our examples today, but do take a moment on your own to explore the many options that are available. The `apply` function returns a vector or array or list of values obtained by applying a function to margins of an array or matrix. We know about vectors/arrays and functions, but what are these “margins”? Margins are referring to either the rows (denoted by 1), the columns (denoted by 2) or both (1:2). By “both”, we mean  apply the function to each individual value. Let's try this with the mean function on our data:


```r
samplemeans <- apply(data_ordered, 2, mean) 
```

_How long is the vector of values returned?_

## Basic plots in R

The mathematician Richard Hamming once said, "The purpose of computing is insight, not numbers", and the best way to develop insight is often to visualize data. Visualization deserves an entire lecture (or course) of its own, but we can explore a few features of R's base plotting package.

When we are working with large sets of numbers it can be useful to display that information graphically. R has a number of built-in tools for basic graph types such as hisotgrams, scatter plots, bar charts, boxplots and much [more](http://www.statmethods.net/graphs/). We'll test a few of these out here on our `samplemeans` vector, but first we will create a combined data frame that maps our metadata to the sample mean values.


```r
# Create a combined data frame
all(rownames(metadata) == names(samplemeans)) # sanity check for sample order
df <- cbind(metadata, samplemeans) 
```


### Scatterplot
Let's start with a **scatterplot**. A scatter plot provides a graphical view of the relationship between two sets of numbers. We don't have a variable in our metadata that is a continous variable, so there is nothing to plot it against but we can plot the values against their index values just to demonstrate the function.


```r
plot(samplemeans)
```

 ![scatter-1](../figure/scatter-plot1-1.png) 

Each point represents a sample and the value on the x-axis is the sample number, where the values on the y-axis correspond to the average expression for that sample. For any plot you can customize many features of your graphs (fonts, colors, axes, titles) through [graphic options](http://www.statmethods.net/advgraphs/parameters.html)
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
In the case of our data, a **barplot**  would be much more useful. We can use `barplot` to draw a single bar representing each sample and the height indicates the average expression level. 


```r
barplot(samplemeans)
```

 ![bar-1](../figure/unnamed-chunk-10-1.png) 

The sample names appear to be too large for the plot, we can change that by changing the `cex.names` value. 


```r
barplot(samplemeans, cex.names=0.5)
```

 ![bar-2](../figure/unnamed-chunk-11-1.png) 

The names are too small to read. Alternatively we can also just change the names to be numeric values and keep the same size.


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
If we are interested in an overall distribution of values, **histogram** is a plot very commonly used. It plots the frequencies that data appears within certain ranges. To plot a histogram of the data use the `hist` command:


```r
hist(samplemeans)
```

 ![hist-1](../figure/unnamed-chunk-14-1.png) 

The range of values for sample means is 22 to 39. As you can see R will automatically calculate the intervals to use. There are many options to determine how to break up the intervals. Let's increase the number of breaks to see how that changes the plot:


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

Using addiitonal sample information from our metadata, we can use plots to compare values between the two different celltypes 'typeA' and 'typeB' using a **boxplot**. A boxplot provides a graphical view of the median, quartiles, maximum, and minimum of a data set. 


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

> ### Challenge 
> The previous challenge asked you to compute the standard error for "sample1". Using the `apply` function to generate a vector of standard error values for each sample. Use a boxplot to illustrate the differences in standard error between WT and KO samples. 



## Advanced figures (`ggplot2`)

There's also a plotting package called [`ggplot2`](http://docs.ggplot2.org/) that adds a lot of functionality to the basic plots seen above. The syntax takes some getting used to but it's extremely powerful and flexible. We can start by re-creating some of the above plots but using ggplot functions to get a feel for the syntax.

`ggplot` is best used on data in the `data.frame` form, so we will will work with our combined `df` for the following figures. Let's start by loading the `ggplot2` library.


```r
library(ggplot2)
```


### Boxplot 

The `ggplot()` command creates a plot object. In it we assign our data frame to the `data` argument, and `aes()` creates what Hadley Wickham calls an aesthetic: a mapping of variables to various parts of the plot. Note that ggplot functions can be chained with `+` signs to adding layers to the final plot. The next in chain is `geom_boxplot()`. The `geom` function specifies the geometric objects that define the graph type. The geom option is expressed as a character vector with one or more entries. Values include `geom_point`, `geom_boxplot`, `geom_line` etc


```r
ggplot(data=df, aes(x= genotype, y=samplemeans)) + 
  geom_boxplot() 
```

 ![ggbox-1](../figure/unnamed-chunk-19-1.png)


Unlike base R graphs, the ggplot graphs are not effected by many of the options set in the `par()` function (e.g. adjusting relative size of axis labels usin `cex`). They can be modified using the `theme()` function, and by adding graphic parameters. Here, we will increase the size of the axis labels and the main title. We can also change the `fill` variable to `celltype` - how does this change the plot? **What if you switch `genotype` with `celltype` in the aeshetics argument. How will that affect the figure?** 


```r
ggplot(data=df, aes(x= genotype, y=samplemeans, fill=celltype)) + 
  geom_boxplot() + 
  ggtitle('Genotype differences in average gene expression') +
  xlab('Genotype') +
  ylab('Mean expression') +
  theme(plot.title = element_text(size = rel(2.0)),
        axis.title = element_text(size = rel(1.5)),
        axis.text = element_text(size = rel(1.25)))
```

 ![ggbox-2](../figure/unnamed-chunk-21-1.png)

### Barplot
For the barplot, we need to define the graph type to `geom_bar`. Since we don't have an x variable, we need to specify the row names as our index so each sample is plotted on its own. For `fill` you can use `genotype` or `celltype` and see how the plot changes. **Can you determine how we got the axis labels on an angle?**


```r
ggplot(data=df, aes(x=row.names(df), y=samplemeans, fill=genotype)) +
  geom_bar(colour="black", stat="identity") +
  ggtitle('Average expression for each sample') +
  xlab('') +
  ylab('Mean expression') +
  theme(plot.title = element_text(size = rel(2.0)),
        axis.title = element_text(size = rel(1.5)),
        axis.text = element_text(size = rel(1.25)),
        axis.text.x = element_text(angle=45, vjust=0.5, hjust=0.6, size = rel(1.25)))
```

 ![ggbar-1](../figure/unnamed-chunk-22-1.png) 



We have only scratched the surface here. To learn more, see the [ggplot reference site](http://docs.ggplot2.org/), and Winston Chang's excellent [Cookbook for R](http://wiki.stdout.org/rcookbook/Graphs/) site. Though slightly out of date, [ggplot2: Elegant Graphics for Data Anaysis](http://www.amazon.com/ggplot2-Elegant-Graphics-Data-Analysis/dp/0387981403) is still the definative book on this subject.


## Writing figures to file

There are two ways in which figures and plots can be output to a file (rather than simply displaying on screen). The first (and easiest) is to export directly from the RStudio 'Plots' panel, by clicking on `Export` when the image is plotted. This will give you the option of `png` or `pdf` and selecting the directory to which you wish to save it to. The second option is to use R functions in the console, allowing you the flexibility to specify parameters to dictate the size and resolution of the output image. Some of the more popular formats include `pdf()`, `png`.

Initialize a plot that will be written directly to a file using `pdf`, `png` etc. Within the function you will need to specify a name for your image, and the with and height (optional). Then create a plot using the usual functions in R. Finally, close the file using the `dev.off()` function. There are also `bmp`, `tiff`, and `jpeg` functions, though the jpeg function has proven less stable than the others.



```r
pdf("figure/barplot.pdf")
ggplot(data=df, aes(x=row.names(df), y=samplemeans, fill=genotype)) +
  geom_bar(colour="black", stat="identity") +
  ggtitle('Average expression for each sample') +
  xlab('') +
  ylab('Mean expression') +
  theme(plot.title = element_text(size = rel(2.0)),
        axis.title = element_text(size = rel(1.5)),
        axis.text = element_text(size = rel(1.25)),
        axis.text.x = element_text(angle=45, vjust=0.5, hjust=0.6, size = rel(1.25)))
dev.off()
```


