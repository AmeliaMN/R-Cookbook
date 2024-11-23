
# Data Transformations {#DataTransformations}

Introduction {-#intro-DataTransformations}
------------

While traditional programming languages use loops, R has traditionally encouraged using
vectorized operations and the `apply` family of functions to crunch data in batches,
greatly streamlining the calculations. There is nothing to prevent you from writing loops in R that break your data into whatever chunks you want and then do an operation on each chunk. However, using vectorized functions can, in many cases, increase the speed, readability, and maintainability of your code. 

In recent history, however, the tidyverse, specifically the `purrr` and `dplyr` packages, has introduced new idioms into R that make these concepts easier to learn and slightly more consistent. The name `purrr` comes from a play on the phrase "Pure R." A "pure function" is a function where the result of the function is determined only by its inputs, and which does not produce any side effects. This is not a functional programming concept you need to understand in order to get great value from `purrr`, however. All most users need to know is that `purrr` contains functions to help us operate "chunk by chunk" on our data in a way that meshes well with other tidyverse packages such as `dplyr`. 

Base R has many apply functions—`apply`, `lapply`, `sapply`, `tapply`, and `mapply`—and their cousins, `by` and `split`. These are solid functions that have been workhorses in Base R for years. The authors have struggled a bit with how much to focus on the Base R apply functions and how much to focus on the newer "tidy" approach. After much debate we've chosen to try to illustrate the `purrr` approach and to acknowledge Base R approaches and, in a few places, to illustrate both. The interface to `purrr` and `dplyr` is very clean and, we believe, in most cases, more intuitive. 

Applying a Function to Each List Element {#recipe-id149}
----------------------------------------

### Problem {-#problem-id149}

You have a list, and you want to apply a function to each element of the
list.

### Solution {-#solution-id149}

We can use `map` to apply the function to every element of a list:


``` r
library(tidyverse)

lst %>%
  map(fun)
```

### Discussion {-#discussion-id149}

Let's look at a specific example of taking the average of all the numbers in each element of a list:


``` r
library(tidyverse)

lst <- list(
  a = c(1,2,3),
  b = c(4,5,6)
)
lst %>%
  map(mean)
#> $a
#> [1] 2
#> 
#> $b
#> [1] 5
```

The `map` function will call your function once for every element on your list. Your function should expect one
argument, an element from the list. The `map` functions
will collect the returned values and return them in a list.

The `purrr` package contains a whole family of `map` functions that take a list or a vector and then return an object with the same number of elements as the input.
The type of object they return varies based on which `map` function is used. See the help file for `map` for a complete list, but a few of the most common are as follows:

map()
: Always returns a list, and the elements of the list may be of different types. This is quite similar to the Base R function `lapply`.

map_chr()
: Returns a character vector.

map_int()
: Returns an integer vector.

map_dbl()
: Returns a floating-point numeric vector.

Let’s take a quick look at a contrived situation where we have a function that could result in a character or an integer result:


``` r
fun <- function(x) {
  if (x > 1) {
    1
  } else {
    "Less Than 1"
  }
}

fun(5)
#> [1] 1
fun(0.5)
#> [1] "Less Than 1"
```
Let's create a list of elements that we can map `fun` to and look at how some of the `map` variants behave:


``` r
lst <- list(.5, 1.5, .9, 2)

map(lst, fun)
#> [[1]]
#> [1] "Less Than 1"
#> 
#> [[2]]
#> [1] 1
#> 
#> [[3]]
#> [1] "Less Than 1"
#> 
#> [[4]]
#> [1] 1
```
You can see that `map` produced a list and it is of mixed data types. 

And `map_chr` will produce a character vector and coerce the numbers into characters:

``` r
map_chr(lst, fun)
#> [1] "Less Than 1" "1.000000"    "Less Than 1" "1.000000"

## or using pipes
lst %>%
  map_chr(fun)
#> [1] "Less Than 1" "1.000000"    "Less Than 1" "1.000000"
```

while `map_dbl` will try to coerce a character string into a double and die trying. 

``` r
map_dbl(lst, fun)
#> Error in `map_dbl()`:
#> i In index: 1.
#> Caused by error:
#> ! Can't coerce from a string to a double.
```

As mentioned earlier, the Base R `lapply` function acts very much like `map`. The Base R `sapply` function is more like the other `map` functions we discussed previously in that the function tries to
simplify the results into a vector or matrix. 

### See Also {-#see_also-id149}

See Recipe \@ref(recipe-id117), ["Defining a Function"](#recipe-id117).

Applying a Function to Every Row of a Data Frame {#recipe-row_df}
------------------

### Problem {-#problem-row_df}
You have a function and you want to apply it to every row in a data frame. 

### Solution {-#solution-row_df}

The `mutate` function will create a new variable based on a vector of values. But if we are using a function that can't take in a vector and output a vector, then we have to do a row by row operation using `rowwise`. 

We can use `rowwise` in a pipe chain to tell `dplyr` to do all following commands row-by-row:


``` r
df %>%
  rowwise() %>%
  row_by_row_function()

```

### Discussion {-#discussion-row_df}

Let's create a function and apply it row by row to a data frame:

Our function will simply calculate the sum of a sequence from `a` to `b` by `c`:

``` r
fun <- function(a, b, c) {
  sum(seq(a, b, c))
}
```

Let's create some data to apply this function to, then use `rowwise` to apply our function, `fun`, to it:

``` r
df <- data.frame(mn = c(1, 2, 3),
                 mx = c(8, 13, 18),
                 rng = c(1, 2, 3))

df %>%
  rowwise %>%
  mutate(output = fun(a = mn, b = mx, c = rng))
#> # A tibble: 3 x 4
#> # Rowwise: 
#>      mn    mx   rng output
#>   <dbl> <dbl> <dbl>  <dbl>
#> 1     1     8     1     36
#> 2     2    13     2     42
#> 3     3    18     3     63
```

Had we tried to run this function without `rowwise`, it would have thrown an error because the `seq` function cannot process an entire vector:


``` r
df %>%
  mutate(output = fun(a = mn, b = mx, c = rng))
#> Error in `mutate()`:
#> i In argument: `output = fun(a = mn, b = mx, c = rng)`.
#> Caused by error in `seq.default()`:
#> ! 'from' must be of length 1
```


Applying a Function to Every Row of a Matrix {#recipe-id146}
--------------------------------

### Problem {-#problem-id146}

You have a matrix. You want to apply a function to every row,
calculating the function result for each row.

### Solution {-#solution-id146}

Use the `apply` function. Set the second argument to `1` to indicate
row-by-row application of a function:


``` r
results <- apply(mat, 1, fun)    # mat is a matrix, fun is a function
```

The `apply` function will call `fun` once for each row of the matrix,
assemble the returned values into a vector, and then return that vector.

### Discussion {-#discussion-id146}

You may notice that we show only the use of the Base R `apply` function here, while other recipes illustrate `purrr` alternatives.
As of this writing, matrix operations are out of scope for `purrr`, so we use the very solid Base R `apply` function. If you really like the `purrr` syntax, you can use those functions if you first convert your marix to a data frame or tibble. But if your matrix is large, you will notice a meaningful runtime slowdown using `purrr`. 

Suppose your matrix `long` is longitudinal data, so each row contains data
for one subject and the columns contain the repeated observations over time:


``` r
long <- matrix(1:15, 3, 5)
long
#>      [,1] [,2] [,3] [,4] [,5]
#> [1,]    1    4    7   10   13
#> [2,]    2    5    8   11   14
#> [3,]    3    6    9   12   15
```

You could calculate the average observation for each subject by applying
the `mean` function to each row. The result is a vector:


``` r
apply(long, 1, mean)
#> [1] 7 8 9
```

If your matrix has row names, `apply` uses them to identify the
elements of the resulting vector, which is handy.


``` r
rownames(long) <- c("Moe", "Larry", "Curly")
apply(long, 1, mean)
#>   Moe Larry Curly 
#>     7     8     9
```

The function being called should expect
one argument, a vector, which will be one row from the matrix. The
function can return a scalar or a vector. In the vector case, `apply`
assembles the results into a matrix. The `range` function returns a
vector of two elements, the minimum and the maximum, so applying it to
`long` produces a matrix:


``` r
apply(long, 1, range)
#>      Moe Larry Curly
#> [1,]   1     2     3
#> [2,]  13    14    15
```

You can employ this recipe on data frames as well. It works if the data
frame is homogeneous—that is, either all numbers or all character strings.
When the data frame has columns of different types, extracting vectors from
the rows isn’t sensible because vectors must be homogeneous.

Applying a Function to Every Column {#recipe-id157}
-----------------------------------

### Problem {-#problem-id157}

You have a matrix or data frame, and you want to apply a function to
every column.

### Solution {-#solution-id157}

For a matrix, use the `apply` function. Set the second argument to `2`,
which indicates column-by-column application of the function. So if our matrix or data frame was named `mat` and we wanted to apply a function named `fun` to every column, it would look like this:


``` r
apply(mat, 2, fun) 
```

For a data frame, use the `map_df` function from `purrr`:

``` r
df2 <- map_df(df, fun) 
```


### Discussion {-#discussion-id157}

Let's look at an example with real numbers and apply the `mean` function to every column of a matrix:

``` r
mat <- matrix(c(1, 3, 2, 5, 4, 6), 2, 3)
colnames(mat) <- c("t1", "t2", "t3")
mat
#>      t1 t2 t3
#> [1,]  1  2  4
#> [2,]  3  5  6

apply(mat, 2, mean)  # Compute the mean of every column
#>  t1  t2  t3 
#> 2.0 3.5 5.0
```

In Base R, the `apply` function is intended for processing a matrix or data frame. The second argument of `apply` determines the direction:

-   `1` means process row by row.

-   `2` means process column by column.

This is more mnemonic than it looks. We speak of matrices in “rows and
columns,” so rows are first and columns second: 1 and 2, respectively.

A data frame is a more complicated data structure than a matrix, so
there are more options. You can simply use `apply`, in which case R will
convert your data frame to a matrix and then apply your function. That
will work if your data frame contains only one type of data but will probably not do what you want if some columns are numeric and some are character. In that
case, R will force all columns to have identical types, likely
performing an unwanted conversion as a result.

Fortunately, there are multiple alternatives. Recall that a data frame is a kind
of list: it is a list of the columns of the data frame. `purrr` has a whole family of `map` functions that return different types of objects. Of particular interest here is `map_df`, which returns a `data.frame`, thus the `df` in the name. 


``` r
df2 <- map_df(df, fun) # Returns a data.frame
```

The function `fun` should expect one argument: a column from the data
frame.

Here is a common recipe to check the types of columns in data frames. In this example, the `batch` column of this data frame, at quick glance, seems to contain numbers:


``` r
load("./data/batches.rdata")
head(batches)
#>   batch clinic dosage shrinkage
#> 1     3     KY     IL    -0.307
#> 2     3     IL     IL    -1.781
#> 3     1     KY     IL    -0.172
#> 4     3     KY     IL     1.215
#> 5     2     IL     IL     1.895
#> 6     2     NJ     IL    -0.430
```

But using `map_df` to print out the class of each column reveals the column `batch` to be a factor instead:

``` r
map_df(batches, class)
#> # A tibble: 1 x 4
#>   batch  clinic dosage shrinkage
#>   <chr>  <chr>  <chr>  <chr>    
#> 1 factor factor factor numeric
```

> Warning: Notice in this output how the third row says `<chr>` repeatedly. This is because the output of `class` is being put in a data frame and then printed. The intermediate data frame is all character fields. It's the last row that tells us our original data frame has three factor columns and one numeric field. 

### See Also {-#see_also-id157}

See Recipes \@ref(recipe-id068), ["Selecting Data Frame Columns by Position"](#recipe-id068), \@ref(recipe-id149), ["Applying a Function to Each List Element"](#recipe-id149), and  \@ref(recipe-id146), ["Applying a Function to Every Row"](#recipe-id146).


Applying a Function to Parallel Vectors or Lists {#recipe-id275}
------------------------------------------------

### Problem {-#problem-id275}

You have a function that takes multiple arguments. You want to
apply the function element-wise to vectors and obtain a vector result.
Unfortunately, the function is not vectorized; that is, it works on
scalars but not on vectors.

### Solution {-#solution-id275}

Use one of the `map` or `pmap` functions from the tidyverse core package `purrr`. The most general solution is to put your vectors in a list, then use `pmap`: 


``` r
lst <- list(v1, v2, v3)
pmap(lst, fun)
```
`pmap` will take the elements of `lst` and pass them as the inputs to `fun`. 

If you only have two vectors you are passing as inputs to your function, the `map2_*` family of functions is convenient and saves you the step of putting your vectors in a list first. `map2` will return a list, while the typed variants (`map2_chr`, `map2_dbl`, etc.) return vectors of the type their name implies:


``` r
map2(v1, v2, fun)
```
or if `fun` returns only a double, then use the typed variant of `map2`:

``` r
map2_dbl(v1, v2, fun)
```

The typed variants in `purrr` functions refer to the *output* type expected from the function. All the typed variants return vectors of their respective type, while the untyped variants return lists, which allow mixing of types. 

### Discussion {-#discussion-id275}

The basic operators of R, such as *x* + *y*, are vectorized; this means
that they compute their result element by element and return a vector of
results. Also, many R functions are vectorized.

Not all functions are vectorized, however, and those that are not typed work
only on scalars. Using vector arguments produces errors at best and
meaningless results at worst. In such cases, the `map` functions from `purrr` can
effectively vectorize the function for you.

Consider the `gcd` function from Recipe \@ref(recipe-id117), ["Defining a Function"](#recipe-id117), which takes two arguments:


``` r
gcd <- function(a, b) {
  if (b == 0) {
    return(a)
  } else {
    return(gcd(b, a %% b))
  }
}
```

If we apply `gcd` to two vectors, the result is wrong answers and a pile
of error messages:


``` r
gcd(c(1, 2, 3), c(9, 6, 3))
#> Error in if (b == 0) {: the condition has length > 1
```

The function is not vectorized, but we can use `map` to "vectorize" it.
In this case, since we have two inputs we're mapping over, we should use the `map2` function. This gives the element-wise GCDs between two vectors. 


``` r
a <- c(1, 2, 3)
b <- c(9, 6, 3)
my_gcds <- map2(a, b, gcd)
my_gcds
#> [[1]]
#> [1] 1
#> 
#> [[2]]
#> [1] 2
#> 
#> [[3]]
#> [1] 3
```
Notice that `map2` returns a list of lists. If we wanted the output in a vector, we could use `unlist` on the result, or use one of the typed variants, such as `map2_dbl`:


``` r
unlist(my_gcds)
#> [1] 1 2 3
```

The `map` family of `purrr` functions give you a series of variations that return specific types of output. The suffixes on the function names communicate the type of *vector* they will return. While `map` and `map2` return lists, since the type-specific variants are returning objects guaranteed to be the same type, they can be put in atomic vectors. For example, we could use the `map_chr` function to ask R to coerce the results into character output or `map2_dbl` to ensure the results are doubles:


``` r
map2_chr(a, b, gcd)
#> Warning: Automatic coercion from double to character was deprecated in purrr 1.0.0.
#> i Please use an explicit call to `as.character()` within `map_chr()` instead.
#> i The deprecated feature was likely used in the base package.
#>   Please report the issue to the authors.
#> This warning is displayed once every 8 hours.
#> Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
#> generated.
#> [1] "1.000000" "2.000000" "3.000000"
map2_dbl(a, b, gcd)
#> [1] 1 2 3
```

If our data has more than two vectors, or the data is already in a list, we can use the `pmap` family of functions, which take a list as an input. 


``` r
lst <- list(a,b)
pmap(lst, gcd)
#> [[1]]
#> [1] 1
#> 
#> [[2]]
#> [1] 2
#> 
#> [[3]]
#> [1] 3
```

Or if we want a typed vector as output:

``` r
lst <- list(a,b)
pmap_dbl(lst, gcd)
#> [1] 1 2 3
```

With the `purrr` functions, remember that the `pmap` family are parallel mappers that take in a *list* as inputs, while `map2` functions take two, and only two, *vectors* as inputs. 

### See Also {-#see_also-id275}
This is really just a special case of our very first recipe in this chapter, Recipe \@ref(recipe-id149), ["Applying a Function to Each List Element"](#recipe-id149). See that recipe for more discussion of `map` variants. 
In addition, Jenny Bryan has a great collection of `purrr` tutorials on her [GitHub site](https://jennybc.github.io/purrr-tutorial/).

Applying a Function to Groups of Data {#recipe-id148}
-------------------------------------

### Problem {-#problem-id148}

Your data elements occur in groups. You want to process the data by
groups—for example, summing by group or averaging by group.

### Solution {-#solution-id148}

The easiest way to do grouping is with the `dplyr` function `group_by` in conjunction with `summarize`. If our data frame is `df` and has a variable we want to group by named `grouping_var`, and we want to apply the function `fun` to all the combinations of `v1` and `v2`, we can do that with `group_by`:


``` r
df %>%
  group_by(v1, v2) %>%
  summarize(
    result_var = fun(value_var)
  )
```

### Discussion {-#discussion-id148}

Let's look at a specific example where our input data frame, `df`, contains a variable, `my_group`, which we want to group by, and a field named `values`, which we would like to calculate some statistics on:

``` r
df <- tibble(
  my_group = c("A", "B","A", "B","A", "B"),
  values = 1:6
)

df %>%
  group_by(my_group) %>%
  summarize(
    avg_values = mean(values),
    tot_values = sum(values),
    count_values = n()
  )
#> # A tibble: 2 x 4
#>   my_group avg_values tot_values count_values
#>   <chr>         <dbl>      <int>        <int>
#> 1 A                 3          9            3
#> 2 B                 4         12            3
```
The output has one record per grouping along with calculated values for the three summary fields we defined. 

If you are grouping by several variables, please be aware that `summarize` will change your grouping. Each grouping becomes a single row; at the same time, it also removes the *last* grouping variable. In other words, if you group your data by `A`, `B`, and `C` and then `summarize` it, the resulting data frame is grouped only by `A` and `B`. This is surprising but necessary. If `summarize` kept the `C` grouping, each "group" would contain exactly one row, which would be pointless.

Creating a New Column Based on Some Condition  {#recipe-casewhen}
------------------------------------------------------

### Problem {-#problem-casewhen}

You want to create a new column in a data frame based on some condition. 


### Solution {-#solution-casewhen}

Using the `dplyr` tidyverse package, we can create new data frame columns using `mutate` and then use `case_when` to implement conditional logic.


``` r
df %>%
  mutate(
    new_field = case_when(my_field == "something" ~ "result", 
                          my_field != "something else" ~ "other result", 
                          TRUE ~ "all other results")
  )
```


### Discussion {-#discussion-casewhen}

The `case_when` function from `dplyr` is analogous to `CASE WHEN` in SQL or nested `IF` statements in Excel. The function tests every element and when it finds a condition that is true, it returns the value on the right side of the `~` (tilde). 

Let's look at an example where we want to add a text field that describes a value. First let's set up some simple example data in a data frame with one column named `vals`:


``` r
df <- data.frame(vals = 1:5)
```
Now let's implement logic that creates a field called `new_vals`. If `vals` is less than or equal to 2, we'll return `2 or less`; if the value is between greater than 2 and less than or equal to 4, we'll return `2 to 4`; and otherwise we'll return `over 4`:


``` r
df %>%
  mutate(new_vals = case_when(vals <= 2 ~ "2 or less",
                              vals > 2 & vals <= 4 ~ "2 to 4",
                              TRUE ~ "over 4"))
#>   vals  new_vals
#> 1    1 2 or less
#> 2    2 2 or less
#> 3    3    2 to 4
#> 4    4    2 to 4
#> 5    5    over 4
```

You can see in the example that the condition goes on the right of the `~`, while the resulting return value goes on the right. Each condition is separated by commas. `case_when` will evaluate each condition sequentially and stop evaluating as soon as one of the criteria returns `TRUE`. Our last line is our "or else" statement. By setting the criteria to `TRUE`, no matter what, this condition will be met if no condition above it has returned `TRUE`. 

### See Also {-#see_also-casewhen}

See Recipe \@ref(recipe-row_df), ["Applying a Function to Every Row of a Data Frame"](#recipe-row_df), for more examples of using `mutate`. 


