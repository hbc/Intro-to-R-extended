---
title: "Data manipulation"
date: "03/03/2015"
layout: topic
minutes: 60
---

## Learning Objectives
* Inspecting data structures
* Using indexes and sequences to select data from vectors and dataframes
* Subsetting data using logical operators
* Matching and re-organizing data
* Writing data to file


## Inspecting data structures

There are a wide selection of base functions in R that are useful for inspecting your data and summarizing it. Let's start with a simple data structure such as vectors. A commonly used function is `length()`, which tells you how many elements are in a particular vector:


	length(glengths)
	length(species)


The `class()` function is useful in indicating the datatype or data structure of a variable. For example, if we were interested in knowing what type of data was contained in `glengths`:

	class(glengths)


We could also use class on a data frame or any other type of object. Let's use the `metadata` file that we created to test out some more functions. 


Take a look at the dataframe by typing out the variable name `metadata` and pressing return; the variable contains information describing the samples in our study. Each row holds information for a single sample, and the columns represent `genotype`(WT or KO),  `celltype` (typeA or typeB), and `replicate number`.


	metadata

```
   genotype celltype replicate
1        Wt    typeA         1
2        Wt    typeA         2
3        Wt    typeA         3
4        KO    typeA         1
5        KO    typeA         2
6        KO    typeA         3
7        Wt    typeB         1
8        Wt    typeB         2
9        Wt    typeB         3
10       KO    typeB         1
11       KO    typeB         2
12       KO    typeB         3
```

> **NOTE**: 
> 
> If you did not create this dataframe you can use the `read.csv` function to read in a file containing the same information. The file is a csv (comma separated values) file. 
>
	metadata <- read.csv(file='data/mouse_exp_design.csv')
> 
> *This function is described in more detail a bit later in the lesson, when we load in our data file.*
> 


Suppose we had a larger file, we might not want to display all the contents in the console. Instead we could check the top (the first 6 lines) of this `data.frame` using the function `head()`:


	head(metadata)


Previously, we had mentioned that character values get converted to factors by default using `data.frame`. Another way to assess this change would be to use the __`str`__ucture function. You will get specific details on each column:


	str(metadata)


```
'data.frame':	12 obs. of  3 variables:
 $ genotype : Factor w/ 2 levels "KO","Wt": 2 2 2 1 1 1 2 2 2 1 ...
 $ celltype : Factor w/ 2 levels "typeA","typeB": 1 1 1 1 1 1 2 2 2 2 ...
 $ replicate: num  1 2 3 1 2 3 1 2 3 1 ...
```

As you can see, the columns `genotype` and `celltype` are of the `factor` class, whereas the replicate column has been interpreted as integer data type.

__You can also get this information from the "Environment" tab in RStudio.__

### Functions for data inspection

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

***
**Exercise** 

1. How many rows and how many columns are there in `metadata`?
2. We need to add row names to `metadata` so that it corresponds with our data file when we bring it in. The vector of sample names are provided below. Using the functions listed above, assign `metadata` a new set of row names.

```
	rnames <- c("sample1", "sample2", "sample3", "sample4", "sample5", "sample6", "sample7", "sample8", "sample9", "sample10", 
				"sample11", "sample12") 
```

***

## Selecting data using indexes and sequences

When analyzing data, we often want to **partition the data so that we are only working with selected columns or rows.** A data frame or data matrix is simply a collection of vectors combined together. So let's begin with vectors and how to access differnt elements, and then extend those concepts to dataframes.

### Vectors

If we want to extract one or several values from a vector, we must provide one or several indexes using square brackets `[ ]` syntax. The **index represents the element number within a vector** (or the compartment number, if you think of the bucket analogy). R indexes start at 1. Programming languages like Fortran, MATLAB, and R start counting at 1, because that's what human beings typically do. Languages in the C family (including C++, Java, Perl, and Python) count from 0 because that's simpler for computers to do.

Let's start by creating a vector called age:

	age <- c(15, 18, 22, 45, 52, 56, 67, 73, 81)


Suppose we only wanted the fifth value of this vector, we would use the following syntax:

	age[5]

If we wanted to select more than one element we would still use the square bracket syntax, but rather than using a single value we would pass in a *vector of several index values*:

	idx <- c(3,5,7) # create vector of the elements of interest
	age[idx]


To select a sequence of continuous values from a vector, we would use `:` which is a special function that creates numeric vectors of integer in increasing or decreasing order. Let's select the *first five values* from age:

	age[1:5]

Alternatively, if you wanted the reverse could try `5:1` for instance, and see what is returned. 


The function `seq()` (for __seq__uence) can also be used to create sequences,

	seq(1,5)

If we place the `seq` function inside the square brackets this will do exactly the same thing as `age[1:5]`. Test it out for yourself:

	age[seq(1,5)]

The `seq` function can also allow for more complex patterns. Passing in the `by` argument will allow you to generate a sequence based on the specified interval:

	seq(1, 10, by=2)

Additionally, the `length.out` parameter will provide the restriction on the maximum length of the resulting vector:

	seq(5, 10, length.out=3)  # equal breaks of sequence into vector length = length.out 


A combinination of parameters can also be used:

```r

seq(50, by=5, length.out=10)   # sequence 50 by 5 until you hit vector length = length.out
seq(1, 8, by=3)                # sequence by 3 until you hit 8
```

### Factors

We briefly introduced factors in the last lesson, but it only becomes more intuitive once you've had a chance to work with them. Remember that when we created our factor, we took the set of unique elements in the expression vector were obtained, ordered alphabetically, and integer value-label pairs were created (i.e high=1, low=2, medium=3). To view the integer assignments under the hood you can use `str`:

	str(expression)
	
	Factor w/ 3 levels "high","low","medium": 2 1 3 1 2 3 1

The unique elements are referred to as "factor levels", and we can use the function `levels()` to idenitfy the different categories/levels for a factor:  

	levels(expression)

With the establishment of defined levels, we can then use the `summary()` function to classify and count the elements for each level: 

	summary(expression)

Factors can be ordered or unordered. Sometimes, the order of the factors does not matter, other times you might want to specify the order because it is meaningful (e.g., "low" < "medium" < "high") or it is required by particular type of analysis. 

In the example above, the factor is unordered. You can check this by trying the following:

	min(expression) # doesn't work!

To order factor levels, you can simply add an argument to the function `ordered=TRUE`:

	factor(expression, ordered=TRUE)
	
	[1] low    high   medium high   low    medium high  
	Levels: high < low < medium

But what you'll find is that by default R will order levels by alpahabetical order. In order to get the desired ordering (i.e. "low" < "medium" < "high") we need to specify the order of levels and add the argument `ordered=TRUE`.

```{r}
expression <- factor(expression, levels=c("low", "medium", "high"), ordered=TRUE)
levels(expression)
min(expression) ## works!
```

***
**Exercise**

1. Use the `samplegroup` vector we created in a previous lesson, and change that to an ordered factor such that KO < CTL < OE. 

***

### Dataframes

Dataframes (and matrices) have 2 dimensions (rows and columns), so if we want to select some specific data from it we need to specify the "coordinates" we want from it. We use the same square bracket notation but rather than providing a single index, there are *two indexes required*. Within the square bracket, **row numbers come first followed by column numbers (and the two are separated by a comma)**. For example:

	metadata[1, 1]   # first element in the first column of the data frame
	metadata[1, 3]   # first element in the 3rd column

Now if you only wanted to select based on rows, you would provide the indexes for the rows and leave the columns index blank. The key here is to include the comma, to let R know that you are accessing a 2-dimensional data structure:

	metadata[3, ]    # the 3rd row for all columns
	metadata[1:3, ] # first three rows
	metadata[c(1,3,7), ] # first, third and seventh rows

Similarly, if you were selecting specific columns from the data frame - the rows are left blank:

	metadata[ ,3]    # the entire 3rd column



For larger datasets, it can be tricky to remember the column number that corresponds to a particular variable. (Is celltype in column 1
or 2? oh, right... they are in column 1). In some cases, the column number for a variable can change if the script you are using adds or removes
columns. It's therefore often better to use column names to refer to a particular variable, and it makes your code easier to read and your intentions clearer.

You can do operations on a particular column, by selecting it using the `$` sign. In this case, the entire column is a vector. For instance, to extract all the gentotypes from our dataset, we can use: 

	metadata$genotype 

You can use `names(metadata)` or `colnames(metadata)` to remind yourself of the column names. We can then supply index values to select specific values from that vector. For example, if we wanted the genotype information for the first five samples in `metadata`:

	metadata$genotype[1:5]

The `$` allows you to select a single column by name. To select multiple columns by name, you need to  concatenate a vector of strings that correspond to column names: 

```
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


***

**Exercise** 

1. The function `nrow()` on a `data.frame` returns the number of rows. Use it, in conjuction with `seq()` to create a new `data.frame` called `data_by_2` that includes every other row of the metadata.


***



## Subsetting data by values

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

We can also use the `subset` function with other [logical operators](http://www.statmethods.net/management/operators.html) in R. For example, suppose we wanted to subset to keep only the **WT samples** from the **typeA** celltype.


	subset(metadata, celltype == "typeA" & genotype == "Wt")


```
##         genotype celltype replicate
## sample1       Wt    typeA         1
## sample2       Wt    typeA         2
## sample3       Wt    typeA         3
```

Alternatively, we could try looking at only the first two replicates of each sample set. Here, we can use the less than operator since replicate is currently a numeric vector. Adding in the argument select allows us to specify which columns to keep. Which columns are left?

	subset(metadata, replicate < 3, select = c('genotype', 'celltype'))


***

**Exercise** 

1. Return only those samples from `all_data` dataframe with age == 4.
2. Using the `all_data` dataframe, return only the samples where `genotype` is Wt and `age` is greater than 4.

***

## Matching data 

Often when working with genomic data, we have a data file that corresponds with our metadata file. The data file contains measurements from the biological assay for each individual sample. In our case, the biological assay is gene expression and data was generated using RNA-Seq. Let's bring in the data matrix of RPKM values.

We will read in the RPKM file using the the `read.csv` function which has *one required argument* and several *specifications* that can be provided. The mandatory argument is a path to the file and filename, which in our case is `data/counts.rpkm` file. We will put the function to the right of the assignment operator, meaning that **any output will be saved as the variable name provided on the left**.


	rpkm_data <- read.csv("data/counts.rpkm")


> *Note: By default, `read.csv` converts (= coerces) columns that contain characters (i.e., text) into the `factor` data type. Depending on what you want to do with the data, you may want to keep these columns as `character`. To do so, `read.csv()` and `read.table()` have an argument called `stringsAsFactors` which can be set to `FALSE`.*
> 
> There are several functions in R for reading in data, depending on the type of data you are working with. The table below lists the functions needed to import data from common file formats.
> 
> | Data Type  | Function | Package
> | -----------:|:----------------:|:---------------:|
> | comma separated (.csv)  | read.csv()	| utils (default) |
> | other delimited formats | read.table(); read.csv() | utils (default) |
> | Stata version 7-12 (.dta) | read.dta() | foreign |
> | Stata version 13-14 (.dta) | readdta() | haven |
> | SPSS (.sav) |	read.spss() | foreign |
> | SAS (.sas7bdat) | read.sas7bdat() | sas7bdat |
> | Excel (.xls, .xlsx) | readWorksheetFromFile() | XLConnect |



Take a look at the first few lines of the data matrix to see what's in there.


	head(rpkm_data)


It looks as if the sample names (header) in our data matrix are similar to the row names of our metadata file, but it's hard to tell since they are not in the same order. We can do a quick check of the dimensions and at least see if the numbers match up. 


	ncol(rpkm_data)


What we want to know is, **do we have data for every sample that we have metadata?** 


### The `match` function

We'll be using the `match` function to evaluate which samples are present in both data structure, and then re-order them. This function takes at least 2 arguments: 1) a vector of values to *be matched*, and 2) a vector of values to be *matched against*. The function returns the position of the matches in the second vector. Let's create vectors A and B to demonstrate how it works:

	A <- c(1,2,3,4,5)
	B <- c(5,4,3,2,1)  # same numbers but backwards 

	match(A,B)
	
	[1] 5 4 3 2 1

The function should return a vector of size `length(A)`. Each number that is returned represents the index of vector B where the matching value was observed. 

Let's change vector B so that only a subset are retained:

	A <- c(1,2,3,4,5)
	B <- c(4,2,1)  # remove values 

And try to `match` again:

	match(A, B)

	[1]  3  2 NA  1 NA

Note, for values that don't match by default return an `NA` value. You can specify what values you would have it assigned using `nomatch` argument. Also, if there is more than one matching value found only the first is reported.


We are trying to *match the row names of our metadata with the column names of our expression data*, so these will be the arguments for `match`. Using these two arguments we will retrieve a vector of match indices. The resulting vector represents the re-ordering of the column names in our data matrix to be identical to the rows in metadata:
 
	m <- match(row.names(metadata), colnames(rpkm_data))


Now we can create a new data matrix in which columns are re-ordered based on the match indices:

	rpkm_ordered  <- rpkm_data[,m]


Check and see what happened by using `head`. You can also verify that column names of this new data matrix matches the metadata row names by using the `all` function:

	head(rpkm_ordered)
	all(row.names(metadata) == colnames(rpkm_ordered))


### Writing to file 

Everything we have done so far has only modified the data in R; the files have remained unchanged. Now that we have re-ordered our data matrix to match our metadata, it's a good idea to save that to file in case we need it for future analyses.


To write our matrix to file in comma separated format (.csv), we can use the `write.csv` function. There are two required arguments: the data structure you are exporting, and the path and filename that you are exporting to. By default the delimiter is set, and columns will be separated by a comma:

	write.csv(rpkm_ordered, file="data/counts.rpkm_ordered.csv")

Similar to reading in data, there are a wide variety of functions available allowing you to export data in specific formats. Another commonly used function is `write.table`, which allows you to specify the delimiter you wish to use. This function is commonly used to create tab-delimited files.


***
**Exercise** 


We are particularly interested in the expression of some specific marker genes. We would like to extract the expression data for these genes and save this information to file.

1. We learned previously how to read in data from a data.frame. We will use a different function, `scan()` to bring in a vector of values. Look up how to use the `scan()` function uing R help `?`. The list of genes you need to read in is located in the `data` directory. The files is called `important_genes.txt`.  `scan` will read in the list of genes as a character vector. Assign this vector to teh varaible `important_genes`.
2. Now, let's find the positions of our important genes (`important_genes`) in the RPKM dataframe (`rpkm_data`) by using the `match()` function. Save the positions of the genes as a vector of positions called `m`.
3. Extract the rows for our important genes from the `rpkm_data` using the `m` indexes, and save as `rpkm_important_genes`.
4. Write the expression data for our important genes to file using `write.table()`, and save as a tab-delimited file called `Important_gene_counts.txt`. 

> **NOTE**:
>  
> Writing a vector of values to file requires a different function than the functions available for writing dataframes. You can use `write()` to save a vector of values to file. For example:
>

		write(important_genes, "important_genes.txt", ncolumns=1)
>
> Sometimes when writing a dataframe with row names to file, the column names will align starting with the row names column. To avoid this, you can include the argument `col.names = NA` when writing to file to ensure all of the column names line up with the correct column values.
> 
> 


***




### An R package for data manipulation
The methods presented above are using base R functions for data manipulation. For more advanced R users, 
the package `dplyr` is a fairly new (2014) package that tries to provide easy
tools for the most common data manipulation tasks. It is built to work directly
with data frames. The thinking behind it was largely inspired by the package
`plyr` which has been in use for some time but suffered from being slow in some
cases.` dplyr` addresses this by porting much of the computation to C++. An
additional feature is the ability to work with data stored directly in an
external database. The benefits of doing this are that the data can be managed
natively in a relational database, queries can be conducted on that database,
and only the results of the query returned.


---
*The materials used in this lesson is adapted from work that is Copyright © Data Carpentry (http://datacarpentry.org/). 
All Data Carpentry instructional material is made available under the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0).*
