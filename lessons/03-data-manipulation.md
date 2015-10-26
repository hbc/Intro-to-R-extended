---
title: "Data manipulation"
date: "03/03/2015"
layout: topic
minutes: 30 
---

## Learning Objectives
* Using indexes and sequences to select data within vectors and dataframes
* Manipulating data by subsetting with use of logical operators
* Examining contents of vectors using matching
* Re-organizing data based on match indices




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



