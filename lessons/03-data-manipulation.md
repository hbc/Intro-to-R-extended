---
title: "Data manipulation"
date: "03/03/2015"
layout: topic
minutes: 30 
---

## Learning Objectives
* Using functions to explore data structures
* Using indexes and sequences to select data from vectors and dataframes
* Subsetting data using logical operators
* Re-organizing data based on match indices


## Using functions to explore data structures

There are also a selection of base functions in R that are useful for inspecting your data and summarizing it. Let's start with a simple data structure such as vectors. A commonly used function is `length()`, which tells you how many elements are in a particular vector:


	length(glengths)
	length(species)


The `class()` function is useful in indicating the datatype or data structure of a variable. So for example if we were interested in knowing what was inside `glengths`:

	class(glengths)


We could also use class on a data frame or any other type of object. Let's load in a data frame to test out some more functions. We will use `read.csv` to read in data from a csv (comma separated values) file. There are numerous other functions to load in data depending on your filetype, but `read.csv` is one of the more commonly used ones.

> Note: When typing out read.csv try pressing the `Tab` key after typing only `read`. You will find that a drop-down menu will appear listing all `read` options for loading in files. The window to the right gives you more information on the function and its arguments as you scroll down and highlight each individually.


	metadata <- read.csv(file='data/mouse_exp_design.csv')


The function has *one required argument* and several *options* that can be changed. The mandatory argument is a path to the file and filename, which in our case is `mouse_exp_design.csv` file. We will put the function to the right of the assignment operator, meaning that any output will be saved as the variable name provided on the left.

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



## Indexing and sequences

When analyzing data, we often want to partition the data so that we are only working with selected columns or rows. A data frame or data matrix is simply a collection of vectors combined together. So let's begin with vectors, then apply those concepts to dataframes.

### Vectors

If we want to extract one or several values from a vector, we must provide one or several indexes in square brackets. The index represents the element number within a vector (or the compartment number, if you think of the bucket analogy). R indexes start at 1. Programming languages like Fortran, MATLAB, and R start counting at 1, because that's what human beings typically do. Languages in the C family (including C++, Java, Perl, and Python) count from 0 because that's simpler for computers to do.

Let's start by creating a vector called age:

	age <- c(15, 18, 22, 45, 52, 56, 67, 73, 81)


Suppose we only wanted the fifth value of this vector, we would use the following syntax:

	age[5]

If we wanted to index more than one element we would still use the square bracket sytntax, but rather than using a single value we would pass in a vector of the index values:

	idx <- c(3,5,7)
	age[idx]


To access a sequence of continuous values from a vector, we would use `:` which is a special function that creates numeric vectors of integer in increasing or decreasing order. Let's select the *first five values* from age:

	age[1:5]

Alternatively, if you wanted the reverse could try `5:1` for instance and see what is returned. The function `seq()` (for __seq__uence) can also be used to create sequences, but allow for more complex patterns. Passing in the `by` argument will allow you to generate a sequence based on the specified interval:

	seq(1, 10, by=2)

Additionally, the `length.out` parameter will provide the restriction on the maximum length of the resulting vector. A combininatioj of parameters can also be used:

```r
seq(5, 10, length.out=3)       # equal breaks of sequence into vector length = length.out
seq(50, by=5, length.out=10)   # sequence 50 by 5 until you hit vector length = length.out
seq(1, 8, by=3)                # sequence by 3 until you hit 8
```

### Dataframes

Dataframes have 2 dimensions (rows and columns), so if we want to extract some specific data from it we need to specify the "coordinates" we want from it. We use the same square bracket syntax but rather than providing a single index, there are two inputs required. Within the square bracket, row numbers come first followed by column numbers (and the two are separated by a comma). For example:

	metadata[1, 1]   # first element in the first column of the data frame
	metadata[1, 3]   # first element in the 3rd column

Now if you only wanted to select based on rows, you would provide the indexes for the rows and leave the columns blank. The key here is to include the comma, to let R know that you are accessing a 2 dimensional data structure:

	metadata[3, ]    # the 3rd row for all columns
	metadata[1:3, ] # first three rows
	metadata[c(1,3,7), ] # first, third and seventh rows

Similarly, if you were selecting specific columns from the data frame - the rows are left blank:

	metadata[ ,3]    # the entire 3rd column


> ### Challenge
> The function `nrow()` on a `data.frame` returns the number of rows. Use it, in conjuction with `seq()` to create a new `data.frame` called `data_by_2` that includes every other row of the metadata.


For larger datasets, it can be tricky to remember the column number that corresponds to a particular variable. (Is celltype in column 2
or 2? oh, right... they are in column 1). In some cases, the column number for a variable can change if the script you are using adds or removes
columns. It's therefore often better to use column names to refer to a particular variable, and it makes your code easier to read and your intentions
clearer.

You can do operations on a particular column, by selecting it using the `$` sign. In this case, the entire column is a vector. For instance, to extract all
the gentotypes from our dataset, we can use: `metadata$genotype`. You can use `names(metadata)` or `colnames(metadata)` to remind yourself of the column names.

To select multiple columns by name the square bracket syntax is used by concatenating a vector of strings that correspond to column names: 

```r
metadata[, c("genotype", "celltype")]
```

```
##          genotype celltype
## sample1        Wt    typeA
## sample2        Wt    typeA
## sample3        Wt    typeA
## sample4        KO    typeA
## sample5        KO    typeA
## sample6        KO    typeA
## sample7        Wt    typeB
## sample8        Wt    typeB
## sample9        Wt    typeB
## sample10       KO    typeB
## sample11       KO    typeB
## sample12       KO    typeB
```



## Subsetting data

Another way of partitioning your data, is by filtering based on the content that is in your dataframe using the `subset()` function. For example, we can look at the samples of a specific celltype "typeA":


	subset(metadata, celltype == "typeA")


```
##         genotype celltype replicate
## sample1       Wt    typeA         1
## sample2       Wt    typeA         2
## sample3       Wt    typeA         3
## sample4       KO    typeA         1
## sample5       KO    typeA         2
## sample6       KO    typeA         3
```

We can also subset using other [logical operators](http://www.statmethods.net/management/operators.html) in R. For example suppose we wanted to subset to keep only the **WT samples** from the **typeA** celltype.


	subset(metadata, celltype == "typeA" & genotype == "Wt")


```
##         genotype celltype replicate
## sample1       Wt    typeA         1
## sample2       Wt    typeA         2
## sample3       Wt    typeA         3
```

Alternatively, we could try looking at only the first two replicates of each sample set. Here, we can use the less than operator since replicate is currently a numeric vector. Adding in the argument select allows us to specify which columns to keep. Which columns are left?

	subset(metadata, replicate < 3, select = c('genotype', 'celltype'))




## Matching data 

Often when working with genomic data, we have a data file that corresponds with our metadata file. The data file contains measurements from the biological assay for each individual sample. In our case, the biological assay is gene expression and data was generated using RNA-Seq. Let's bring in the data matrix of RPKM values:


	rpkm_data <- read.csv("data/counts.rpkm")


Take a look at the first few lines of the data matrix to see what's in there.


	head(rpkm_data)


It looks as if the sample names (header) in our data matrix are similar to the row names of our metadata file, but it's hard to tell since they are not in the same order. We can do a quick check of the dimensions and atleast see if the numbers match up. 


	ncol(rpkm_data)


What we want to know is, **do we have data for every sample that we have metadata?** 


There are many ways to answer this using R. We'll be using the `match` function, which takes at least 2 arguments: 1) a vector of values to be matched, and 2) a vector of values to be matched against. The function returns the position of the matches in the second vector. Take a look at the example below where vector B is the reverse of vector A:


	A <- c(1,3,5,7,9,11)   
	B <- c(11,9,7,5,3,1) 

Now if we use the match function with A as our first input and B as our second, you will be returned a vector of size `length(A)`. Each number that is returned represents the index of vector B where the matching values was observed. 

	match(A.B)

Let's change vector B so that only a subset are retained:

	A <- c(1,3,5,7,9,11)   
	B <- c(9,5,1,1) 

Note, for values that don't match you can specify what values you would have it assigned using `nomatch` argument (by default this is set to `NA`). Also, illustrated in the example, if there is more than one matching value found only the first is reported.


### Subset data using matching

We are trying to *match the row names of our metadata with the column names of our expression data*, so these will be the arguments for `match`. There are base functions in R which allow you to extract the row and column names as a vector:

	row.names(metadata)
	col.names(rpkm_data)
 
Using these two arguments we will retrieve a vector of match indices. This vector represents the re-ordering of the column names in our data matrix to be identical to the rows in metadata:
 
	m <- match(row.names(metadata), colnames(rpkm_data))


Now we can create a new data matrixin which columns are re-ordered based on the match indices:

	data_ordered  <- rpkm_data[,m]


Check and see what happened by using `head`. You can also verfy that column names of this new data matrix matches the metadata row names by using the `all` function:

	head(data_ordered)
	all(row.names(metadata) == colnames(data_ordered))


> ### An R package for data manipulation
> The package `dplyr` is a fairly new (2014) package that tries to provide easy
> tools for the most common data manipulation tasks. It is built to work directly
> with data frames. The thinking behind it was largely inspired by the package
> `plyr` which has been in use for some time but suffered from being slow in some
> cases.` dplyr` addresses this by porting much of the computation to C++. An
> additional feature is the ability to work with data stored directly in an
> external database. The benefits of doing this are that the data can be managed
> natively in a relational database, queries can be conducted on that database,
>and only the results of the query returned.


---
*The materials used in this lesson is adapted from work that is Copyright © Data Carpentry (http://datacarpentry.org/). 
All Data Carpentry instructional material is made available under the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0).*
