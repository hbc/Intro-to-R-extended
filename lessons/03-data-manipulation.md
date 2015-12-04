---
title: "Data manipulation"
date: "03/03/2015"
layout: topic
minutes: 30 
---

## Learning Objectives
* Inspecting data structures
* Using indexes and sequences to select data from vectors and dataframes
* Subsetting data using logical operators
* Matching and re-organizing data


## Inspecting data structures

There are a wide selection of base functions in R that are useful for inspecting your data and summarizing it. Let's start with a simple data structure such as vectors. A commonly used function is `length()`, which tells you how many elements are in a particular vector:


	length(glengths)
	length(species)


The `class()` function is useful in indicating the datatype or data structure of a variable. For example, if we were interested in knowing what type of data was contained in `glengths`:

	class(glengths)


We could also use class on a data frame or any other type of object. Let's use the metadata file that we created to test out some more functions. 


Take a look at the dataframe by typing out the variable name `metadata` and pressing return. The file contains information describing the samples in our study. Each row holds information for a single sample, and the columns represent `genotype`(WT or KO),  `celltype` (typeA or typeB), and `replicate number`.


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

> If you did not create this dataframe you can use the `read.csv` function to read in a file containing the same information. The file is a csv (comma separated values) file. 
>
	metadata <- read.csv(file='data/mouse_exp_design.csv')
>
>
> The function has *one required argument* and several *specifications* that can be provided. The mandatory argument is a path to the file and filename, which in our case is `mouse_exp_design.csv` file. We will put the function to the right of the assignment operator, meaning that **any output will be saved as the variable name provided on the left**.
>
> *Note: By default, `read.csv` converts (= coerces) columns that contain characters (i.e., text) into the `factor` data type. Depending on what you want to do with the data, you may want to keep these columns as `character`. To do so, `read.csv()` and `read.table()` have an argument called `stringsAsFactors` which can be set to `FALSE`.*


Suppose we had a larger file, we might not want to display all the contents in the console. Instead we could check the top (the first 6 lines) of this `data.frame` using the function `head()`:


	head(metadata)


Let's now check the __str__ucture of this `data.frame` in more details with the function `str()`:

	str(metadata)


```
## 'data.frame':	12 obs. of  3 variables:
##  $ genotype : Factor w/ 2 levels "KO","Wt": 2 2 2 1 1 1 2 2 2 1 ...
##  $ celltype : Factor w/ 2 levels "typeA","typeB": 1 1 1 1 1 1 2 2 2 2 ...
##  $ replicate: int  1 2 3 1 2 3 1 2 3 1 ...
```

As you can see, the columns `genotype` and `celltype` are of the `factor` class, whereas the replicate column has been interpreted as integer data type.

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

***
**Exercise** 

1. What is the class of the variable `metadata`?
2. How many rows and how many columns are there?
3. Load in data again, storing it as `test_data` and using `stringsAsFactors=F`. How does this change the structure of the data?

***

## Indexing and sequences

When analyzing data, we often want to partition the data so that we are only working with selected columns or rows. A data frame or data matrix is simply a collection of vectors combined together. So let's begin with vectors, and then extend those concepts to dataframes.

### Vectors

If we want to extract one or several values from a vector, we must provide one or several indexes in square brackets. The index represents the element number within a vector (or the compartment number, if you think of the bucket analogy). R indexes start at 1. Programming languages like Fortran, MATLAB, and R start counting at 1, because that's what human beings typically do. Languages in the C family (including C++, Java, Perl, and Python) count from 0 because that's simpler for computers to do.

Let's start by creating a vector called age:

	age <- c(15, 18, 22, 45, 52, 56, 67, 73, 81)


To index from a data structure, we use the the square bracket notation `[]`. Suppose we only wanted the fifth value of this vector, we would use the following syntax:

	age[5]

If we wanted to index more than one element we would still use the square bracket notation, but rather than using a single value we would pass in a *vector of the index values*:

	idx <- c(3,5,7) # create vector of the elements of interest
	age[idx]


To access a sequence of continuous values from a vector, we would use `:` which is a special function that creates numeric vectors of integer in increasing or decreasing order. Let's select the *first five values* from age:

	age[1:5]

Alternatively, if you wanted the reverse could try `5:1` for instance, and see what is returned. The function `seq()` (for __seq__uence) can also be used to create sequences, but allow for more complex patterns. Passing in the `by` argument will allow you to generate a sequence based on the specified interval:

	seq(1, 10, by=2)

Additionally, the `length.out` parameter will provide the restriction on the maximum length of the resulting vector. A combinination of parameters can also be used:

```r
seq(5, 10, length.out=3)       # equal breaks of sequence into vector length = length.out
seq(50, by=5, length.out=10)   # sequence 50 by 5 until you hit vector length = length.out
seq(1, 8, by=3)                # sequence by 3 until you hit 8
```

### Factors

We briefly introduced factors in the last lesson, but it becomes more intuitive once you've had a chance to work with them. Remember that when we created our factor, we took the set of unique elements in the expression vector were obtained, ordered alphabetically, and integer value-label pairs were created (i.e high=1, low=2, medium=3). The unique elements are referred to as "factor levels", and we can use the function `levels()` to idenitfy the different categories for a factor:  

	levels(expression)

With the establishment of defined levels, we can then use the `summary()` function to classify and count the elements for each level. 

	summary(expression)

Factors can be ordered or unordered and are an important class for statistical analysis and for plotting. Sometimes, the order of the factors does not matter, other times you might want to specify the order because it is meaningful (e.g., "low" < "medium" < "high") or it is required by particular type of analysis. 

In the example above, the factor is unordered. To order factor levels we need to specify the desired order of levels and add an argument to the function `ordered=TRUE`. In this way, R can access the elements as their actual integer values. Try the example below: 

```{r}
min(expression) # doesn't work!

expression <- factor(expression, levels=c("low", "medium", "high"), ordered=TRUE)
levels(expression)
min(expression) ## works!
```

### Dataframes

Dataframes (and matrices) have 2 dimensions (rows and columns), so if we want to extract some specific data from it we need to specify the "coordinates" we want from it. We use the same square bracket notation but rather than providing a single index, there are *two inputs required*. Within the square bracket, **row numbers come first followed by column numbers (and the two are separated by a comma)**. For example:

	metadata[1, 1]   # first element in the first column of the data frame
	metadata[1, 3]   # first element in the 3rd column

Now if you only wanted to select based on rows, you would provide the indexes for the rows and leave the columns index blank. The key here is to include the comma, to let R know that you are accessing a 2 dimensional data structure:

	metadata[3, ]    # the 3rd row for all columns
	metadata[1:3, ] # first three rows
	metadata[c(1,3,7), ] # first, third and seventh rows

Similarly, if you were selecting specific columns from the data frame - the rows are left blank:

	metadata[ ,3]    # the entire 3rd column



For larger datasets, it can be tricky to remember the column number that corresponds to a particular variable. (Is celltype in column 1
or 2? oh, right... they are in column 1). In some cases, the column number for a variable can change if the script you are using adds or removes
columns. It's therefore often better to use column names to refer to a particular variable, and it makes your code easier to read and your intentions clearer.

You can do operations on a particular column, by selecting it using the `$` sign. In this case, the entire column is a vector. For instance, to extract all the gentotypes from our dataset, we can use: `metadata$genotype`. You can use `names(metadata)` or `colnames(metadata)` to remind yourself of the column names. That vector can then be indexed for
specific values. For example, if we wanted the genotype information for the first five samples in `metadata`:

	metadata$genotype[1:5]

To select multiple columns by name the square bracket syntax is used by concatenating a vector of strings that correspond to column names: 

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
2. Extract only those samples in the dataframe `all_data` where `samplename` is "C1", "C2", "C3", "KO1", "KO2", or "KO3".

***


### Lists

Indexing a list requires a slightly different notation, even though in theory a list is a vector (that contains multiple data structures). To index a specific component of a list, you need to use double bracket notation `[[]]`. Let's use the `list1` that we created previously, and index the second component:

	list1[[2]]

What do you see printed to the console? Using the double bracket notation is useful for accessing the individual components whilst preserving the original data structure. When creating this list we know we had originally stored a dataframe in the second component. With the `class` function we can check if that is what we retrieve from indexing:

	comp2 <- list1[[2]]
	class(comp2)

You can also reference what is inside the component by adding and additional bracket. For example, in the first component we have a vector stored. 

	list1[[1]]
	
	[1] "ecoli" "human" "corn" 

Now, if we wanted to reference the first element of that vector we would use:

	list1[[1]][1]

	[1] "ecoli"

You can also do the same for dataframes and matrices, although with larger datasets it is not advisable. Instead, it is better to save the contents of a list component to a variable (as we did above) and further manipulate it. Also, it is important to note that when indexing vectors we can only **access one component at a time**. To access multiple components of a list, see the note below. 

> Note: Using the single bracket notation also works wth lists. The difference is the class of the information that is retrieved. Using single bracket notation i.e. `list1[1]` will return the contents in a list form and *not the original data structure*. The benefit of this notation is that it allows indexing by vectors so you can access multiple components of the list at once.


***

**Exercise**  

Let's practice inspecting lists. Create a list named `random` with the following components: `all_data`, `metadata`, `age`, `list1`, `samplename`, and `number`.

1. Print out the values stored in the `samplename` vector component.
	
2. From the `all_data` component of the list, extract the number of counts for geneX for `samplename= 'C2'`.
	
***

Assigning names to the components in a list can help identify what each list component contains, as well as, facilitating the extraction of values from list components. 

Adding names to components of a list uses the same function as adding names to the columns of a dataframe, `names()`.
	
Let's check and see if the `random` list we just created currently has names for the components:

	names(random)Let's assign names to the components of the random vector:

	names(random) <- c("all_data", "metadata", "age", "list1", "samplename")
	
	names(random)
	
Now that we have named our list components, we can extract components using the $ similar to extracting columns from a dataframe. To attain a component of a list using the component name, use list_name$component_name:

To extract the `all_data` dataframe from the `random` list:

	random$all_data

Now we have three ways that we could extract a component from a list. Let's extract the `age` vector from the `random` list:

	random[[3]]
	random[["age"]]
	random$age

***

**Exercise**

Let's practice combining ways to extract data from the data structures we have covered so far:

1. Extract the sixth element of the `age` vector from the `random` list.
2. Extract the control samples ("C1", "C2", "C3")from the `metadata` dataframe from the `random` list.
3. Extract all elements from the `sex` vector from `list1` from the `random` list.
4. Extract every other element of the `sex` vector from `list1` from the `random` list.

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

1. Return only those samples from `all_data` dataframe with genotype `HH`.
2. Using the `metadata` dataframe, return only the counts for geneX from those samples where `sex` is female and `age` is greater than 4.

***

## Matching data 

Often when working with genomic data, we have a data file that corresponds with our metadata file. The data file contains measurements from the biological assay for each individual sample. In our case, the biological assay is gene expression and data was generated using RNA-Seq. Let's bring in the data matrix of RPKM values:


	rpkm_data <- read.csv("data/counts.rpkm")


Take a look at the first few lines of the data matrix to see what's in there.


	head(rpkm_data)


It looks as if the sample names (header) in our data matrix are similar to the row names of our metadata file, but it's hard to tell since they are not in the same order. We can do a quick check of the dimensions and at least see if the numbers match up. 


	ncol(rpkm_data)


What we want to know is, **do we have data for every sample that we have metadata?** 

### The `%in%` operator
 
Although lacking in [documentation](http://dr-k-lo.blogspot.com/2013/11/in-in-r-underused-in-operator.html) this operator is well-used and convenient once you get the hang of it. The operator is used with the following syntax: 

	vector1_of_values %in% vector2_of_values


It will take a vector as input to the left and will **evaluate each element to see if there is a match in the vector that follows on the right of the operator.** *The two vectors do not have to be the same size.* This operation will return a vector of the same length as vector1 containing logical values to indicate whether or not there was a match. Take a look at the example below:


	A <- c(1,3,5,7,9,11)   # odd numbers
	B <- c(2,4,6,8,10,12)  # even numbers

	# test to see if any of A are in B	
	A %in% B


```
## [1] FALSE FALSE FALSE FALSE FALSE FALSE
```

Since vector A contains only odd numbers and vector B contains only even numbers, there is no overlap and so the vector returned contains a `FALSE` for each element. Let's change a couple of numbers inside vector B to match vector A.


```r
B <- c(2,4,6,8,1,5)  # add some odd numbers in 

# test to see if any of A are in B
A %in% B
```

```
## [1]  TRUE FALSE  TRUE FALSE FALSE FALSE
```

The logical vector returned tells us which elements are matching and which are not.  In this example the vectors are small and so it's easy to count by eye; but when we work with large datasets this is not practical. A quick way to assess whether or not we had any matches would be to use the `any` function to see if any of the values contained in vector A are also in vector B:

	any(A %in% B)


The `all` function is also useful. Given a logical vector, it will tell you whether are all values returned are `TRUE`. If there is atleast one `FALSE` value, the `all` function will return a `FALSE` and you know that all of A are not contained in B.


	all(A %in% B)

Suppose we had **two vectors that had the same values but just not in the same order**. We could also use `all` to test for that. Rather than using the `%in%` operator we would use `==` and compare each element to the same position in the other vector. Unlike the `%in%` operator, **for this to work you must have two vectors that are of equal length**.

```r
A <- c(1,2,3,4,5)
B <- c(5,4,3,2,1)  # same numbers but backwards 

# test to see if any of A are in B
A %in% B

# test to see if any of A is equal to B
A == B

# use all to check if they are a perfect match
all(A == B)

```

Let's try this on our data and see whether we have metadata information for all samples in our expression data. We'll start by creating two vectors; one with the `row.names` of the metadata and `colnames` of the RPKM data. These are base functions in R which allow you to extract the row and column names as a vector:

	x <- row.names(metadata)
	y <- colnames(rpkm_data)

Now check to see that all of `x` are in `y`:

	all(x %in% y)


We know that all samples are present, but are they in the same order:

	all(x == y)

**Looks like all of the samples are there, but will need to reordered.**

***
**Exercise** 

We have a list of IDs for genes of particular interest to us. Instead of scrolling through our list of counts, we want to see the number of counts associated with each of these genes. Let's use the `%in%` operator to extract the information for those genes from `rpkm_data`.

1. Create a vector for your important gene IDs, and use the %in% operator to determine whether these genes are in the row names of our `rpkm_data` dataset.

	- important_genes <- c("ENSMUSG00000083700", "ENSMUSG00000080990", 	"ENSMUSG00000065619", "ENSMUSG00000047945", "ENSMUSG00000081010", 	"ENSMUSG00000030970")
	
2. Extract the rows for the important genes from your `rpkm_data` dataset.	
***

### The `match` function

We'll be using the `match` function to re-roder samples. This function takes at least 2 arguments: 1) a vector of values to *be matched*, and 2) a vector of values to be *matched against*. The function returns the position of the matches in the second vector. We can use the vectors A and B created previously to demonstrate how it works:

	match(A,B)

The function should return a vector of size `length(A)`. Each number that is returned represents the index of vector B where the matching value was observed. 

Let's change vector B so that only a subset are retained:

	A <- c(1,2,3,4,5)
	B <- c(4,2,1)  # remove values 

And try to `match` again:

	match(A, B)

	[1]  3  2 NA  1 NA

Note, for values that don't match by default return an `NA` value. You can specify what values you would have it assigned using `nomatch` argument. Also, if there is more than one matching value found only the first is reported.


We are trying to *match the row names of our metadata with the column names of our expression data*, so these will be the arguments for `match` (we had previously assigned these to `x` and `y`, respectively). Using these two arguments we will retrieve a vector of match indices. This vector represents the re-ordering of the column names in our data matrix to be identical to the rows in metadata:
 
	m <- match(row.names(metadata), colnames(rpkm_data))


Now we can create a new data matrixin which columns are re-ordered based on the match indices:

	rpkm_ordered  <- rpkm_data[,m]


Check and see what happened by using `head`. You can also verify that column names of this new data matrix matches the metadata row names by using the `all` function:

	head(rpkm_ordered)
	all(row.names(metadata) == colnames(rpkm_ordered))


***
**Exercise** 

NEEDS TO BE ADDED

***

> ### An R package for data manipulation
> The methods presented above are using base R functions for data manipulation. For more advanced R users, 
> the package `dplyr` is a fairly new (2014) package that tries to provide easy
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
