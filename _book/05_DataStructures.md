




# Data Structures {#DataStructures}

Introduction {-#intro-DataStructures}
------------

You can get pretty far in R just using vectors. That’s what
Chapter \@ref(SomeBasics) is all about. This chapter moves beyond vectors to
recipes for matrices, lists, factors, data frames, and tibbles (which are a special kind of data frames). If you have
preconceptions about data structures, we suggest you put them aside. R
does data structures differently than many other languages.

If you want to study the technical aspects of R’s data structures, we
suggest reading [*R in a
Nutshell*](http://oreilly.com/catalog/9780596801717) (O’Reilly) and the R
Language Definition. The notes here are more informal. These are things we
wish we’d known when we started using R.

### Vectors {-}

Here are some key properties of vectors:

Vectors are homogeneous

:   All elements of a vector must have the same type or, in R
    terminology, the same *mode*.

Vectors can be indexed by position

:   So `v[2]` refers to the second element of `v`.

Vectors can be indexed by multiple positions, returning a subvector

:   So `v[c(2,3)]` is a subvector of `v` that consists of the second and
    third elements.

Vector elements can have names

:   Vectors have a `names` property, the same length as the vector
    itself, that gives names to the elements:

    
    ``` r
    v <- c(10, 20, 30)
    names(v) <- c("Moe", "Larry", "Curly")
    print(v)
    #>   Moe Larry Curly 
    #>    10    20    30
    ```

If vector elements have names, then you can select them by name

:   Continuing the previous example:

    
    ``` r
    v[["Larry"]]
    #> [1] 20
    ```

### Lists {-}

Lists are heterogeneous

:   Lists can contain elements of different types; in R terminology,
    list elements may have different modes. Lists can even contain other
    structured objects, such as lists and data frames; this allows you
    to create recursive data structures.

Lists can be indexed by position

:   So `lst[[2]]` refers to the second element of `lst`. Note the double
    square brackets. Double brackets means that R will return the element as whatever type of element it is. 

Lists let you extract sublists

:   So `lst[c(2,3)]` is a sublist of `lst` that consists of the second
    and third elements. Note the single square brackets. Single brackets means that R will return the items in a list. If you pull a single element with single brackets, like `lst[2]`, R will return a list of length 1 with the first item being the desired item. 

List elements can have names

:   Both `lst[["Moe"]]` and `lst$Moe` refer to the element named “Moe.”

Since lists are heterogeneous and since their elements can be retrieved
by name, a list is like a dictionary or hash or lookup table in other
programming languages (discussed in Recipe \@ref(recipe-id158), ["Building a Name/Value Association List"](#recipe-id158). 

What’s surprising (and cool) is that in R, unlike most of those other
programming languages, lists can also be indexed by position.

### Mode: Physical Type {-}

In R, every object has a mode, which indicates how it is stored in
memory: as a number, as a character string, as a list of pointers to
other objects, as a function, and so forth (see Table \@ref(tab:phystype)).

  -------------------------------------------------------------------------
  Object                        Example                         Mode
  ----------------------------- ------------------------------- -----------
  Number                        `3.1415`                        numeric

  Vector of numbers             `c(2.7.182,                     numeric
                                                3.1415)`        

  Character string              `"Moe"`                         character

  Vector of character strings   `c("Moe", "Larry",              character
                                                "Curly")`       

  Factor                        `factor(c("NY", "CA",           numeric
                                                "IL"))`         

  List                          `list("Moe", "Larry",           list
                                                "Curly")`       

  Data frame                    `data.frame(x=1:3, y=c("NY",    list
                                                "CA", "IL"))`   

  Function                      `print`                         function
  -------------------------------------------------------------------------
  : (\#tab:phystype) R object-mode mapping

The `mode` function gives us this information:


``` r
mode(3.1415)                        # Mode of a number
#> [1] "numeric"
mode(c(2.7182, 3.1415))             # Mode of a vector of numbers
#> [1] "numeric"
mode("Moe")                         # Mode of a character string
#> [1] "character"
mode(list("Moe", "Larry", "Curly")) # Mode of a list
#> [1] "list"
```

A critical difference between a vector and a list can be summed up this
way:

-   In a vector, all elements must have the same mode.

-   In a list, the elements can have different modes.

### Class: Abstract Type {-}

In R, every object also has a class, which defines its abstract type.
The terminology is borrowed from object-oriented programming. A single
number could represent many different things: a distance, a point in
time, or a weight, for example. All those objects have a mode of “numeric” because they
are stored as a number, but they could have different classes to
indicate their interpretation.

For example, a `Date` object consists of a single number:


``` r
d <- as.Date("2010-03-15")
mode(d)
#> [1] "numeric"
length(d)
#> [1] 1
```

But it has a class of `Date`, telling us how to interpret that number—namely, as the number of days since January 1, 1970:


``` r
class(d)
#> [1] "Date"
```

R uses an object’s class to decide how to process the object. For
example, the generic function `print` has specialized versions (called
*methods*) for printing objects according to their class: `data.frame`,
`Date`, `lm`, and so forth. When you print an object, R calls the
appropriate `print` function according to the object’s class.

### Scalars {-}

The quirky thing about scalars is their relationship to vectors. In some
software, scalars and vectors are two different things. In R, they are
the same thing: a scalar is simply a vector that contains exactly one
element. In this book we often use the term *scalar*, but that’s just
shorthand for “vector with one element.”

Consider the built-in constant `pi`. It is a scalar:


``` r
pi
#> [1] 3.14
```

Since a scalar is a one-element vector, you can use vector functions on
`pi`:


``` r
length(pi)
#> [1] 1
```

You can index it. The first (and only) element is $\pi$ of course:


``` r
pi[1]
#> [1] 3.14
```

If you ask for the second element, there is none:


``` r
pi[2]
#> [1] NA
```

### Matrices {-}

In R, a matrix is just a vector that has dimensions. It may seem strange
at first, but you can transform a vector into a matrix simply by giving
it dimensions.

A vector has an attribute called `dim`, which is initially `NULL`, as
shown here:


``` r
A <- 1:6
dim(A)
#> NULL
print(A)
#> [1] 1 2 3 4 5 6
```

We give dimensions to the vector when we set its `dim` attribute. Watch
what happens when we set our vector dimensions to 2 × 3 and print it:


``` r
dim(A) <- c(2, 3)
print(A)
#>      [,1] [,2] [,3]
#> [1,]    1    3    5
#> [2,]    2    4    6
```

Voilà! The vector was reshaped into a 2 × 3 matrix.

A matrix can be created from a list, too. Like a vector, a list has a
`dim` attribute, which is initially `NULL`:


``` r
B <- list(1, 2, 3, 4, 5, 6)
dim(B)
#> NULL
```

If we set the `dim` attribute, it gives the list a shape:


``` r
dim(B) <- c(2, 3)
print(B)
#>      [,1] [,2] [,3]
#> [1,] 1    3    5   
#> [2,] 2    4    6
```

Voilà! We have turned this list into a 2 × 3 matrix.

### Arrays {-}

The discussion of matrices can be generalized to three-dimensional or even
*n*-dimensional structures: just assign more dimensions to the
underlying vector (or list). The following example creates a
three-dimensional array with dimensions 2 × 3 × 2:


``` r
D <- 1:12
dim(D) <- c(2, 3, 2)
print(D)
#> , , 1
#> 
#>      [,1] [,2] [,3]
#> [1,]    1    3    5
#> [2,]    2    4    6
#> 
#> , , 2
#> 
#>      [,1] [,2] [,3]
#> [1,]    7    9   11
#> [2,]    8   10   12
```

Note that R prints one “slice” of the structure at a time, since it’s
not possible to print a three-dimensional structure on a two-dimensional
medium.

It strikes us as very odd that we can turn a list into a matrix just by
giving the list a `dim` attribute. But wait: it gets stranger.

Recall that a list can be heterogeneous (mixed modes). We can start with
a heterogeneous list, give it dimensions, and thus create a
heterogeneous matrix. This code snippet creates a matrix that is a mix
of numeric and character data:


``` r
C <- list(1, 2, 3, "X", "Y", "Z")
dim(C) <- c(2, 3)
print(C)
#>      [,1] [,2] [,3]
#> [1,] 1    3    "Y" 
#> [2,] 2    "X"  "Z"
```

To us, this is strange because we ordinarily assume a matrix is purely
numeric, not mixed. R is not that restrictive.

The possibility of a heterogeneous matrix may seem powerful and
strangely fascinating. However, it creates problems when you are doing
normal, day-to-day stuff with matrices. For example, what happens when
the matrix `C` (from the previous example) is used in matrix multiplication? What happens if
it is converted to a data frame? The answer is that odd things happen.

In this book, we generally ignore the pathological case of a
heterogeneous matrix. We assume you’ve got simple, vanilla matrices. Some
recipes involving matrices may work oddly (or not at all) if your matrix
contains mixed data. Converting such a matrix to a vector or data frame,
for instance, can be problematic
(see Recipe \@ref(recipe-id074), ["Converting One Structured Data Type into Another"](#recipe-id074).)


### Factors {-}

A factor looks like a character vector, but it has special properties. R keeps
track of the unique values in a vector, and each unique value is called
a *level* of the associated factor. R uses a compact representation for
factors, which makes them efficient for storage in data frames. In other
programming languages, a factor would be represented by a vector of
enumerated values.

There are two key uses for factors:

Categorical variables

:   A factor can represent a categorical variable. Categorical variables
    are used in contingency tables, linear regression, analysis of
    variance (ANOVA), logistic regression, and many other areas.

Grouping 

:   This is a technique for labeling or tagging your data items
    according to their group. See Chapter \@ref(DataTransformations).

### Data Frames {-}

A data frame is a powerful and flexible structure. Most serious R
applications involve data frames. A data frame is intended to mimic a
dataset, such as one you might encounter in SAS or SPSS, or a table in an SQL database.

A data frame is a tabular (rectangular) data structure, which means that
it has rows and columns. It is not implemented by a matrix, however.
Rather, a data frame is a list with the following characteristics:

-   The elements of the list are vectors and/or factors.[^dataframe]

-   Those vectors and factors are the columns of the data frame.

-   The vectors and factors must all have the same length; in other
    words, all columns must have the same height.

-   The equal-height columns give a rectangular shape to the data frame.

-   The columns must have names.

Because a data frame is both a list and a rectangular structure, R
provides two different paradigms for accessing its contents:

-   You can use list operators to extract columns from a data frame,
    such as `df[i]`, `df[[i]]`, or `df$name`.

-   You can use matrix-like notation, such as `df[i,j]`, `df[i,]`,
    or `df[,j]`.

Your perception of a data frame likely depends on your background:

To a statistician

:   A data frame is a table of observations. Each row contains
    one observation. Each observation must contain the same variables.
    These variables are called columns, and you can refer to them
    by name. You can also refer to the contents by row number and column
    number, just as with a matrix.

To a SQL programmer

:   A data frame is a table. The table resides entirely in memory, but
    you can save it to a flat file and restore it later. You needn’t
    declare the column types because R figures that out for you.

To an Excel user

:   A data frame is like a worksheet, or perhaps a range within
    a worksheet. It is more restrictive, however, in that each column
    has a type.

To an SAS user

:   A data frame is like a SAS dataset for which all the data resides
    in memory. R can read and write the data frame to disk, but the data
    frame must be in memory while R is processing it.

To an R programmer

:   A data frame is a hybrid data structure, part matrix and part list.
    A column can contain numbers, character strings, or factors, but not
    a mix of them. You can index the data frame just like you index
    a matrix. The data frame is also a list, where the list elements are
    the columns, so you can access columns by using list operators.

To a computer scientist

:   A data frame is a rectangular data structure. The columns are
    typed, and each column must be numeric values, character
    strings, or a factor. Columns must have labels; rows may
    have labels. The table can be indexed by position, column name,
    and/or row name. It can also be accessed by list operators, in which
    case R treats the data frame as a list whose elements are the
    columns of the data frame.

To a corporate executive

:   You can put names and numbers into a data frame.
    A data frame is like a little database.
    Your staff will enjoy using data frames.

### Tibbles {-}

A *tibble* is a modern reimagining of the data frame, introduced by Hadley Wickham in the `tibble` package, which is a core package in the tidyverse.
Most of the common functions you would use with data frames also work with tibbles.
However, tibbles typically do less than data frames and complain more.
This idea of complaining and doing less may remind you of your least favorite coworker; however, we think tibbles will be one of your favorite data structures.
Doing less and complaining more can be a feature, not a bug. 

Unlike data frames, tibbles:

* Do not give you row numbers by default.
* Do not give you strange, unexpected column names.
* Don't coerce your data into factors (unless you explicitly ask for that).
* Recycle vectors of length 1 but not other lengths.

In addition to basic data frame functionality, tibbles:

* Print only the top four rows and a bit of metadata by default.
* Always return a tibble when subsetting.
* Never do partial matching: if you want a column from a tibble, you have to ask for it using its full name.
* Complain more by giving you more warnings and chatty messages to make sure you understand what the software is doing.

All these extras are designed to give you fewer surprises and help you make fewer mistakes.

Appending Data to a Vector {#recipe-id048}
--------------------------

### Problem {-#problem-id048}

You want to append additional data items to a vector.

### Solution {-#solution-id048}

Use the vector constructor (`c`) to construct a vector with the
additional data items:


``` r
v <- c(1, 2, 3)
newItems <- c(6, 7, 8)
c(v, newItems)
#> [1] 1 2 3 6 7 8
```

For a single item, you can also assign the new item to the next vector element.
R will automatically extend the vector:


``` r
v <- c(1, 2, 3)
v[length(v) + 1] <- 42
v
#> [1]  1  2  3 42
```

### Discussion {-#discussion-id048}

If you ask us about appending a data item to a vector, we will likely suggest
that maybe you shouldn’t.

> **Warning**
>
> R works best when you think about entire vectors, not single data
> items. Are you repeatedly appending items to a vector? If so, then you
> are probably working inside a loop. That’s OK for small vectors, but
> for large vectors your program will run slowly. The memory management
> in R works poorly when you repeatedly extend a vector by one element.
> Try to replace that loop with vector-level operations. You’ll write
> less code, and R will run much faster.

Nonetheless, one does occasionally need to append data to vectors. Our
experiments show that the most efficient way of doing so is to create a new vector
using the vector constructor (`c`) to join the old and new data. This
works for appending single elements or multiple elements:


``` r
v <- c(1, 2, 3)
v <- c(v, 4) # Append a single value to v
v
#> [1] 1 2 3 4

w <- c(5, 6, 7, 8)
v <- c(v, w) # Append an entire vector to v
v
#> [1] 1 2 3 4 5 6 7 8
```

You can also append an item by assigning it to the position past the end
of the vector, as shown in the Solution. In fact, R is very liberal
about extending vectors. You can assign to any element and R will expand
the vector to accommodate your request:


``` r
v <- c(1, 2, 3)   # Create a vector of three elements
v[10] <- 10       # Assign to the 10th element
v                 # R extends the vector automatically
#>  [1]  1  2  3 NA NA NA NA NA NA 10
```

Note that R did not complain about the out-of-bounds subscript. It just
extended the vector to the needed length, filling it with `NA`.

R includes an `append` function that creates a new vector by appending
items to an existing vector. However, our experiments show that this
function runs more slowly than both the vector constructor and the
element assignment.

Inserting Data into a Vector {#recipe-id049}
----------------------------

### Problem {-#problem-id049}

You want to insert one or more data items into a vector.

### Solution {-#solution-id049}

Despite its name, the `append` function inserts data into a vector by
using the `after` parameter, which gives the insertion point for the new
item or items:

`append(`*vec*`, `*newvalues*`, after = `*n*`)`

### Discussion {-#discussion-id049}

The new items will be inserted at the position given by `after`. This
example inserts 99 into the middle of a sequence:


``` r
append(1:10, 99, after = 5)
#>  [1]  1  2  3  4  5 99  6  7  8  9 10
```

The special value of `after=0` means insert the new items at the *head* of
the vector:


``` r
append(1:10, 99, after = 0)
#>  [1] 99  1  2  3  4  5  6  7  8  9 10
```

The comments in Recipe \@ref(recipe-id048), ["Appending Data to a Vector"](#recipe-id048) apply here,
too. If you are inserting single items into a vector, you might be
working at the element level when working at the vector level would be
easier to code and faster to run.

Understanding the Recycling Rule {#recipe-id050}
--------------------------------

### Problem {-#problem-id050}

You want to understand the mysterious Recycling Rule that governs how R
handles vectors of unequal length.

### Discussion {-#discussion-id050}

When you do vector arithmetic, R performs element-by-element operations.
That works well when both vectors have the same length: R pairs the
elements of the vectors and applies the operation to those pairs.

But what happens when the vectors have unequal lengths?

In that case, R invokes the Recycling Rule. It processes the vector
element in pairs, starting at the first elements of both vectors. At a
certain point, the shorter vector is exhausted while the longer vector
still has unprocessed elements. R returns to the beginning of the
shorter vector, “recycling” its elements; continues taking elements from
the longer vector; and completes the operation. It will recycle the
shorter-vector elements as often as necessary until the operation is
complete.

It’s useful to visualize the Recycling Rule. Here is a diagram of two
vectors, 1:6 and 1:3:

```{}
   1:6   1:3
  ----- -----
    1     1
    2     2
    3     3
    4
    5
    6
```

Obviously, the 1:6 vector is longer than the 1:3 vector. If we try to
add the vectors using (1:6) + (1:3), it appears that 1:3 has too few
elements. However, R recycles the elements of 1:3, pairing the two
vectors like this and producing a six-element vector:

```{}
   1:6   1:3   (1:6) + (1:3)
  ----- ----- ---------------
    1     1         2
    2     2         4
    3     3         6
    4               5
    5               7
    6               9
```
Here is what you see in the R console:


``` r
(1:6) + (1:3)
#> [1] 2 4 6 5 7 9
```

It’s not only vector operations that invoke the Recycling Rule;
functions can, too. The `cbind` function can create column vectors, such
as the following column vectors of 1:6 and 1:3. The two column have
different heights, of course:

```r}
cbind(1:6)

cbind(1:3)
```

If we try binding these column vectors together into a two-column
matrix, the lengths are mismatched. The 1:3 vector is too short, so
`cbind` invokes the Recycling Rule and recycles the elements of 1:3:


``` r
cbind(1:6, 1:3)
#>      [,1] [,2]
#> [1,]    1    1
#> [2,]    2    2
#> [3,]    3    3
#> [4,]    4    1
#> [5,]    5    2
#> [6,]    6    3
```

If the longer vector’s length is not a multiple of the shorter vector’s
length, R gives a warning. That’s good, since the operation is highly
suspect and there is likely a bug in your logic:


``` r
(1:6) + (1:5) # Oops! 1:5 is one element too short
#> Warning in (1:6) + (1:5): longer object length is not a multiple of shorter
#> object length
#> [1]  2  4  6  8 10  7
```

Once you understand the Recycling Rule, you will realize that operations
between a vector and a scalar are simply applications of that rule. In
this example, the 10 is recycled repeatedly until the vector addition is
complete:


``` r
(1:6) + 10
#> [1] 11 12 13 14 15 16
```

Creating a Factor (Categorical Variable) {#recipe-id051}
----------------------------------------

### Problem {-#problem-id051}

You have a vector of character strings or integers. You want R to treat
them as a factor, which is R’s term for a categorical variable.

### Solution {-#solution-id051}

The `factor` function encodes your vector of discrete values into a factor:

```
f <- factor(v)   # v can be a vector of strings or integers
```

If your vector contains only a subset of possible values and not the
entire universe, then include a second argument that gives the possible
levels of the factor:

```
f <- factor(v, levels)
```

### Discussion {-#discussion-id051}

In R, each possible value of a categorical variable is called a *level*.
A vector of levels is called a *factor*.
Factors fit very cleanly into the vector orientation of R,
and they are used in powerful ways for processing data and building statistical models.

Most of the time, converting your categorical data into a factor is a
simple matter of calling the `factor` function, which identifies the
distinct levels of the categorical data and packs them into a factor:


``` r
f <- factor(c("Win", "Win", "Lose", "Tie", "Win", "Lose"))
f
#> [1] Win  Win  Lose Tie  Win  Lose
#> Levels: Lose Tie Win
```

Notice that when we printed the factor, `f`, R did not put quotes around
the values. They are levels, not strings. Also notice that when we
printed the factor, R also displayed the distinct levels below the
factor.

If your vector contains only a subset of all the possible levels, then R
will have an incomplete picture of the possible levels. Suppose you have
a string-valued variable `wday` that gives the day of the week on which
your data was observed:


``` r
wday <- c("Wed", "Thu", "Mon", "Wed", "Thu",
          "Thu", "Thu", "Tue", "Thu", "Tue")
f <- factor(wday)
f
#>  [1] Wed Thu Mon Wed Thu Thu Thu Tue Thu Tue
#> Levels: Mon Thu Tue Wed
```

R thinks that Monday, Thursday, Tuesday, and Wednesday are the only
possible levels. Friday is not listed. Apparently, the lab staff never
made observations on Friday, so R does not know that Friday is a
possible value. Hence, you need to list the possible levels of `wday`
explicitly:


``` r
f <- factor(wday, levels=c("Mon", "Tue", "Wed", "Thu", "Fri"))
f
#>  [1] Wed Thu Mon Wed Thu Thu Thu Tue Thu Tue
#> Levels: Mon Tue Wed Thu Fri
```

Now R understands that `f` is a factor with five possible levels.
It knows their correct order, too.
It originally put Thursday before Tuesday because it assumes alphabetical order by default.
The explicit `levels` argument defines the correct order.

In many situations it is not necessary to call `factor` explicitly.
When an R function requires a factor, it usually converts your data to a
factor automatically.
The `table` function, for instance, works only on factors,
so it routinely converts its inputs to factors without asking.
You must explicitly create a factor variable when you want to specify
the full set of levels or when you want to control the ordering of levels.

### See Also {-#see_also-id051}

See Recipe \@ref(recipe-id137), ["Binning Your Data"](#recipe-id137), to create a factor from continuous data.


Combining Multiple Vectors into One Vector and a Factor {#recipe-id227}
-------------------------------------------------------

### Problem {-#problem-id227}

You have several groups of data, with one vector for each group. You
want to combine the vectors into one large vector and simultaneously
create a parallel factor that identifies each value’s original group.

### Solution {-#solution-id227}

Create a list that contains the vectors. Use the `stack` function to
combine the list into a two-column data frame:

```
comb <- stack(list(v1 = v1, v2 = v2, v3 = v3)) # Combine 3 vectors
```

The data frame’s columns are called `values` and `ind`. The first column
contains the data, and the second column contains the parallel factor.

### Discussion {-#discussion-id227}

Why in the world would you want to mash all your data into one big
vector and a parallel factor? The reason is that many important
statistical functions require the data in that format.

Suppose you survey freshmen, sophomores, and juniors regarding their
confidence level (“What percentage of the time do you feel confident in
school?”). Now you have three vectors, called `freshmen`, `sophomores`,
and `juniors`. You want to perform an ANOVA of the differences
between the groups. The ANOVA function, `aov`, requires one vector with
the survey results as well as a parallel factor that identifies the
group. You can combine the groups using the `stack` function:


``` r
freshmen <- c(1, 2, 1, 1, 5)
sophomores <- c(3, 2, 3, 3, 5)
juniors <- c(5, 3, 4, 3, 3)

comb <- stack(list(fresh = freshmen, soph = sophomores, jrs = juniors))
print(comb)
#>    values   ind
#> 1       1 fresh
#> 2       2 fresh
#> 3       1 fresh
#> 4       1 fresh
#> 5       5 fresh
#> 6       3  soph
#> 7       2  soph
#> 8       3  soph
#> 9       3  soph
#> 10      5  soph
#> 11      5   jrs
#> 12      3   jrs
#> 13      4   jrs
#> 14      3   jrs
#> 15      3   jrs
```

Now you can perform the ANOVA on the two columns:

```
aov(values ~ ind, data = comb)
```

When building the list we must provide tags for the list elements.
(The tags are `fresh`, `soph`, and `jrs` in this example.)
Those tags are required because `stack` uses them as the levels of the parallel factor.

Creating a List {#recipe-id053}
-------------------------------

### Problem {-#problem-id053}

You want to create and populate a list.

### Solution {-#solution-id053}

To create a list from individual data items, use the `list` function:

```
lst <- list(x, y, z)
```

### Discussion {-#discussion-id053}

Lists can be quite simple, such as this list of three numbers:


``` r
lst <- list(0.5, 0.841, 0.977)
lst
#> [[1]]
#> [1] 0.5
#> 
#> [[2]]
#> [1] 0.841
#> 
#> [[3]]
#> [1] 0.977
```

When R prints the list, it identifies each list element by its position
(`[[1]]`, `[[2]]`, `[[3]]`) and prints the element’s value (e.g.,
`[1] 0.5`) under its position.

More usefully, lists can, unlike vectors, contain elements of different
modes (types). Here is an extreme example of a mongrel created from a
scalar, a character string, a vector, and a function:


``` r
lst <- list(3.14, "Moe", c(1, 1, 2, 3), mean)
lst
#> [[1]]
#> [1] 3.14
#> 
#> [[2]]
#> [1] "Moe"
#> 
#> [[3]]
#> [1] 1 1 2 3
#> 
#> [[4]]
#> function (x, ...) 
#> UseMethod("mean")
#> <bytecode: 0x104434e08>
#> <environment: namespace:base>
```

You can also build a list by creating an empty list and populating it.
Here is our “mongrel” example built in that way:


``` r
lst <- list()
lst[[1]] <- 3.14
lst[[2]] <- "Moe"
lst[[3]] <- c(1, 1, 2, 3)
lst[[4]] <- mean
lst
#> [[1]]
#> [1] 3.14
#> 
#> [[2]]
#> [1] "Moe"
#> 
#> [[3]]
#> [1] 1 1 2 3
#> 
#> [[4]]
#> function (x, ...) 
#> UseMethod("mean")
#> <bytecode: 0x104434e08>
#> <environment: namespace:base>
```

List elements can be named. The `list` function lets you supply a name
for every element:


``` r
lst <- list(mid = 0.5, right = 0.841, far.right = 0.977)
lst
#> $mid
#> [1] 0.5
#> 
#> $right
#> [1] 0.841
#> 
#> $far.right
#> [1] 0.977
```

### See Also {-#see_also-id053}

See the [Introduction](#intro-DataStructures) to this chapter for more
about lists; see Recipe \@ref(recipe-id158), ["Building a Name/Value Association List"](#recipe-id158),
for more about building and using lists with named elements.

Selecting List Elements by Position {#recipe-id054}
---------------------------------------------------

### Problem {-#problem-id054}

You want to access list elements by position.

### Solution {-#solution-id054}

Use one of these ways. Here, `lst` is a list variable:

`lst[[`_n_`]]`

:   Select the *n*th element from the list.

`lst[c(_n_~1~, _n_~2~, ..., _n_~k~)]`

:   Returns a list of elements, selected by their positions.

Note that the first form returns a single element and the second form returns
a list.

### Discussion {-#discussion-id054}

Suppose we have a list of four integers, called `years`:


``` r
years <- list(1960, 1964, 1976, 1994)
years
#> [[1]]
#> [1] 1960
#> 
#> [[2]]
#> [1] 1964
#> 
#> [[3]]
#> [1] 1976
#> 
#> [[4]]
#> [1] 1994
```

We can access single elements using the double-square-bracket syntax:


``` r
years[[1]]
#> [1] 1960
```

We can extract sublists using the single-square-bracket syntax:


``` r
years[c(1, 2)]
#> [[1]]
#> [1] 1960
#> 
#> [[2]]
#> [1] 1964
```

This syntax can be confusing because of a subtlety:
there is an important difference between `lst[[`*n*`]]` and `lst[`*n*`]`.
They are not the same thing:

`lst[[`_n_`]]`

:   This is an element, not a list. It is the *n*th element of `lst`.

`lst[`_n_`]`

:   This is a list, not an element. The list contains one element, taken
    from the *n*th element of `lst`.
    
(The second form is a special case of
`lst[c(_n_~1~, _n_~2~, ..., _n_~k~)]` in which we eliminated the `c(...)` construct
because there is only one *n*.)

The difference becomes apparent when we inspect the structure of the
result — one is a number and the other is a list:


``` r
class(years[[1]])
#> [1] "numeric"

class(years[1])
#> [1] "list"
```

The difference becomes annoyingly apparent when we `cat` the value.
Recall that `cat` can print atomic values or vectors but complains about
printing structured objects:


``` r
cat(years[[1]], "\n")
#> 1960

cat(years[1], "\n")
#> Error in cat(years[1], "\n"): argument 1 (type 'list') cannot be handled by 'cat'
```

We got lucky here because R alerted us to the problem. In other
contexts, you might work long and hard to figure out that you accessed a
sublist when you wanted an element, or vice versa.

Selecting List Elements by Name {#recipe-id253}
-------------------------------

### Problem {-#problem-id253}

You want to access list elements by their names.

### Solution {-#solution-id253}

Use one of these forms. Here, `lst` is a list variable:

`lst[["*name*"]]`

:   Selects the element called **name**.
    Returns `NULL` if no element has that name.

`lst$*name*`

:   Same as previous, just different syntax.

`lst[c(*name*~1~, *name*~2~, ..., *name*~k~)]`

:   Returns a list built from the indicated elements of `lst`.

Note that the first two forms return an element, whereas the third form
returns a list.

### Discussion {-#discussion-id253}

Each element of a list can have a name. If named, the element can be
selected by its name. This assignment creates a list of four named
integers:


``` r
years <- list(Kennedy = 1960, Johnson = 1964,
              Carter = 1976, Clinton = 1994)
```

These next two expressions return the same value—namely, the element
that is named “Kennedy”:


``` r
years[["Kennedy"]]
#> [1] 1960
years$Kennedy
#> [1] 1960
```

The following two expressions return sublists extracted from `years`:


``` r
years[c("Kennedy", "Johnson")]
#> $Kennedy
#> [1] 1960
#> 
#> $Johnson
#> [1] 1964

years["Carter"]
#> $Carter
#> [1] 1976
```

Just as with selecting list elements by position
(see Recipe \@ref(recipe-id054), ["Selecting List Elements by Position"](#recipe-id054)),
there is an important difference between `lst[["*name*"]]` and `lst["*name*"]`.
They are not the same:

`lst[["*name*"]]`

:   This is an element, not a list.

`lst["*name*"]`

:   This is a list, not an element.

(The second form is a special case of
`lst[c(*name~1~*, *name~2~*, ..., *name~k~*)]` in which we don’t need the `c(...)`
construct because there is only one name.)

### See Also {-#see_also-id253}

See Recipe \@ref(recipe-id054), ["Selecting List Elements by Position"](#recipe-id054), to access
elements by position rather than by name.

Building a Name/Value Association List {#recipe-id158}
--------------------------------------

### Problem {-#problem-id158}

You want to create a list that associates names and values,
such as a dictionary, hash, or lookup table would in another programming language.

### Solution {-#solution-id158}

The `list` function lets you give names to elements, creating an
association between each name and its value:

```
lst <- list(mid = 0.5, right = 0.841, far.right = 0.977)
```

If you have parallel vectors of names and values, you can create an
empty list and then populate the list by using a vectorized assignment
statement:


``` r
values <- c(1, 2, 3)
names <- c("a", "b", "c")
lst <- list()
lst[names] <- values
```

### Discussion {-#discussion-id158}

Each element of a list can be named, and you can retrieve list elements
by name. This gives you a basic programming tool: the ability to
associate names with values.

You can assign element names when you build the list.
The `list` function allows arguments of the form `*name*=*value*`:


``` r
lst <- list(
  far.left = 0.023,
  left = 0.159,
  mid = 0.500,
  right = 0.841,
  far.right = 0.977
)
lst
#> $far.left
#> [1] 0.023
#> 
#> $left
#> [1] 0.159
#> 
#> $mid
#> [1] 0.5
#> 
#> $right
#> [1] 0.841
#> 
#> $far.right
#> [1] 0.977
```

One way to name the elements is to create an empty list and then
populate it via assignment statements:


``` r
lst <- list()
lst$far.left <- 0.023
lst$left <- 0.159
lst$mid <- 0.500
lst$right <- 0.841
lst$far.right <- 0.977
```

Sometimes you have a vector of names and a vector of corresponding
values:


``` r
values <- -2:2
names <- c("far.left", "left", "mid", "right", "far.right")
```

You can associate the names and the values by creating an empty list and
then populating it with a vectorized assignment statement:


``` r
lst <- list()
lst[names] <- values
lst
#> $far.left
#> [1] -2
#> 
#> $left
#> [1] -1
#> 
#> $mid
#> [1] 0
#> 
#> $right
#> [1] 1
#> 
#> $far.right
#> [1] 2
```

Once the association is made, the list can “translate” names into values
through a simple list lookup:


``` r
cat("The left limit is", lst[["left"]], "\n")
#> The left limit is -1
cat("The right limit is", lst[["right"]], "\n")
#> The right limit is 1

for (nm in names(lst)) cat("The", nm, "limit is", lst[[nm]], "\n")
#> The far.left limit is -2 
#> The left limit is -1 
#> The mid limit is 0 
#> The right limit is 1 
#> The far.right limit is 2
```

Removing an Element from a List {#recipe-id056}
-------------------------------

### Problem {-#problem-id056}

You want to remove an element from a list.

### Solution {-#solution-id056}

Assign `NULL` to the element. R will remove it from the list.

### Discussion {-#discussion-id056}

To remove a list element, select it by position or by name,
and then assign `NULL` to the selected element:


``` r
years <- list(Kennedy = 1960, Johnson = 1964,
              Carter = 1976, Clinton = 1994)
years
#> $Kennedy
#> [1] 1960
#> 
#> $Johnson
#> [1] 1964
#> 
#> $Carter
#> [1] 1976
#> 
#> $Clinton
#> [1] 1994
years[["Johnson"]] <- NULL # Remove the element labeled "Johnson"
years
#> $Kennedy
#> [1] 1960
#> 
#> $Carter
#> [1] 1976
#> 
#> $Clinton
#> [1] 1994
```

You can remove multiple elements this way, too:


``` r
years[c("Carter", "Clinton")] <- NULL # Remove two elements
years
#> $Kennedy
#> [1] 1960
```

Flatten a List into a Vector {#recipe-id152}
----------------------------

### Problem {-#problem-id152}

You want to flatten all the elements of a list into a vector.

### Solution {-#solution-id152}

Use the `unlist` function.

### Discussion {-#discussion-id152}

There are many contexts that require a vector.
Basic statistical functions work on vectors but not on lists, for example.
If `iq.scores` is a list of numbers, then we cannot directly compute their mean:


``` r
iq.scores <- list(100, 120, 103, 80, 99)
mean(iq.scores)
#> Warning in mean.default(iq.scores): argument is not numeric or logical:
#> returning NA
#> [1] NA
```

Instead, we must flatten the list into a vector using `unlist` and then
compute the mean of the result:


``` r
mean(unlist(iq.scores))
#> [1] 100
```

Here is another example. We can `cat` scalars and vectors, but we cannot
`cat` a list:


``` r
cat(iq.scores, "\n")
#> Error in cat(iq.scores, "\n"): argument 1 (type 'list') cannot be handled by 'cat'
```

One solution is to flatten the list into a vector before printing:


``` r
cat("IQ Scores:", unlist(iq.scores), "\n")
#> IQ Scores: 100 120 103 80 99
```

### See Also {-#see_also-id152}

Conversions such as this are discussed more fully in Recipe \@ref(recipe-id074),
["Converting One Structured Data Type into Another"](#recipe-id074).

Removing NULL Elements from a List {#recipe-id058}
----------------------------------

### Problem {-#problem-id058}

Your list contains `NULL` values. You want to remove them.

### Solution {-#solution-id058}

The `compact` from the `purrr` package will remove the `NULL` elements:

### Discussion {-#discussion-id058}
The curious reader may be wondering how a list can contain `NULL`
elements, given that we remove elements by setting them to `NULL`
(see Recipe \@ref(recipe-id056), ["Removing an Element from a List"](#recipe-id056)).
The answer is that we can *create* a list containing `NULL` elements:


``` r
library(purrr)     # or library(tidyverse)

lst <- list("Moe", NULL, "Curly")
lst
#> [[1]]
#> [1] "Moe"
#> 
#> [[2]]
#> NULL
#> 
#> [[3]]
#> [1] "Curly"

compact(lst)   # Remove NULL element
#> [[1]]
#> [1] "Moe"
#> 
#> [[2]]
#> [1] "Curly"
```

In practice we might end up with `NULL` items in a list after applying some transformation.

### See Also {-#see_also-id058}
See Recipe \@ref(recipe-id056), ["Removing an Element from a List"](#recipe-id056) for how to remove
list elements.

In R, `NA` and `NULL` are not the same thing.
The `compact` function will remove `NULL` from a list but not `NA`.
To remove `NA` values, see Recipe \@ref(recipe-id060), 
["Removing List Elements Using a Condition"](#recipe-id060).

Removing List Elements Using a Condition {#recipe-id060}
----------------------------------------

### Problem {-#problem-id060}

You want to remove elements from a list according to a conditional test,
such as removing elements that are undefined, negative, or smaller than some threshold.

### Solution {-#solution-id060}
Start with a function that returns `TRUE` when your criteria is met and `FALSE` otherwise.
Then use the `discard` function from `purrr` to remove values that match your criteria.
This code snippet, for example, uses the `is.na` function to remove NA values from `lst`:


``` r
lst <- list(NA, 0, NA, 1, 2)

lst %>%
  discard(is.na)
#> [[1]]
#> [1] 0
#> 
#> [[2]]
#> [1] 1
#> 
#> [[3]]
#> [1] 2
```

### Discussion {-#discussion-id060}
The `discard` function removes elements from a list using a *predicate*,
which is a function that returns either `TRUE` or `FALSE`.
The predicate is applied to each element of the list.
If the predicate returns `TRUE`, the element is discarded; otherwise, it is kept.

Suppose we want to remove character strings from `lst`.
The function `is.character` is a predicate that returns `TRUE` if its argument is a character string,
so we can use it with `discard`.


``` r
lst <- list(3, "dog", 2, "cat", 1)

lst %>%
  discard(is.character)
#> [[1]]
#> [1] 3
#> 
#> [[2]]
#> [1] 2
#> 
#> [[3]]
#> [1] 1
```

You can define your own predicate and use it with `discard`.
This example removes both `NA` and `NULL` values from a list
by defining the predicate `is_na_or_null`:


``` r
is_na_or_null <- function(x) {
  is.na(x) || is.null(x)
}

lst <- list(1, NA, 2, NULL, 3)

lst %>%
  discard(is_na_or_null)
#> [[1]]
#> [1] 1
#> 
#> [[2]]
#> [1] 2
#> 
#> [[3]]
#> [1] 3
```

Lists can hold complex objects, too, not just atomic values.
Suppose that `mods` is a list of linear models created by the `lm` function.


``` r
mods <- list(lm(x ~ y1),
             lm(x ~ y2),
             lm(x ~ y3))
```

We can define a predicate, `filter_r2`, to identify models
whose *R*^2^ values are less than 0.70,
then use the predicate to remove models from `mods`:


``` r
filter_r2 <- function(model) {
  summary(model)$r.squared < 0.7
}

mods %>%
  discard(filter_r2)
```

### See Also {-#see_also-id060}
See Recipes \@ref(recipe-id054), ["Selecting List Elements by Position"](#recipe-id054),
\@ref(recipe-id056), ["Removing an Element from a List"](#recipe-id056),
and \@ref(recipe-id117), ["Defining a Function"](#recipe-id117).

This recipe uses the `discard` function.
The inverse of `discard` is the `keep` function,
which uses a predicate to *retain* list elements instead of discarding them.


Initializing a Matrix {#recipe-id062}
---------------------

### Problem {-#problem-id062}

You want to create a matrix and initialize it from given values.

### Solution {-#solution-id062}

Capture the data in a vector or list, and then use the `matrix` function
to shape the data into a matrix. This example shapes a vector into a 2 ×
3 matrix (i.e., two rows and three columns):


``` r
vec <- 1:6
matrix(vec, 2, 3)
#>      [,1] [,2] [,3]
#> [1,]    1    3    5
#> [2,]    2    4    6
```

### Discussion {-#discussion-id062}

The first argument of `matrix` is the data, the second argument is the
number of rows, and the third argument is the number of columns.
Note that the matrix in the Solution was filled column by column, not row by row.

It’s common to initialize an entire matrix to one value, such as `0` or
`NA`. If the first argument of `matrix` is a single value, then R will
apply the Recycling Rule and automatically replicate the value to fill
the entire matrix:


``` r
matrix(0, 2, 3) # Create an all-zeros matrix
#>      [,1] [,2] [,3]
#> [1,]    0    0    0
#> [2,]    0    0    0

matrix(NA, 2, 3) # Create a matrix populated with NA
#>      [,1] [,2] [,3]
#> [1,]   NA   NA   NA
#> [2,]   NA   NA   NA
```

You can create a matrix with a one-liner, of course, but it becomes
difficult to read:


``` r
mat <- matrix(c(1.1, 1.2, 1.3, 2.1, 2.2, 2.3), 2, 3)
mat
#>      [,1] [,2] [,3]
#> [1,]  1.1  1.3  2.2
#> [2,]  1.2  2.1  2.3
```

A common idiom in R is typing the data itself in a rectangular shape
that reveals the matrix structure:


``` r
theData <- c(
  1.1, 1.2, 1.3,
  2.1, 2.2, 2.3
)
mat <- matrix(theData, 2, 3, byrow = TRUE)
mat
#>      [,1] [,2] [,3]
#> [1,]  1.1  1.2  1.3
#> [2,]  2.1  2.2  2.3
```

Setting `byrow=TRUE` tells `matrix` that the data is row-by-row and not
column-by-column (which is the default). In condensed form, that
becomes:


``` r
mat <- matrix(c(1.1, 1.2, 1.3,
                2.1, 2.2, 2.3),
              2, 3,
              byrow = TRUE)
```

Expressed this way, it's easy to see the two rows and three
columns of data.

There is a quick-and-dirty way to turn a vector into a matrix:
just assign dimensions to the vector.
This was discussed in the [Introduction](#intro-DataStructures).
The following example creates a
vanilla vector and then shapes it into a 2 × 3 matrix:


``` r
v <- c(1.1, 1.2, 1.3, 2.1, 2.2, 2.3)
dim(v) <- c(2, 3)
v
#>      [,1] [,2] [,3]
#> [1,]  1.1  1.3  2.2
#> [2,]  1.2  2.1  2.3
```

We find this more opaque than using `matrix`,
especially since there is no `byrow` option here.

### See Also {-#see_also-id062}

See Recipe \@ref(recipe-id050), ["Understanding the Recycling Rule"](#recipe-id050).

Performing Matrix Operations {#recipe-id139}
----------------------------

### Problem {-#problem-id139}

You want to perform matrix operations such as transpose, matrix
inversion, matrix multiplication, or constructing an identity matrix.

### Solution {-#solution-id139}

`t(A)`

:   Matrix transposition of A

`solve(A)`

:   Matrix inverse of A

`A %*% B`

:   Matrix multiplication of A and B

`diag(*n*)`

:   An *n*-by-*n* diagonal (identity) matrix

### Discussion {-#discussion-id139}

Recall that `A*B` is element-wise multiplication, whereas `A` `%*%` `B`
is matrix multiplication.

All these functions return a matrix. Their arguments can be either
matrices or data frames. If they are data frames, then R will first
convert them to matrices (although this is useful only if the data frame
contains exclusively numeric values).

Giving Descriptive Names to the Rows and Columns of a Matrix {#recipe-id063}
------------------------------------------------------------

### Problem {-#problem-id063}

You want to assign descriptive names to the rows or columns of a matrix.

### Solution {-#solution-id063}

Every matrix has a `rownames` attribute and a `colnames` attribute.
Assign a vector of character strings to the appropriate attribute:


``` r
rownames(mat) <- c("rowname1", "rowname2", ..., "rownameN")
colnames(mat) <- c("colname1", "colname2", ..., "colnameN")
```

### Discussion {-#discussion-id063}

R lets you assign names to the rows and columns of a matrix, which is
useful for printing the matrix.
R will display the names if they are defined, enhancing the readability of your output.
Consider this matrix of correlations between the stock prices of IBM, Microsoft,and Google:




``` r
print(corr_mat)
#>       [,1]  [,2]  [,3]
#> [1,] 1.000 0.556 0.390
#> [2,] 0.556 1.000 0.444
#> [3,] 0.390 0.444 1.000
```

In this form, the interpretation of the matrix is not self-evident.
We can give names to the rows and columns, clarifying its meaning:


``` r
colnames(corr_mat) <- c("AAPL", "MSFT", "GOOG")
rownames(corr_mat) <- c("AAPL", "MSFT", "GOOG")
corr_mat
#>       AAPL  MSFT  GOOG
#> AAPL 1.000 0.556 0.390
#> MSFT 0.556 1.000 0.444
#> GOOG 0.390 0.444 1.000
```

Now you can see at a glance which rows and columns apply to which
stocks.

Another advantage of naming rows and columns is that you can refer to
matrix elements by those names:


``` r
# What is the correlation between MSFT and GOOG?
corr_mat["MSFT", "GOOG"]
#> [1] 0.444
```

Selecting One Row or Column from a Matrix {#recipe-id250}
-----------------------------------------

### Problem {-#problem-id250}

You want to select a single row or a single column from a matrix.

### Solution {-#solution-id250}

The solution depends on what you want.
If you want the result to be a simple vector, just use normal indexing:


``` r
mat[1, ]     # First row
mat[, 3]     # Third column
```

If you want the result to be a one-row matrix or a one-column matrix,
then include the `drop=FALSE` argument:


``` r
mat[1, , drop=FALSE]   # First row, one-row matrix
mat[, 3, drop=FALSE]   # Third column, one-column matrix
```

### Discussion {-#discussion-id250}

Normally, when you select one row or column from a matrix, R strips off
the dimensions. The result is a dimensionless vector:




``` r
mat[1, ]
#> [1] 1.1 1.2 1.3
mat[, 3]
#> [1] 1.3 2.3
```

When you include the `drop=FALSE` argument, however, R retains the
dimensions. In that case, selecting a row returns a row vector (a 1 ×
*n* matrix):


``` r
mat[1, , drop=FALSE]
#>      [,1] [,2] [,3]
#> [1,]  1.1  1.2  1.3
```

Likewise, selecting a column with `drop=FALSE` returns a column vector
(an *n* × 1 matrix):


``` r
mat[, 3, drop=FALSE]
#>      [,1]
#> [1,]  1.3
#> [2,]  2.3
```

Initializing a Data Frame from Column Data {#recipe-id251}
------------------------------------------

### Problem {-#problem-id251}

Your data is organized by columns, and you want to assemble it into a
data frame.

### Solution {-#solution-id251}

If your data is captured in several vectors and/or factors, use the
`data.frame` function to assemble them into a data frame:


``` r
df <- data.frame(v1, v2, v3, f1)
```

If your data is captured in a *list* that contains vectors and/or
factors, use `as.data.frame` instead:


``` r
df <- as.data.frame(list.of.vectors)
```

### Discussion {-#discussion-id251}

A data frame is a collection of columns, each of which corresponds to an
observed variable (in the statistical sense, not the programming sense).
If your data is already organized into columns, then it’s easy to build
a data frame.

The `data.frame` function can construct a data frame from vectors, where
each vector is one observed variable. Suppose you have two numeric
variables, one character variable, and one response variable.
The `data.frame` function can create a data frame from your vectors.




``` r
data.frame(pred1, pred2, pred3, resp)
#>   pred1 pred2 pred3 resp
#> 1  1.75  11.8    AM 13.2
#> 2  4.01  10.7    PM 12.9
#> 3  2.64  12.2    AM 13.9
#> 4  6.03  12.2    PM 14.9
#> 5  2.78  15.0    PM 16.4
```

Notice that `data.frame` takes the column names from your program
variables. You can override that default by supplying explicit column
names:


``` r
data.frame(p1 = pred1, p2 = pred2, p3 = pred3, r = resp)
#>     p1   p2 p3    r
#> 1 1.75 11.8 AM 13.2
#> 2 4.01 10.7 PM 12.9
#> 3 2.64 12.2 AM 13.9
#> 4 6.03 12.2 PM 14.9
#> 5 2.78 15.0 PM 16.4
```

If you'd rather have a tibble than a data frame, use the `tibble` function from the tidyverse:


``` r
tibble(p1 = pred1, p2 = pred2, p3 = pred3, r = resp)
#> # A tibble: 5 x 4
#>      p1    p2 p3        r
#>   <dbl> <dbl> <fct> <dbl>
#> 1  1.75  11.8 AM     13.2
#> 2  4.01  10.7 PM     12.9
#> 3  2.64  12.2 AM     13.9
#> 4  6.03  12.2 PM     14.9
#> 5  2.78  15.0 PM     16.4
```

Sometimes, your data may indeed be organized into vectors,
but those vectors are held in a list, not individual program variables.


``` r
list.of.vectors <- list(p1=pred1, p2=pred2, p3=pred3, r=resp)
```

In that case, use the `as.data.frame` function to create a data frame from the list:


``` r
as.data.frame(list.of.vectors)
#>     p1   p2 p3    r
#> 1 1.75 11.8 AM 13.2
#> 2 4.01 10.7 PM 12.9
#> 3 2.64 12.2 AM 13.9
#> 4 6.03 12.2 PM 14.9
#> 5 2.78 15.0 PM 16.4
```

or use `as_tibble` to create a tibble:


``` r
as_tibble(list.of.vectors)
#> # A tibble: 5 x 4
#>      p1    p2 p3        r
#>   <dbl> <dbl> <fct> <dbl>
#> 1  1.75  11.8 AM     13.2
#> 2  4.01  10.7 PM     12.9
#> 3  2.64  12.2 AM     13.9
#> 4  6.03  12.2 PM     14.9
#> 5  2.78  15.0 PM     16.4
```

#### Factors in data frames {-}

There is an important difference between creating a data frame and creating a tibble.
When you use the `data.frame` function to create a data frame,
R will convert character values into factors by default.
The `pred3` value in the preceding `data.frame` example was converted to a factor,
but that is not evident from the output.

The `tibble` and `as_tibble` functions, however, do not change character data.
If you look at the `tibble` example,
you'll see column `p3` has type `chr`, meaning character.

This difference is something you should be aware of.
It can be maddeningly frustrating to debug an issue caused by this subtle difference. 


Initializing a Data Frame from Row Data {#recipe-id252}
---------------------------------------

### Problem {-#problem-id252}

Your data is organized by rows, and you want to assemble it into a data
frame.

### Solution {-#solution-id252}

Store each row in a one-row data frame.
Use `rbind` to bind the rows into one large data frame:


``` r
rbind(row1, row2, . . . , rowN)
```

### Discussion {-#discussion-id252}

Data often arrives as a collection of observations.
Each observation is a record or tuple that contains several values, one for each observed variable.
The lines of a flat file are usually like that: each line is one record,
each record contains several columns,
and each column is a different variable (see Recipe \@ref(recipe-id098), ["Reading Files with a Complex Structure"](#recipe-id098)).
Such data is organized by *observation*, not by *variable*.
In other words, you are given rows one at a time rather than columns one at a time.

Each such row might be stored in several ways. One obvious way is as a
vector. If you have purely numerical data, use a vector.

Many datasets, however, are a mixture of numeric, character, and
categorical data, in which case a vector won’t work.
We recommend storing each such heterogeneous row in a one-row data frame.
(You could store each row in a list, but this recipe gets a little more complicated.)

We need to bind together those rows into a data frame.
That’s what the `rbind` function does.
It binds its arguments in such a way that each argument becomes one row in the result.
If we `rbind` these three observations, for example, we get a three-row data frame:


``` r
r1 <- data.frame(a = 1, b = 2, c = "X")
r2 <- data.frame(a = 3, b = 4, c = "Y")
r3 <- data.frame(a = 5, b = 6, c = "Z")
rbind(r1, r2, r3)
#>   a b c
#> 1 1 2 X
#> 2 3 4 Y
#> 3 5 6 Z
```

When you're working with a large number of rows,
they will likely be stored in a list;
that is, you will have a list of rows.
The `bind_rows` function from the tidyverse package `dplyr`, handles that case, as shown in this toy example:


``` r
list.of.rows <- list(r1, r2, r3)
bind_rows(list.of.rows)
#>   a b c
#> 1 1 2 X
#> 2 3 4 Y
#> 3 5 6 Z
```

Sometimes, for reasons beyond your control, each row of data is
stored in a list rather than one-row data frames.
You may be dealing with rows returned by a function or a database package, for example. `bind_rows` can handle that situation as well:


``` r
# Same toy data, but rows stored in lists
l1 <- list(a = 1, b = 2, c = "X")
l2 <- list(a = 3, b = 4, c = "Y")
l3 <- list(a = 5, b = 6, c = "Z")
list.of.lists <- list(l1, l2, l3)

bind_rows(list.of.lists)
#> # A tibble: 3 x 3
#>       a     b c    
#>   <dbl> <dbl> <chr>
#> 1     1     2 X    
#> 2     3     4 Y    
#> 3     5     6 Z
```

#### Factors in data frames {-}

If you would rather get characters instead of factors, you have a couple of options.
One is to set the `stringsAsFactors` parameter to `FALSE` when `data.frame` is called:


``` r
data.frame(a = 1, b = 2, c = "a", stringsAsFactors = FALSE)
#>   a b c
#> 1 1 2 a
```

Of course, if you inherited your data and it's already in a data frame with factors,
you can convert all factors in a `data.frame` to characters using this bonus recipe:


``` r
## same set up as in the previous examples
l1 <- list( a=1, b=2, c='X' )
l2 <- list( a=3, b=4, c='Y' )
l3 <- list( a=5, b=6, c='Z' )
obs <- list(l1, l2, l3)
df <- do.call(rbind, Map(as.data.frame, obs)) 

# Yes, you could use stringsAsFactors=FALSE above,
# but we're assuming the data.frame 
# came to you with factors already 

i <- sapply(df, is.factor)             ## determine which columns are factors
df[i] <- lapply(df[i], as.character)   ## turn only the factors to characters
df 
#>   a b c
#> 1 1 2 X
#> 2 3 4 Y
#> 3 5 6 Z
```

Keep in mind that if you use a `tibble` instead of a `data.frame`, then characters will not be forced into factors by default. 

### See Also {-#see_also-id252}

See Recipe \@ref(recipe-id251), ["Initializing a Data Frame from Column Data"](#recipe-id251)
if your data is organized by columns, not rows.  

Appending Rows to a Data Frame {#recipe-id071}
------------------------------ 

### Problem {-#problem-id071}

You want to append one or more new rows to a data frame.

### Solution {-#solution-id071}

Create a second, temporary data frame containing the new rows. Then use
the `rbind` function to append the temporary data frame to the original
data frame.

### Discussion {-#discussion-id071}
Suppose we have a data frame of Chicago-area suburbs.


``` r
suburbs <- read_csv("./data/suburbs.txt")
#> Rows: 17 Columns: 4
#> -- Column specification --------------------------------------------------------
#> Delimiter: ","
#> chr (3): city, county, state
#> dbl (1): pop
#> 
#> i Use `spec()` to retrieve the full column specification for this data.
#> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

Further suppose we want to append a new row.
First, we create a one-row data frame with the new data:


``` r
newRow <- data.frame(city = "West Dundee", county = "Kane",
                     state = "IL", pop = 7352)
```

Next, we use the `rbind` function to append that one-row data frame to
our existing data frame:


``` r
rbind(suburbs, newRow)
#> # A tibble: 18 x 4
#>   city    county   state     pop
#>   <chr>   <chr>    <chr>   <dbl>
#> 1 Chicago Cook     IL    2853114
#> 2 Kenosha Kenosha  WI      90352
#> 3 Aurora  Kane     IL     171782
#> 4 Elgin   Kane     IL      94487
#> 5 Gary    Lake(IN) IN     102746
#> 6 Joliet  Kendall  IL     106221
#> # i 12 more rows
```

The `rbind` function tells R that we are appending a new row to `suburbs`, not a new column.
It may be obvious to you that `newRow` is a row and not a column, but it is not obvious to R.
(Use the `cbind` function to append a column.)

One word of caution: the new row must use the same column names as the
data frame. Otherwise, `rbind` will fail.

We can combine these two steps into one, of course:


``` r
rbind(suburbs,
      data.frame(city = "West Dundee", county = "Kane",
                 state = "IL", pop = 7352))
#> # A tibble: 18 x 4
#>   city    county   state     pop
#>   <chr>   <chr>    <chr>   <dbl>
#> 1 Chicago Cook     IL    2853114
#> 2 Kenosha Kenosha  WI      90352
#> 3 Aurora  Kane     IL     171782
#> 4 Elgin   Kane     IL      94487
#> 5 Gary    Lake(IN) IN     102746
#> 6 Joliet  Kendall  IL     106221
#> # i 12 more rows
```

We can even extend this technique to multiple new rows because `rbind`
allows multiple arguments:


``` r
rbind(suburbs,
      data.frame(city = "West Dundee", county = "Kane",
                 state = "IL", pop = 7352),
      data.frame(city = "East Dundee", county = "Kane",
                 state = "IL", pop = 3192)
)
#> # A tibble: 19 x 4
#>   city    county   state     pop
#>   <chr>   <chr>    <chr>   <dbl>
#> 1 Chicago Cook     IL    2853114
#> 2 Kenosha Kenosha  WI      90352
#> 3 Aurora  Kane     IL     171782
#> 4 Elgin   Kane     IL      94487
#> 5 Gary    Lake(IN) IN     102746
#> 6 Joliet  Kendall  IL     106221
#> # i 13 more rows
```

It's worth noting that in the previous examples we seamlessly comingled tibbles and data frames. `suburbs` is a tibble because we used the tidy function `read_csv`, which produces tibbles, while `newRow` was created using `data.frame`, which returns a traditional R data frame.
And note that the data frames contain factors while the tibbles do not:


``` r
str(suburbs) # a tibble
#> spc_tbl_ [17 x 4] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
#>  $ city  : chr [1:17] "Chicago" "Kenosha" "Aurora" "Elgin" ...
#>  $ county: chr [1:17] "Cook" "Kenosha" "Kane" "Kane" ...
#>  $ state : chr [1:17] "IL" "WI" "IL" "IL" ...
#>  $ pop   : num [1:17] 2853114 90352 171782 94487 102746 ...
#>  - attr(*, "spec")=
#>   .. cols(
#>   ..   city = col_character(),
#>   ..   county = col_character(),
#>   ..   state = col_character(),
#>   ..   pop = col_double()
#>   .. )
#>  - attr(*, "problems")=<externalptr>
str(newRow)  # a data.frame
#> 'data.frame':	1 obs. of  4 variables:
#>  $ city  : chr "West Dundee"
#>  $ county: chr "Kane"
#>  $ state : chr "IL"
#>  $ pop   : num 7352
```

When the inputs to `rbind` are a mix of `data.frame` objects and `tibble` objects,
the result will have the same type as the *first* argument of `rbind`.
So this would produce a tibble:


``` r
rbind(some_tibble, some_data.frame)
```

while this would produce a data frame:


``` r
rbind(some_data.frame, some_tibble)
```

Selecting Data Frame Columns by Position {#recipe-id068}
--------------------------------------------------------

### Problem {-#problem-id068}

You want to select columns from a data frame according to their position.

### Solution {-#solution-id068}

Use the `select` function:

`df %>% select(*n*~1~, *n*~2~, ..., *n*~k~)`

where `df` is a data frame
and `*n*~1~`, `*n*~2~`, ..., `*n~k~*` are integers with values between 1 and the number of columns.

### Discussion {-#discussion-id068}

Let’s use the first three rows of the dataset of population data
for the 16 largest cities in the Chicago metropolitan area:


``` r
suburbs <- read_csv("data/suburbs.txt") %>% head(3)
#> Rows: 17 Columns: 4
#> -- Column specification --------------------------------------------------------
#> Delimiter: ","
#> chr (3): city, county, state
#> dbl (1): pop
#> 
#> i Use `spec()` to retrieve the full column specification for this data.
#> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
suburbs
#> # A tibble: 3 x 4
#>   city    county  state     pop
#>   <chr>   <chr>   <chr>   <dbl>
#> 1 Chicago Cook    IL    2853114
#> 2 Kenosha Kenosha WI      90352
#> 3 Aurora  Kane    IL     171782
```

Right off the bat, we can see this is a tibble.
This will extract the first column (and only the first column):


``` r
suburbs %>%
  dplyr::select(1)
#> # A tibble: 3 x 1
#>   city   
#>   <chr>  
#> 1 Chicago
#> 2 Kenosha
#> 3 Aurora
```

These will extract  multiple columns:


``` r
suburbs %>%
  dplyr::select(1, 3, 4)
#> # A tibble: 3 x 3
#>   city    state     pop
#>   <chr>   <chr>   <dbl>
#> 1 Chicago IL    2853114
#> 2 Kenosha WI      90352
#> 3 Aurora  IL     171782
suburbs %>%
  dplyr::select(2:4)
#> # A tibble: 3 x 3
#>   county  state     pop
#>   <chr>   <chr>   <dbl>
#> 1 Cook    IL    2853114
#> 2 Kenosha WI      90352
#> 3 Kane    IL     171782
```

#### List expressions {-}

The `select` verb is part of the tidyverse package `dplyr`.
Base R also has its own rich functionality for selecting columns, at the cost of some additional syntax.
The choices can be confusing until you understand the logic behind the alternatives.

One alternative uses list expressions.
This might seem odd until you recall that a data frame is a list of columns.
The list expression selects columns from that list.
As you read this explanation,
notice how the change in syntax—double brackets versus single brackets—changes the
meaning of the expression.

We can select exactly one column by using double brackets (`[[` and `]]`):

`df[[*n*]]`

:   Returns a *vector*—specifically, the vector in the *n*th column of `df`.

We can select one or more columns by using single brackets (`[` and `]`).

`df[*n*]`

:   Returns a *data frame* consisting solely of the *n*th column of `df`.

`df[c(*n*~1~, *n*~2~, ..., *n*~k~)]`

:   Returns a *data frame* built from the columns in positions *n*~1~,
    *n*~2~, ..., *n*~k~ of `df`.

For example,
we can use list notation to select the first column from `suburbs`,
the `city` column:


``` r
suburbs[[1]]
#> [1] "Chicago" "Kenosha" "Aurora"
```

That column is a character vector, so that’s what `suburbs[[1]]` returns: a vector.

The result changes when we use the single-bracket notation,
as in `suburbs[1]` or `suburbs[c(1,3)]`.
We still get the requested columns, but R leaves them in a data frame.
This example returns the first column as a one-column data frame:


``` r
suburbs[1]
#> # A tibble: 3 x 1
#>   city   
#>   <chr>  
#> 1 Chicago
#> 2 Kenosha
#> 3 Aurora
```

And this example returns the first and third columns as a data frame:


``` r
suburbs[c(1, 3)]
#> # A tibble: 3 x 2
#>   city    state
#>   <chr>   <chr>
#> 1 Chicago IL   
#> 2 Kenosha WI   
#> 3 Aurora  IL
```

(The expression `suburbs[1]` is actually a shortened form of `suburbs[c(1)]`.
We don’t need the `c(...)` wrapper because there is only one *n*.)

A major source of confusion is that `suburbs[[1]]` and `suburbs[1]` look
similar but produce very different results:

`suburbs[[1]]`

:   Returns one column.

`suburbs[1]`

:   Returns a data frame that contains contains exactly one column.

The point here is that "one column" is different from "a data frame that contains one column."
The first expression returns a vector. The second expression returns a data frame, which is a different data structure.

#### Matrix-style subscripting {-}

You can use matrix-style subscripting to select columns from a data frame:

`df[, *n*]`

:   Returns a *vector* taken from the *n*th column
    (assuming that `*n*` contains exactly one value).

`df[, c(*n*~1~, *n*~2~, ..., *n*~k~)]`

:   Returns a *data frame* built from the columns in positions *n*~1~,
    *n*~2~, ..., *n~k~*.

An odd quirk can bite you here: you might get a column vector or you
might get a data frame, depending upon how many subscripts you use and whether you are operating on a tibble or a `data.frame`. tibbles will always return tibbles when you index. However a `data.frame` may return a vector if you use one index. 

In the simple case of one index on a `data.frame` you get a vector, like this:


``` r
# suburbs is a tibble so we convert for this example
suburbs_df <- as.data.frame(suburbs)
suburbs_df[, 1]
#> [1] "Chicago" "Kenosha" "Aurora"
```

But using the same matrix-style syntax with multiple indexes returns a
data frame:


``` r
suburbs_df[, c(1, 4)]
#>      city     pop
#> 1 Chicago 2853114
#> 2 Kenosha   90352
#> 3  Aurora  171782
```

This creates a problem.
Suppose you see this expression in some old R code:


``` r
df[, vec]
```

Quick, does that return a column or a data frame?
Well, it depends.
If `vec` contains one value, then you get a column;
otherwise, you get a data frame.
You cannot tell from the syntax alone.

To avoid this problem, you can include `drop=FALSE` in the subscripts,
forcing R to return a data frame:


``` r
df[, vec, drop = FALSE]
```

Now there is no ambiguity about the returned data structure.
It’s a data frame.

When all is said and done, using matrix notation to select columns from data frames can be tricky.
Use `select` when you can.

### See Also {-#see_also-id068}

See Recipe \@ref(recipe-id250), ["Selecting One Row or Column from a Matrix"](#recipe-id250), for more
about using `drop=FALSE`.

Selecting Data Frame Columns by Name {#recipe-id254}
------------------------------------

### Problem {-#problem-id254}

You want to select columns from a data frame according to their name.

### Solution {-#solution-id254}
Use `select` and give it the column names.

`df %>% select(*name*~1~, *name*~2~, ..., *name*~k~)`

### Discussion {-#discussion-id254}

All columns in a data frame must have names.
If you know the name, it’s usually more convenient and readable to select by name, not by position.
Note that you don't put the column names in quotes when using `select`.

The solutions just described are similar to those for Recipe \@ref(recipe-id068), ["Selecting Data Frame Columns by Position"](#recipe-id068),
where we selected columns by position.
The only difference is that here we use column names instead of column numbers.
All the observations made in that recipe apply here. 

#### List expressions {-}

The `select` verb is part of the tidyverse.
Base R itself also has several rich methods for selecting columns by name,
at the cost of some additional syntax.

To select a single column, use one of these list expressions.
Note that they use *double* brackets (`[[` and `]]`).

`df[["*name*"]]`

:   Returns *one column*, the column called `*name*`.

`df$*name*`

:   Same as previous, just different syntax.

To select one or more columns, use these list expressions.
Note that they use *single* brackets (`[` and `]`).

`df["*name*"]`

:   Selects one column from a data frame.

`df[c("*name*~1~", "*name*~2~", ..., "*name*~k~")]`

:   Selects several columns.

#### Matrix-style subscripting {-}

Base R also allows matrix-style subscripting
for selecting one or more columns from a data frame
by name:

`df[, "*name*"]`

:   Returns the named column.

`df[, c("*name*~1~", "*name*~2~", ..., "*name*~k~")]`

:   Selects several columns in a data frame.

The matrix-style subscripting can return either a column or a data frame, so be careful how many names you supply.
See the comments in Recipe \@ref(recipe-id068), ["Selecting Data Frame Columns by Position"](#recipe-id068) for a
discussion of this “gotcha” and using `drop=FALSE`.

### See Also {-#see_also-id254}

See Recipe \@ref(recipe-id068), ["Selecting Data Frame Columns by Position"](#recipe-id068),
to select by position instead of name.

Changing the Names of Data Frame Columns {#recipe-id069}
----------------------------------------

### Problem {-#problem-id069}
You want to change the names of a data frame's columns.

### Solution {-#solution-id069}

The `rename` function from the `dplyr` package makes renaming pretty easy:

`df %>% rename(*newname*~1~ = *oldname*~1~, . . . , *newname*~n~ = *oldname*~n~)`

where `df` is a data frame,
`*oldname*~i~` are names of columns in `df`, and
`*newname*~i~` are the desired new names.

Note that the argument order is `*newname* = *oldname*`. 

### Discussion {-#discussion-id069}

The columns of data frames must have names.
You can change them using `rename`:


``` r
df <- data.frame(V1 = 1:3, V2 = 4:6, V3 = 7:9)
df %>% rename(tom = V1, dick = V2)
#>   tom dick V3
#> 1   1    4  7
#> 2   2    5  8
#> 3   3    6  9
```

The column names are stored in an attribute called `colnames`,
so another way to rename columns is to change that attribute.


``` r
colnames(df) <- c("tom", "dick", "V2")
df
#>   tom dick V2
#> 1   1    4  7
#> 2   2    5  8
#> 3   3    6  9
```

If you happen to be using `select` to select individual columns,
you can rename those columns at the same time:


``` r
df <- data.frame(V1 = 1:3, V2 = 4:6, V3 = 7:9)
df %>% select(tom = V1, V2)
#>   tom V2
#> 1   1  4
#> 2   2  5
#> 3   3  6
```

The difference between renaming with `select` versus renaming with `rename` is that
`rename` will rename what you specify, leaving all other columns intact and unchanged,
whereas `select` keeps only the columns you select.
In the preceding example, `V3` is dropped because it's not in the `select` statement.
Both `select` and `rename` use the same argument order: `*newname* = *oldname*`.

### See Also {-#see_also-id069}

See Recipe \@ref(recipe-id074), ["Converting One Structured Data Type into Another"](#recipe-id074).


Removing NAs from a Data Frame {#recipe-id249}
------------------------------

### Problem {-#problem-id249}

Your data frame contains `NA` values, which is creating problems for you.

### Solution {-#solution-id249}

Use `na.omit` to remove rows that contain any `NA` values.


``` r
clean_dfrm <- na.omit(dfrm)
```

### Discussion {-#discussion-id249}

We frequently stumble upon situations where just a few `NA` values in a
data frame cause everything to fall apart. One solution is simply to
remove all rows that contain any `NA`s. That’s what `na.omit` does.

Consider a data frame with embedded `NA` values:


``` r
df <- data.frame(
  x = c(1, NA, 3, 4, 5),
  y = c(1, 2, NA, 4, 5)
)
df
#>    x  y
#> 1  1  1
#> 2 NA  2
#> 3  3 NA
#> 4  4  4
#> 5  5  5
```

The `cumsum` function should calculate cumulative sums,
but it stumbles on the `NA` values:


``` r
colSums(df)
#>  x  y 
#> NA NA
```

If we remove rows with `NA` values, `cumsum` can complete its summations:


``` r
cumsum(na.omit(df))
#>    x  y
#> 1  1  1
#> 4  5  5
#> 5 10 10
```

But watch out! The `na.omit` function removes *entire* rows.
The non-`NA` values in those rows disappear, changing the meaning of "cumulative sum."

This recipe works for removing `NA` from vectors and matrices, too,
but not lists.

The obvious danger here is that simply dropping observations from your
data could render the results numerically or statistically
meaningless. Make sure that omitting data makes sense in your context.
Remember that `na.omit` will remove entire rows, not just the `NA` values,
which could eliminate useful information.

Excluding Columns by Name {#recipe-id072}
-------------------------

### Problem {-#problem-id072}

You want to exclude a column from a data frame using its name.

### Solution {-#solution-id072}

Use the `select` function from the `dplyr` package with a dash (minus sign) in front of the name of the column to exclude:


``` r
select(df, -bad)   # Select all columns from df except bad
```

### Discussion {-#discussion-id072}
Placing a minus sign in front of a varible name tells the `select` function to drop that variable. 

This comes in handy when we're calculating a correlation matrix from a data frame,
and we want to exclude the nondata columns such as labels.




``` r
cor(patient_data)
#>            patient_id    pre  dosage   post
#> patient_id     1.0000  0.159 -0.0486  0.391
#> pre            0.1590  1.000  0.8104 -0.289
#> dosage        -0.0486  0.810  1.0000 -0.526
#> post           0.3912 -0.289 -0.5262  1.000
```

This correlation matrix includes the meaningless “correlation” between
`patient_id` and other variables, which is annoying.
We can exclude the `patient_id` column to clean up the output:


``` r
patient_data %>%
  select(-patient_id) %>%
  cor
#>           pre dosage   post
#> pre     1.000  0.810 -0.289
#> dosage  0.810  1.000 -0.526
#> post   -0.289 -0.526  1.000
```

We can exclude multiple columns the same way:


``` r
patient_data %>%
  select(-patient_id, -dosage) %>%
  cor()
#>         pre   post
#> pre   1.000 -0.289
#> post -0.289  1.000
```

Combining Two Data Frames {#recipe-id155}
-------------------------

### Problem {-#problem-id155}

You want to combine the contents of two data frames into one data frame.

### Solution {-#solution-id155}

To combine the columns of two data frames side by side, use `cbind` (column bind):

```
all.cols <- cbind(df1, df2)
```

To “stack” the rows of two data frames, use `rbind` (row bind):

```
all.rows <- rbind(df1, df2)
```

### Discussion {-#discussion-id155}
You can combine data frames in one of two ways:
either by putting the columns side by side to create a wider data frame,
or by “stacking” the rows to create a taller data frame.

The `cbind` function will combine data frames side by side.


``` r
df1 <- data.frame(a = c(1,2))
df2 <- data.frame(b = c(7,8))

cbind(df1, df2)
#>   a b
#> 1 1 7
#> 2 2 8
```

You would normally combine columns with the same height (number of rows).
Technically speaking, however, `cbind` does not require matching heights.
If one data frame is short, it will invoke the Recycling Rule
to extend the short columns as necessary (see Recipe \@ref(recipe-id050), ["Understanding the Recycling Rule"](#recipe-id050)),
which may or may not be what you want.

The `rbind` function will “stack” the rows of two data frames.


``` r
df1 <- data.frame(x = c("a", "a"), y = c(5, 6))
df2 <- data.frame(x = c("b", "b"), y = c(9, 10))
rbind(df1, df2)
#>   x  y
#> 1 a  5
#> 2 a  6
#> 3 b  9
#> 4 b 10
```

The `rbind` function requires that the data frames have the same width—the same number of columns and same column names.
The columns need not be in the same *order*, however;
`rbind` will sort that out.

Finally, this recipe is slightly more general than the title implies.
First, you can combine more than two data frames because both `rbind`
and `cbind` accept multiple arguments. Second, you can apply this recipe
to other data types because `rbind` and `cbind` work also with vectors,
lists, and matrices.

Merging Data Frames by Common Column {#recipe-id057}
----------------------------------------------------

### Problem {-#problem-id057}
You have two data frames that share a common column.
You want to merge or join their rows into one data frame by matching on the common column.

### Solution {-#solution-id057}
We can use the `join` functions from the `dplyr` package to join our data frames together on a common column. 

If you want only rows that appear only in *both* data frames,
use `inner_join`.


``` r
inner_join(df1, df2, by = "col")
```

where `"col"` is the column that appears in both data frames.

If you want all rows that appear in *either* data frame,
use `full_join` instead.


``` r
full_join(df1, df2, by = "col")
```

If you want all rows from `df1` and only those from `df2` that match, use `left_join`:

``` r
left_join(df1, df2, by = "col")
```

Or to get all records from `df2` and only the matching ones from `df1`, use `right_join`:

``` r
right_join(df1, df2, by = "col")
```



### Discussion {-#discussion-id057}
Suppose you have two data frames, `born` and `died`, that each contain a
column called `name`:


``` r
born <- tibble(
  name = c("Moe", "Larry", "Curly", "Harry"),
  year.born = c(1887, 1902, 1903, 1964),
  place.born = c("Bensonhurst", "Philadelphia", "Brooklyn", "Moscow")
)

died <- tibble(
  name = c("Curly", "Moe", "Larry"),
  year.died = c(1952, 1975, 1975)
)
```

We can merge them into one data frame by using `name` to combine matched rows:


``` r
inner_join(born, died, by="name")
#> # A tibble: 3 x 4
#>   name  year.born place.born   year.died
#>   <chr>     <dbl> <chr>            <dbl>
#> 1 Moe        1887 Bensonhurst       1975
#> 2 Larry      1902 Philadelphia      1975
#> 3 Curly      1903 Brooklyn          1952
```

Notice that `inner_join` does not require the rows to be sorted or even to
occur in the same order.
It found the matching rows for Curly even though they occur in different positions.
It also discarded the row for Harry, which appeared only in `born`.

A `full_join` of these data frames includes every row of both,
even rows with no matching values.


``` r
full_join(born, died, by="name")
#> # A tibble: 4 x 4
#>   name  year.born place.born   year.died
#>   <chr>     <dbl> <chr>            <dbl>
#> 1 Moe        1887 Bensonhurst       1975
#> 2 Larry      1902 Philadelphia      1975
#> 3 Curly      1903 Brooklyn          1952
#> 4 Harry      1964 Moscow              NA
```

Where a data frame has no matching value, its columns are filled with `NA`:
the `year.died` for Harry is `NA`.

If we don't supply the join function with a field to join by, then it will attempt to join by any field with matching names in both data frames and will return an informational response stating which field it is joining on:


``` r
full_join(born, died)
#> Joining with `by = join_by(name)`
#> # A tibble: 4 x 4
#>   name  year.born place.born   year.died
#>   <chr>     <dbl> <chr>            <dbl>
#> 1 Moe        1887 Bensonhurst       1975
#> 2 Larry      1902 Philadelphia      1975
#> 3 Curly      1903 Brooklyn          1952
#> 4 Harry      1964 Moscow              NA
```

If we want to join two data frames on a field that does not have the same name in both data frames, we need our `by` paramter to be a vector of equalities:


``` r
df1 <- data.frame(key1 = 1:3, value=2)
df2 <- data.frame(key2 = 1:3, value=3)

inner_join(df1, df2, by = c("key1" = "key2"))
#>   key1 value.x value.y
#> 1    1       2       3
#> 2    2       2       3
#> 3    3       2       3
```

Notice in the preceding example how both tables have a field named `value` that gets renamed in the output. The field from the first table becomes `value.x`, while the field from the second table becomes `value.y`. `dplyr` joins will always rename output this way when there is a naming clash on columns not being joined on. 

### See Also {-#see_also-id057}
See Recipe \@ref(recipe-id155), ["Combining Two Data Frames"](#recipe-id155), for other ways to combine
data frames.

The examples joined on a single column, `name`, but these functions can join on multiple columns, too.
For details, see the function documentation by typing `**?dplyr::join**`.

These join operations were inspired by SQL.
Just like in SQL, there are multiple types of joins in `dplyr`, including inner, left, right, full, semi, and anti.
Again, see the function documentation.


Converting One Atomic Value into Another {#recipe-id248}
----------------------------------------

### Problem {-#problem-id248}

You have a data value that has an atomic data type: character, complex,
double, integer, or logical. You want to convert this value into one of
the other atomic data types.

### Solution {-#solution-id248}

For each atomic data type, there is a function for converting values to
that type. The conversion functions for atomic types include:

-   `as.character(x)`

-   `as.complex(x)`

-   `as.numeric(x)` or `as.double(x)`

-   `as.integer(x)`

-   `as.logical(x)`

### Discussion {-#discussion-id248}

Converting one atomic type into another is usually pretty simple. If the
conversion works, you get what you would expect. If it does not work,
you get `NA`:


``` r
as.numeric(" 3.14 ")
#> [1] 3.14
as.integer(3.14)
#> [1] 3
as.numeric("foo")
#> Warning: NAs introduced by coercion
#> [1] NA
as.character(101)
#> [1] "101"
```

If you have a vector of atomic types, these functions apply themselves
to every value. So the preceding examples of converting scalars
generalize easily to converting entire vectors:


``` r
as.numeric(c("1", "2.718", "7.389", "20.086"))
#> [1]  1.00  2.72  7.39 20.09
as.numeric(c("1", "2.718", "7.389", "20.086", "etc."))
#> Warning: NAs introduced by coercion
#> [1]  1.00  2.72  7.39 20.09    NA
as.character(101:105)
#> [1] "101" "102" "103" "104" "105"
```

When converting logical values into numeric values, R converts `FALSE`
to `0` and `TRUE` to `1`:


``` r
as.numeric(FALSE)
#> [1] 0
as.numeric(TRUE)
#> [1] 1
```

This behavior is useful when you are counting occurrences of `TRUE` in
vectors of logical values. If `logvec` is a vector of logical values,
then `sum(logvec)` does an implicit conversion from logical to integer
and returns the number of `TRUE`s:


``` r
logvec <- c(TRUE, FALSE, TRUE, TRUE, TRUE, FALSE)
sum(logvec) ## num true
#> [1] 4
length(logvec) - sum(logvec) ## num not true
#> [1] 2
```
Converting One Structured Data Type into Another {#recipe-id074}
------------------------------------------------

### Problem {-#problem-id074}

You want to convert a variable from one structured data type to
another—for example, converting a vector into a list, or a matrix into a
data frame.

### Solution {-#solution-id074}

These functions convert their argument into the corresponding structured
data type:

-   `as.data.frame(x)`

-   `as.list(x)`

-   `as.matrix(x)`

-   `as.vector(x)`

Some of these conversions may surprise you, however. We suggest you review Table \@ref(tab:tbl-conversions) for more detail.

### Discussion {-#discussion-id074}

Converting between structured data types can be tricky. Some conversions
behave as you’d expect. If you convert a matrix into a data frame, for
instance, the rows and columns of the matrix become the rows and columns
of the data frame. No sweat.

------------------------------------------------------------------------------------------------------
Conversion         How                                                 Notes
------------------ --------------------------------------------------- -------------------------------
Vector→List        `as.list(vec)`                                      Don’t use `list(vec)`;  that 
                                                                       creates a one-element list whose 
                                                                       only element is a copy of `vec`.

Vector→Matrix      To create a one-column matrix:
                   `cbind(vec)` *or* `as.matrix(vec)`                   See Recipe \@ref(recipe-id062).
                   To create a one-row matrix: `rbind(vec)`
                   To create an *n* × *m* matrix: `matrix(vec,n,m)`

Vector→Data frame  To create a one-column data frame: 
                   `as.data.frame(vec)`             
                   To create a one-row data frame: 
                   `as.data.frame(rbind(vec))`

List→Vector        `unlist(lst)`                                       Use `unlist` rather than `as.vector`;
                                                                       see Note 1 and Recipe \@ref(recipe-id152).

List→Matrix        To create a one-column matrix: `as.matrix(lst)`                     
                   To create a one-row matrix: `as.matrix(rbind(lst))`
                   To create an *n* × *m* matrix: `matrix(lst,n,m)`

List→Data frame    If the list elements are columns of data: 
                   `as.data.frame(lst)`    
                   If the list elements are rows of data, see
                   Recipe \@ref(recipe-id252).

Matrix→Vector      `as.vector(mat)`                                    Returns all matrix elements in a vector.

Matrix→List        `as.list(mat)`                                      Returns all matrix elements in a list.

Matrix→Data frame  `as.data.frame(mat)`

Data frame→Vector  To convert a one-row data frame: `df[1,]`             See Note 2.
                   To convert a one-column data frame: 
                   `df[,1]` or `df[[1]]`

Data frame→List    `as.list(df)`                                       See Note 3.

Data frame→Matrix  `as.matrix(df)`                                     See Note 4.
------------------------------------------------------------------------------------------------------

Table: (\#tab:tbl-conversions) Data conversions

In other cases, the results might surprise you.
Table \@ref(tab:tbl-conversions) summarizes some noteworthy examples.
The following Notes are cited in that table:

1.  When you convert a list into a vector, the conversion works cleanly
    if your list contains atomic values that are all of the same mode.
    Things become complicated if either (a) your list contains mixed
    modes (e.g., numeric and character), in which case everything is
    converted to characters; or (b) your list contains other structured
    data types, such as sublists or data frames—in which case very odd
    things happen, so don’t do that.

2.  Converting a data frame into a vector makes sense only if the data
    frame contains one row or one column. To extract all its elements
    into one long vector, use `as.vector(as.matrix(df))`. But even
    that makes sense only if the data frame is all numeric or
    all character; if not, everything is first converted to
    character strings.

3.  Converting a data frame into a list may seem odd in that a data
    frame is already a list (i.e., a list of columns). Using `as.list`
    essentially removes the class (`data.frame`) and thereby exposes the
    underlying list. That is useful when you want R to treat your data
    structure as a list—say, for printing.

4.  Be careful when converting a data frame into a matrix. If the data
    frame contains only numeric values, then you get a numeric matrix. If
    it contains only character values, you get a character matrix. But
    if the data frame is a mix of numbers, characters, and/or factors,
    then all values are first converted to characters. The result is a
    matrix of character strings.

#### Special considerations for matrices {-}

The matrix conversions detailed here assume that your matrix is
homogeneous—that is, all elements have the same mode (e.g., all numeric or all
character). A matrix can to be heterogeneous, too, when the matrix is
built from a list. If so, conversions become messy. For example, when
you convert a mixed-mode matrix to a data frame, the data frame’s
columns are actually lists (to accommodate the mixed data).

### See Also {-#see_also-id074}

See Recipe \@ref(recipe-id248), ["Converting One Atomic Value into Another"](#recipe-id248), for
converting atomic data types; see the
[Introduction](#intro-DataStructures) to this chapter for remarks on
problematic conversions.

[^dataframe]: A data frame can be built from a mixture of vectors, factors, and
    matrices. The columns of the matrices become columns in the data
    frame. The number of *rows* in each matrix must match the *length*
    of the vectors and factors. In other words, all elements of a data
    frame must have the same *height*.

