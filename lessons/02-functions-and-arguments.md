---
layout: topic
title: Functions in R
author: Data Carpentry contributors
minutes: 20
---

## Learning Objectives

* Understanding the structure of a function and how it is used
* Exposure to a few commonly used base functions in R
* An brief introduction to packages and libraries in R 
* Knowing where and how to find help 


## Functions and their arguments

A key feature of R is functions. Functions are **"self contained" modules of code that accomplish a specific task**. Functions usually take in some sort of data structure (value, vector, dataframe etc.), process it, and return a result.

The general usage for a function is the name of the function followed by parantheses:

	function_name(input)

The input(s) are called **arguments** and can be anything, not only numbers or characters, but also other data structures. Exactly what each argument means differs per function, and must be looked up in the documentation (we will discuss help options at the end of this lesson). Arguments serve two main purposes: 1) they can be a physical object (any data structure) on which the function carrries out a task on or 2) an argument can provide specifications that alter the way the function operates. This will become mroe clear as we start to use functions later in the lesson.

Most functions can take several arguments, but many have so-called *defaults*. If you don't specify such an argument when calling the function, the function
itself will fall back on using the *default*. This is a standard value that the author of the function specified as being "good enough in standard cases". An
example would be what symbol to use in a plot. However, if you want something specific, simply change the argument yourself with a value of your choice.

We have already used a few examples of basic functions in the previous lessons i.e `c()`, and  `factor()`. These functions are available as part of R's built in capabilities, and we will explore a few more of these base functions below. You can also get functions from libraries (which we'll talk about in a bit), or even write your own. 

Let's revisit a function that we have used previously to combine data `c()`. The arguments it takes is a collection of numbers, characters or strings (separated by a comma) and performs the task of combining them into a single vector. You can also use it to add elements to an existing vector:


	glengths <- c(glengths, 90) # adding at the end	
	glengths <- c(30, glengths) # adding at the beginning


What happens here is that we take the original vector `glengths` (containing three elements), and we are adding another item to either to the end. We can do this over and over again to build a vector or a dataset.

Since R is used for statistical computing, many of the base functions involve mathematical operations. One example would be the function `sqrt()`. The input/argument must be a number, and the the output is the square root of that number. Let's try finding the square root of 81:


	sqrt(81)

> Executing a function (or 'running it') is referred to as *calling* the function.

Now what would happen if we called the function on a *vector of values* instead of a single value?

	sqrt(glengths)


In this case the task was performed on each individual value of the vector `number` and the respective results were displayed.


Let's try another function, this time using one that we can change some of the *options*, for example `round`:


	round(3.14159)


We can see that we get `3`. That's because the default is to round to the nearest whole number. If we want a different number of digits, we can type `digits=2` or however many we may want.


	round(3.14159, digits=2)


If you provide the arguments in the exact same order as they are defined (in the help manual) you don't have to name them:


	round(3.14159, 2)


However, it's usually not recommended practice because it's a lot of remembering to do, and if you share your code with others that includes less known functions
it makes your code difficult to read. (It's however OK to not include the names of the arguments for basic functions like `mean`, `min`, etc...). Another advantage of naming arguments, is that the order doesn't matter.  This is useful when there start to be more arguments. 

***
**Exercise** 

1. Another commonly used base function is `mean()`. Use this function to calculate an average for the `glengths` vector.

***

## Packages and Libraries

**Packages** are collections of R functions, data, and compiled code in a well-defined format. The directory where packages are stored is called the **library**. The two terms are sometimes used synonomously and there has been [discussion](http://www.r-bloggers.com/packages-v-libraries-in-r/) amongst the community to resolve this. It is somewhat counter-intuitive to _load a package_ using the `library()` function and so you can see how confusion can arise.


There are a set of **standard (or base) packages** which are considered part of the R source code and automatically available as part of your R installation. Base packages contain the **basic functions** that allow R to work, and enable standard statistical and graphical functions on datasets; for example all of the functions that we have been using so far in our examples.

You can check what base packages are loaded by typing into the console:

	sessionInfo()


In this workshop we will mostly be using functions from the standard base packages. However, the more you work with R you will come to realize that there is a cornucopia of R packages that offer a wide variety of functionality. To use additional packages will require installation.

 
Packages for R can be installed from the [CRAN](http://cran.r-project.org/) package repository using the `install.packages` function. An example is given below for the `ggplot2` package that will be required for some images we will create later on. Run this code to install `ggplot2`.


```r
install.packages('ggplot2')
```

Alternatively, packages can also be installed from [Bioconductor](https://www.bioconductor.org/), another repository of packages but mostly pertaining to genomic data analysis. There are many packages that are available in CRAN and Bioconductor, but there are also packages that are specific to one repository. Generally, you can find out this information with a Google search or by trial and error. To install from Bioconductor, you will first need to install Bioconductor and all the standard packages. *This only needs to be done once ever for your R installation.* 

**If you were successful with the installation from CRAN, you do not need to run this**

```r
source("http://bioconductor.org/biocLite.R")
biocLite()
```

Once you have the standard packages installed, you can install additional packages using the `biocLite.R` script. If it's a new R session you will also have to source the script again. Here we show that the same package `ggplot2` is available through Bioconductor:


```r
biocLite('ggplot2')
```

Finally, R packages can also be installed from source. This is useful when you do not have an internet connection (and have the source files locally), since the other two methods are retrieving the source files from remote sites. For this class, if the other two methods for installation did not work for you can install `ggplot2` from source. (We have provided for you a compressed file containing all the required information to build and install the package in the repo). 

To install from source, we use the same `install.packages` function but we have additional *arguments* that provide specifications to *change from defaults*:

	install.packages('ggplot2_1.0.1.tar.gz', type="source", repos=NULL)


### Loading libraries
Once you have the package installed, you can load it into your R session for use. Any of the functions that are specific to that package will be available for you to use by simply calling the function as you would for any of the base functions. *Note that quotations are not required here.*


```r
library(ggplot2)
```

## Seeking help

### I know the name of the function I want to use, but I'm not sure how to use it

Suppose we didn't know how to use the `round` function and wanted more digits; the best way of finding out this information is to use the `?` followed by the name of the function. Doing this will open up the help manual in the bottom right panel of RStudio: 

	?round


If you know the function, but just need to remind yourself of the names of the arguments, you can use:

	args(round)


### I want to use a function that does X, there must be a function for it but I don't know which one...

If you are looking for a function to do a particular task, you can use `help.search()` (but only looks through the installed packages):

	help.search("scatter")

If you can't find what you are looking for, you can use the [rdocumention.org](http://www.rdocumentation.org) website that search through
the help files across all packages available.

***
**Exercise**

1. Compute the [standard error](http://en.wikipedia.org/wiki/Standard_error) for `glengths`. This will require multiple functions, and you may need to use the `?help` to find out more. (Hint: SE is the standard deviation divided by the square root of *n*; where n is the number of observations.)


2. Suppose we wanted to add age information for all of our samples in the `metadata` dataframe. Let's investigate how to use the `cbind` function to add the following `age` vector to the end of our `metadata` dataframe. Name the new dataframe `all_data`.

	```
		age <- c(4,5,5,5,5,4,5,4,5,5,5,4)
	```	

***

### I am stuck... I get an error message that I don't understand

Start by googling the error message. However, this doesn't always work very well
because often, package developers rely on the error catching provided by R. You
end up with general error messages that might not be very helpful to diagnose a
problem (e.g. "subscript out of bounds").

However, you should check stackoverflow. Search using the `[r]` tag. Most
questions have already been answered, but the challenge is to use the right
words in the search to find the answers:
[http://stackoverflow.com/questions/tagged/r](http://stackoverflow.com/questions/tagged/r)

The [Introduction to R](http://cran.r-project.org/doc/manuals/R-intro.pdf) can
also be dense for people with little programming experience but it is a good
place to understand the underpinnings of the R language.

The [R FAQ](http://cran.r-project.org/doc/FAQ/R-FAQ.html) is dense and technical
but it is full of useful information.

### Asking for help

The key to get help from someone is for them to grasp your problem rapidly. You
should make it as easy as possible to pinpoint where the issue might be.

Try to use the correct words to describe your problem. For instance, a package
is not the same thing as a library. Most people will understand what you meant,
but others have really strong feelings about the difference in meaning. The key
point is that it can make things confusing for people trying to help you. **Be as
precise as possible when describing your problem.**

If possible, try to reduce what doesn't work to a simple reproducible
example. If you can reproduce the problem using a very small `data.frame`
instead of your 50,000 rows and 10,000 columns one, provide the small one with
the description of your problem. When appropriate, try to generalize what you
are doing so even people who are not in your field can understand the question.

To share an object with someone else, if it's relatively small, you can use the
function `dput()`. It will output R code that can be used to recreate the exact same
object as the one in memory:

```{r, results='show'}
dput(head(iris)) # iris is an example data.frame that comes with R
```

If the object is larger, provide either the raw file (i.e., your CSV file) with
your script up to the point of the error (and after removing everything that is
not relevant to your issue). Alternatively, in particular if your questions is
not related to a `data.frame`, you can save any other R data structure that you have in your environment to a file:

```{r, eval=FALSE}
save(iris, file="/tmp/iris.RData")
```

The content of this file is however not human readable and cannot be posted
directly on stackoverflow. It can however be sent to someone by email who can read
it with this command:

```{r, eval=FALSE}
some_data <- load(file="~/Downloads/iris.RData")
```

Last, but certainly not least, **always include the output of `sessionInfo()`**
as it provides critical information about your platform, the versions of R and
the packages that you are using, and other information that can be very helpful
to understand your problem.

	sessionInfo()

### Where to ask for help?

* Your friendly colleagues: if you know someone with more experience than you,
  they might be able and willing to help you.
* Stackoverlow: if your question hasn't been answered before and is well
  crafted, chances are you will get an answer in less than 5 min.
* The [R-help](https://stat.ethz.ch/mailman/listinfo/r-help): it is read by a
  lot of people (including most of the R core team), a lot of people post to it,
  but the tone can be pretty dry, and it is not always very welcoming to new
  users. If your question is valid, you are likely to get an answer very fast
  but don't expect that it will come with smiley faces. Also, here more than
  everywhere else, be sure to use correct vocabulary (otherwise you might get an
  answer pointing to the misuse of your words rather than answering your
  question). You will also have more success if your question is about a base
  function rather than a specific package.
* If your question is about a specific package, see if there is a mailing list
  for it. Usually it's included in the DESCRIPTION file of the package that can
  be accessed using `packageDescription("name-of-package")`. You may also want
  to try to email the author of the package directly.
* There are also some topic-specific mailing lists (GIS, phylogenetics, etc...),
  the complete list is [here](http://www.r-project.org/mail.html).

### More resources
* The [Posting Guide](http://www.r-project.org/posting-guide.html) for the R
  mailing lists.
* [How to ask for R help](http://blog.revolutionanalytics.com/2014/01/how-to-ask-for-r-help.html)
  useful guidelines

---
*The materials used in this lesson is adapted from work that is Copyright © Data Carpentry (http://datacarpentry.org/). 
All Data Carpentry instructional material is made available under the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0).*

