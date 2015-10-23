---
layout: topic
title: Functions in R
author: Data Carpentry contributors
minutes: 45
---

## Learning Objectives

* Familiarize participants with functions in R
* Get exposed to a few commonly used functions
* Explore and inspect different data structures


## Functions and their arguments

The other key feature of R is functions. Functions are **"self contained" modules of code that accomplish a specific task**. Functions usually take in some sort of data structure (value, vector, dataframe etc.), process it, and return a result.

The input(s) are called **arguments** and can be anything, not only numbers or filenames, but also other objects. Exactly what each argument means differs per function, and must be looked up in the documentation (? for help). If an argument alters the way the function operates, such as whether to ignore 'bad values', sit is sometimes called an *option*.

Most functions can take several arguments, but many have so-called *defaults*. If you don't specify such an argument when calling the function, the function
itself will fall back on using the *default*. This is a standard value that the author of the function specified as being "good enough in standard cases". An
example would be what symbol to use in a plot. However, if you want something specific, simply change the argument yourself with a value of your choice.

We have already used a few examples of basic functions in the previous lessons i.e `c()`, and  `factor()`. These functions are available as part of R's built in capabilities, and we will explore a few more of these base functions below. You can also get functions from libraries (which we'll talk about in a bit), or even write your own. 


Let's revisit a function that we have used previously to combine data `c()`. The arguments it takes is any number of numbers, characters or strings and performs the task of combining them into a single vector. You can also use it to add elements to an existing vector:


	glengths <- c(glengths, 90) # adding at the end	
	glengths <- c(30, glengths) # adding at the beginning


What happens here is that we take the original vector `glengths`, and we are adding another item first to the end of the other ones, and then another item at
the beginning. We can do this over and over again to build a vector or a dataset.

Since R is used for statistical computing, many of the base functions involve mathematical operations. One example would be the function `sqrt()`. The input (argument) must be a number, and the the output is the square root of that number. Let's try finding the square root of 81:


	sqrt(81)

> Executing a function (or 'running it') is called *calling* the function.

Now what would happen if we called the function on a *vector of values* instead of a single value?

	sqrt(glengths)


In this case the task was performed on each individual value of the vector `number` and the respective results were displayed.


Let's try a function that we can change some of the *options*, for example `round`:


	round(3.14159)


We can see that we get `3`. That's because the default is to round to the nearest whole number. If we want more digits we can see 
how to do that by getting information about the `round` function. We can use `args(round)` or look at thehelp for this function using `?round`.


	args(round)

	?round


We see that if we want a different number of digits, we can type `digits=2` or however many we want.


	round(3.14159, digits=2)


If you provide the arguments in the exact same order as they are defined (in the help manual) you don't have to name them:


	round(3.14159, 2)


However, it's usually not recommended practice because it's a lot of remembering to do, and if you share your code with others that includes less known functions
it makes your code difficult to read. (It's however OK to not include the names of the arguments for basic functions like `mean`, `min`, etc...). Another advantage of naming arguments, is that the order doesn't matter.  This is useful when there start to be more arguments. 



## Using functions to explore data structures

There are also a selection of base functions in R that are useful for inspecting your data and summarizing it. Let's start with a simple data structure such as vectors. A commonly used function is `length()`, which tells you how many elements are in a particular vector:


	length(glengths)
	length(species)


The `class()` function is useful in indicating the datatype or data structure of a variable. So for example if we were interested in knowing what was inside `glengths`:

	class(glengths)


We could also use class on a data frame or any other type of object. Let's load in a data frame to test out some more functions. We will use `read.csv` to read in data from a csv (comma separated values) file. There are numerous other functions to load in data depending on your filetype, but `read.csv` is one of the more commonly used ones.

> Note: When typing out read.csv try pressing the `Tab` key after typing only `read`. You will find that a drop-down menu will appear listing all `read` options for loading in files. The window to the right gives you more information on the function and its arguments as you scroll down and highlight each individually.


	metadata <- read.csv(file='meta/mouse_exp_design.csv')


The function has *one required argument* and several *options* that can be changed. The mandatory argument is a path to the file and filename, which in our  case is  `mouse_exp_design.csv` file. We will put the function to the right of the assignment operator, meaning that any output will be saved as the variable name provided on the left.

Take a look at the file by typing out the variable name `metadata` and pressing return. The file contains information describing the samples in our study. Each row holds information for a single sample, and the columns represent `genotype`(WT or KO),  `celltype` (typeA or typeB), and `replicate number`.


	metadata

```
##          genotype celltype replicate
## sample1        Wt    typeA         1
## sample2        Wt    typeA         2
## sample3        Wt    typeA         3
## sample4        KO    typeA         1
## sample5        KO    typeA         2
## sample6        KO    typeA         3
## sample7        Wt    typeB         1
## sample8        Wt    typeB         2
## sample9        Wt    typeB         3
## sample10       KO    typeB         1
## sample11       KO    typeB         2
## sample12       KO    typeB         3
```

__At this point, make sure all participants have the data loaded__


> Note: By default, `data.frame` converts (= coerces) columns that contain characters (i.e., text) into the `factor` data type. Depending on what you want to do with
> the data, you may want to keep these columns as `character`. To do so, `read.csv()` and `read.table()` have an argument called `stringsAsFactors` which can be set to `FALSE`.


Suppose we had a larger file, we might not want to display all the contents in the console. Instead we could check the top (the first 6 lines) of this `data.frame` using the function `head()`:


	head(metadata)


Let's now check the __str__ucture of this `data.frame` in more details with thefunction `str()`:

	str(metadata)


```
## 'data.frame':	12 obs. of  3 variables:
##  $ genotype : Factor w/ 2 levels "KO","Wt": 2 2 2 1 1 1 2 2 2 1 ...
##  $ celltype : Factor w/ 2 levels "typeA","typeB": 1 1 1 1 1 1 2 2 2 2 ...
##  $ replicate: int  1 2 3 1 2 3 1 2 3 1 ...
```

As you can see, the columns `genotype` and `celltype` are of a special class called `factor` whereas the replicate column has been interpreted as integer data type.

__You can also get this information from the "Environment" tab in RStudio.__

### Inspecting `data.frame` objects

We already saw how the functions `head()` and `str()` can be useful to check the
content and the structure of a `data.frame`. Here is a non-exhaustive list of
functions to get a sense of the content/structure of the data.

* Size:
    * `dim()` - returns a vector with the number of rows in the first element, and
    the number of columns as the second element (the __dim__ensions of the object)
    * `nrow()` - returns the number of rows
    * `ncol()` - returns the number of columns
* Content:
    * `head()` - shows the first 6 rows
    * `tail()` - shows the last 6 rows
* Names:
    * `names()` - returns the column names (synonym of `colnames()` for `data.frame`
	objects)
   * `rownames()` - returns the row names
* Summary:
   * `str()` - structure of the object and information about the class, length and
	content of  each column
   * `summary()` - summary statistics for each column

Note: most of these functions are "generic", they can be used on other types of
objects besides `data.frame`.

> ### Challenge 
> * What is the class of the object `metadata`?
> * How many rows and how many columns are in this object?
> * Load in data again, storing it as `test_data` and using `stringsAsFactors=F`. How does this change the structure of the data?



