---
layout: topic
title: Introduction to R
author: Data Carpentry contributors
minutes: 20
---

## Learning Objectives

* Become familiar with R syntax
* Understand variables and the assignment operator in R
* Understand the various data types and data structures in R


## The R syntax

### Example script

```{r example-script}

# Load libraries
library(Biobase)
library(limma)
library(ggplot2)

# Setup directory variables
baseDir <- getwd()
dataDir <- file.path(baseDir, "data")
metaDir <- file.path(baseDir, "meta")
resultsDir <- file.path(baseDir, "results")

# Load data
meta <- read.delim(file.path(metaDir, '2015-1018_sample_key.csv'), header=T, sep="\t", row.names=1)
```

* Point to the different parts:
  - the **comments** `#` and how they are used to document function and its content
  - the **assignment operator** `<-`
  - **variables** and **objects**
  - a **function** with arguments
  - the `=` for **arguments**

_NOTE: indentation and consistency in spacing to improve clarity and legibility_


You can get output from R simply by typing in math in the console. Test it out:

```{r, purl=FALSE}
3 + 5
12/7
```

We can also type it in the editor and add comments to explain what it is we're doing. Use the R shortcuts to run it directly in the console.

```{r, purl=FALSE}
# I am adding 3 and 5. R is fun!
3+5
```

What happens if we do that same command without the comments? Re-run the command after removing the # sign in the front:

```{r, purl=FALSE, eval=FALSE}
I am adding 3 and 5. R is fun!
3+5
```

Now R is trying to run that sentence as a command, and it 
doesn't work. We get an error in the console "Error: unexpected symbol in "I am" means that the R interpreter did not know what to do with that command.

Ok so it's great that R is a glorified caluculator, but obviously
we want to do more interesting things. 

To do useful and interesting things, we need to assign _values_ to
_variables_. 



## Assignment operator

`<-` is the assignment operator. It assigns values on the right to variables on
the left. So, after executing `x <- 3`, the value of `x` is `3`. The arrow can
be read as 3 **goes into** `x`.  

*In RStudio, typing `Alt + -` (push `Alt`, the key next to your space bar at the same time as the `-` key) will write ` <- ` in a single keystroke.*


## Variables

A variable is a symbolic name for (or reference to) information. Variables in computer programming are analogous to "buckets", where information can be maintained and referenced. On the outside of the bucket is a name. When referring to the bucket, we use the name of the bucket, not the data stored in the bucket.

In the example above, we created a variable or a 'bucket' called `x`. Inside we put a value. Let's create another variable called `y`and give it a value of 5. When assigning a value to an variable, R does not print anything to the console. You can force to
print the value by using parentheses or by typing the name.

```
y <- 5
```

Now we can reference these buckets by name to perform mathematical operations on the values contained within. What do you get in the console? 

```
x + y
```

Try assigning the results of this operation to another variable called `number`. 

```
number <- x + y
```

### Exercise 

- What happens if we change the value of what's inside `x` to 5. What happens to `number`?
- Now try changing `y` to contain the value 10? What do you need to do, to update the variable `number`?

> ### Notes on variables
> Variables can be given any name such as `x`, `current_temperature`, or
> `subject_id`. You want your object names to be explicit and not too long. They
> cannot start with a number (`2x` is not valid but `x2` is). R is case sensitive
> (e.g., `genome_length` is different from `Genome_length`). There are some names that
> cannot be used because they represent the names of fundamental functions in R
> (e.g., `if`, `else`, `for`, see [here](https://stat.ethz.ch/R-manual/R-devel/library/base/html/Reserved.html)
> for a complete list). In general, even if it's allowed, it's best to not use
> other function names (e.g., `c`, `T`, `mean`, `data`) as variable names. When in doubt
> check the help to see if the name is already in use. It's also best to avoid
> dots (`.`) within a variable name as in `my.dataset`. There are many functions
> in R with dots in their names for historical reasons, but because dots have a
> special meaning in R (for methods) and other programming languages, it's best to
> avoid them. It is also recommended to use nouns for variable names, and verbs
> for function names. It's important to be consistent in the styling of your code
> (where you put spaces, how you name variable, etc.). In R, two popular style
> guides are [Hadley Wickham's style guide](http://adv-r.had.co.nz/Style.html) and [Google's](https://google-styleguide.googlecode.com/svn/trunk/Rguide.xml).


## Data Structures

We know that variables are like buckets, and so far we have seen that bucket filled with a single value. Even when `number` was created, the result of the mathematical operation was a single value. **Variables can store more than just a single value, they can store a multitude of different data structures.** These include, but are not limited to, vectors, matrices (`matrix`), data frames (`data.frame`) and factors (`factor`).


### Vectors

A vector is the most common and basic data structure in R, and is pretty much the workhorse of R. It's basically just a list of values, mainly either numbers or characters (or both). The analogy here is that your bucket now has different compartments; these compartments in a vector are called *elements*. Each element contains a single value, and there is no limit to how many elements you can have. The vector is assigned to a single variable, because regardless of how many elements it contains it is still a single bucket. Let's create a vector of genome lengths and assign it to a variable called `glengths`. 

Each element of the vector contains a single numeric value, and three values will be combined together using `c()` (the combine function). All of the values are put within the parentheses and separated with a comma.


```{r, purl=FALSE}
glengths <- c(4.6, 3000, 50000)
glengths
```

A vector can also contain characters. Create another vector called `species` with three elements, where each element corresponds with the genome sizes vector (in Mb).

```{r, purl=FALSE}
species <- c("ecoli", "human", "corn")
species
```

We just saw 2 of the 6 **data types** that R uses: `"character"` and `"numeric"`. The other 4 are:       

* `"logical"` for `TRUE` and `FALSE` (the boolean data type)
* `"integer"` for integer numbers (e.g., `2L`, the `L` indicates to R that it's an integer)
* `"complex"` to represent complex numbers with real and imaginary parts (e.g.,
  `1+4i`) and that's all we're going to say about them
* `"raw"` that we won't discuss further



### Data frame vs. Matrix

A `matrix` in R is a collection of vectors of **same length and identical datatype**. Vectors can be combined as columns in the matrix or by row. Usually matrices are numeric and used in various computational algorithms to serve as a checkpoint. For example, if input data is not of identical datatype, the `matrix()` function will throw an error and stop any downstream code execution.

`data.frame` is the _de facto_ data structure for most tabular data and what we use for statistics and plotting. A `data.frame` is a collection of vectors of identical lengths. Each vector represents a column, and **each vector can be of a different data type** (e.g., characters, integers, factors). 


### Factors

Factors are used to represent categorical data. Factors can be ordered or unordered and are an important class for statistical analysis and for plotting.

Factors are stored as integers, and have labels associated with these unique integers. While factors look (and often behave) like character vectors, they are actually integers under the hood, and you need to be careful when treating them like strings. To create a factor vector we use the `factor()` function:

```{r}
expression <- factor(c("low", "high", "medium", "high", "low", "medium", "high"))
levels(expression)
```

Sometimes, the order of the factors does not matter, other times you might want
to specify the order because it is meaningful (e.g., "low", "medium", "high") or
it is required by particular type of analysis. Additionally, specifying the
order of the levels allows one to compare levels:

```{r}
expression <- factor(expression, levels=c("low", "medium", "high"))
levels(expression)
min(expression) ## doesn't work

expression <- factor(expression, levels=c("low", "medium", "high"), ordered=TRUE)
levels(expression)
min(expression) ## works!
```

In R's memory, these factors are represented by numbers (1, 2, 3). They are
better than using simple integer labels because factors are self describing:
`"low"`, `"medium"`, and `"high"`" is more descriptive than `1`, `2`, `3`. Which
is low?  You wouldn't be able to tell with just integer data. Factors have this
information built in. It is particularly helpful when there are many levels.



