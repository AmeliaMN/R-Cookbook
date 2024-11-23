




# Linear Regression and ANOVA {#LinearRegressionAndANOVA}

Introduction {-#intro-LinearRegressionAndANOVA}
------------

In statistics, modeling is where we get down to business. Models
quantify the relationships between our variables. Models let us make
predictions.

A simple linear regression is the most basic model. It’s just two
variables and is modeled as a linear relationship with an error term:

> *y*~*i*~ = *β*~0~ + *β*~1~*x*~*i*~ + *ε*~*i*~

We are given the data for *x* and *y*. Our mission is to fit the model,
which will give us the best estimates for *β*~0~ and *β*~1~
(see Recipe \@ref(recipe-id203), ["Performing Simple Linear Regression"](#recipe-id203)).

That generalizes naturally to multiple linear regression, where we have
multiple variables on the righthand side of the relationship
(see Recipe \@ref(recipe-id203), ["Performing Multiple Linear Regression"](#recipe-id204)):

> *y*~*i*~ = *β*~0~ + *β*~1~*u*~*i*~ + *β*~2~*v*~*i*~ + *β*~3~*w*~*i*~ +
> *ε*~*i*~

Statisticians call *u*, *v*, and *w* the *predictors* and *y* the
*response*. Obviously, the model is useful only if there is a fairly
linear relationship between the predictors and the response, but that
requirement is much less restrictive than you might think.
Recipe \@ref(recipe-id203), ["Regressing on Transformed Data"](#recipe-id208), discusses transforming
your variables into a (more) linear relationship so that you can use the
well-developed machinery of linear regression.

The beauty of R is that anyone can build these linear models. The models
are built by a function, `lm`, which returns a model object. From the
model object, we get the coefficients (*β*~*i*~) and regression
statistics. It’s easy. Really!

The horror of R is that anyone can build these models. Nothing requires
you to check that the model is reasonable, much less statistically
significant. Before you blindly believe a model, check it. Most of the
information you need is in the regression summary
(see Recipe \@ref(recipe-id203), ["Understanding the Regression Summary"](#recipe-id240)):

Is the model statistically significant?

:   Check the *F* statistic at the bottom of the summary.

Are the coefficients significant?

:   Check the coefficient’s *t* statistics and *p*-values in the
    summary, or check their confidence intervals
    (see Recipe \@ref(recipe-id203), ["Forming Confidence Intervals for Regression"](#recipe-id211)).

Is the model useful?

:   Check the *R*^2^ near the bottom of the summary.

Does the model fit the data well?

:   Plot the residuals and check the regression diagnostics (see Recipes \@ref(recipe-id203), ["Plotting Regression Residuals"](#recipe-id216), and \@ref(recipe-id203), ["Diagnosing a Linear Regression"](#recipe-id212)).

Does the data satisfy the assumptions behind linear regression?

:   Check whether the diagnostics confirm that a linear model is
    reasonable for your data
    (see Recipe \@ref(recipe-id203), ["Diagnosing a Linear Regression"](#recipe-id212)).

### ANOVA {-}

Analysis of variance (ANOVA) is a powerful statistical technique.
First-year graduate students in statistics are taught ANOVA almost
immediately because of its importance, both theoretical and practical.
We are often amazed, however, at the extent to which people outside the
field are unaware of its purpose and value.

Regression creates a model, and ANOVA is one method of evaluating such
models. The mathematics of ANOVA are intertwined with the mathematics of
regression, so statisticians usually present them together; we follow
that tradition here.

ANOVA is actually a family of techniques that are connected by a common
mathematical analysis. This chapter mentions several applications:

One-way ANOVA

:   This is the simplest application of ANOVA. Suppose you have data
    samples from several populations and are wondering whether the
    populations have different means. One-way ANOVA answers
    that question. If the populations have normal distributions, use the
    `oneway.test` function (see Recipe \@ref(recipe-id203), ["Performing One-Way ANOVA"](#recipe-id218));
    otherwise, use the nonparametric version, the `kruskal.test`
    function
    (see Recipe \@ref(recipe-id203), ["Performing Robust ANOVA (Kruskal–Wallis Test")](#recipe-id228)).

Model comparison

:   When you add or delete a predictor variable from a linear
    regression, you want to know whether that change did or did not
    improve the model. The `anova` function compares two regression
    models and reports whether they are significantly different
    (see Recipe \@ref(recipe-id203), ["Comparing Models by Using ANOVA"](#recipe-id232)).

ANOVA table

:   The `anova` function can also construct the ANOVA table of a linear
    regression model, which includes the *F* statistic needed to gauge
    the model’s statistical significance
    (see Recipe \@ref(recipe-id203), ["Getting Regression Statistics"](#recipe-id231)). This important
    table is discussed in nearly every textbook on regression.

The See Also section contains more about the mathematics of ANOVA.

### Example Data {-}

In many of the examples in this chapter, we start with creating example data using R's pseudorandom number generation capabilities. So at the beginning of each recipe, you may see something like the following:


``` r
set.seed(42)
x <- rnorm(100)
e <- rnorm(100, mean=0, sd=5)
y <- 5 + 15 * x + e
```

We use `set.seed` to set the random number generation seed so that if you run the example code on your machine you will get the same answer. In the preceding example, `x` is a vector of 100 draws from a standard normal (`mean` = 0, `sd` = 1) distribution. Then we create a little random noise called `e` from a normal distribution with `mean` = 0 and `sd` = 5. `y` is then calculated as `5 + 15 * x + e`. The idea behind creating example data rather than using "real world" data is that with simulated "toy" data you can change the coefficients and parameters and see how the change impacts the resulting model. For example, you could increase the standard deviation of `e` in the example data and see what impact that has on the `R^2` of your model. 


### See Also {-}

There are many good texts on linear regression. One of our favorites is
*Applied Linear Regression Models*, 4th edition, by Michael Kutner, Christopher Nachtsheim, and John Neter (McGraw-Hill/Irwin). We generally follow their terminology and
conventions in this chapter.

We also like *Linear Models with R* by Julian Faraway (Chapman & Hall),
because it illustrates regression using R and is quite readable.
Earlier versions of Faraday's work are available free [online](http://cran.r-project.org/doc/contrib/Faraway-PRA.pdf), too.

Performing Simple Linear Regression {#recipe-id203}
-----------------------------------

### Problem {-#problem-id203}

You have two vectors, *x* and *y*, that hold paired observations:
(*x*~1~, *y*~1~), (*x*~2~, *y*~2~), ..., (*x~n~*, *y~n~*). You believe
there is a linear relationship between *x* and *y*, and you want to
create a regression model of the relationship.

### Solution {-#solution-id203}

The `lm` function performs a linear regression and reports the
coefficients.

If your data is in vectors:

``` r
lm(y ~ x)
```

Or if your data is in columns in a data frame:

``` r
lm(y ~ x, data = df)
```


### Discussion {-#discussion-id203}

Simple linear regression involves two variables: a predictor (or independent) variable,
often called *x*, and a response (or dependent) variable, often called *y*. The
regression uses the *ordinary least-squares* (OLS) algorithm to fit the
linear model:

> *y~i~* = *β*~0~ + *β*~1~*x~i~* + *ε~i~*

where *β*~0~ and *β*~1~ are the regression coefficients and the *ε~i~*
are the error terms.

The `lm` function can perform linear regression. The main argument is a
model formula, such as `y ~ x`. The formula has the response variable on
the left of the tilde character (`~`) and the predictor variable on the
right. The function estimates the regression coefficients, *β*~0~ and
*β*~1~, and reports them as the intercept and the coefficient of *x*,
respectively:



``` r
set.seed(42)
x <- rnorm(100)
e <- rnorm(100, mean = 0, sd = 5)
y <- 5 + 15 * x + e

lm(y ~ x)
#> 
#> Call:
#> lm(formula = y ~ x)
#> 
#> Coefficients:
#> (Intercept)            x  
#>        4.56        15.14
```

In this case, the regression equation is:

> *y~i~* = 4.56 + 15.14*x~i~* + *ε~i~*

It is quite common for data to be captured inside a data frame, in which
case you want to perform a regression between two data frame columns.
Here, `x` and `y` are columns of a data frame `dfrm`:


``` r
df <- data.frame(x, y)
head(df)
#>        x     y
#> 1  1.371 31.57
#> 2 -0.565  1.75
#> 3  0.363  5.43
#> 4  0.633 23.74
#> 5  0.404  7.73
#> 6 -0.106  3.94
```

The `lm` function lets you specify a data frame by using the `data`
parameter. If you do, the function will take the variables from the data
frame and not from your workspace:


``` r
lm(y ~ x, data = df)          # Take x and y from df
#> 
#> Call:
#> lm(formula = y ~ x, data = df)
#> 
#> Coefficients:
#> (Intercept)            x  
#>        4.56        15.14
```

Performing Multiple Linear Regression {#recipe-id204}
-------------------------------------

### Problem {-#problem-id204}

You have several predictor variables (e.g., *u*, *v*, and *w*) and a
response variable, *y*. You believe there is a linear relationship
between the predictors and the response, and you want to perform a
linear regression on the data.

### Solution {-#solution-id204}

Use the `lm` function. Specify the multiple predictors on the righthand
side of the formula, separated by plus signs (`+`):


``` r
lm(y ~ u + v + w)
```

### Discussion {-#discussion-id204}

Multiple linear regression is the obvious generalization of simple
linear regression. It allows multiple predictor variables instead of one
predictor variable and still uses OLS to compute the coefficients of a
linear equation. The three-variable regression just given corresponds to
this linear model:

> *y~i~* = *β*~0~ + *β*~1~*u~i~* + *β*~2~*v~i~* + *β*~3~*w~i~* + *ε~i~*

R uses the `lm` function for both simple and multiple linear regression.
You simply add more variables to the righthand side of the model
formula. The output then shows the coefficients of the fitted model. Let's set up some example random normal data using the `rnorm` function:


``` r
set.seed(42)
u <- rnorm(100)
v <- rnorm(100, mean = 3,  sd = 2)
w <- rnorm(100, mean = -3, sd = 1)
e <- rnorm(100, mean = 0,  sd = 3)
```
Then we can create an equation using known coefficients to calculate our *y* variable:

``` r
y <- 5 + 4 * u + 3 * v + 2 * w + e
```

And then if we run a linear regression, we can see that R solves for the coefficients and gets pretty close to the actual values just used:

``` r
lm(y ~ u + v + w)
#> 
#> Call:
#> lm(formula = y ~ u + v + w)
#> 
#> Coefficients:
#> (Intercept)            u            v            w  
#>        4.77         4.17         3.01         1.91
```

The `data` parameter of `lm` is especially valuable when the number of
variables increases, since it’s much easier to keep your data in one
data frame than in many separate variables. Suppose your data is
captured in a data frame, such as the `df` variable shown here:


``` r
df <- data.frame(y, u, v, w)
head(df)
#>       y      u     v     w
#> 1 16.67  1.371 5.402 -5.00
#> 2 14.96 -0.565 5.090 -2.67
#> 3  5.89  0.363 0.994 -1.83
#> 4 27.95  0.633 6.697 -0.94
#> 5  2.42  0.404 1.666 -4.38
#> 6  5.73 -0.106 3.211 -4.15
```

When we supply `df` to the `data` parameter of `lm`, R looks for the
regression variables in the columns of the data frame:


``` r
lm(y ~ u + v + w, data = df)
#> 
#> Call:
#> lm(formula = y ~ u + v + w, data = df)
#> 
#> Coefficients:
#> (Intercept)            u            v            w  
#>        4.77         4.17         3.01         1.91
```

### See Also {-#see_also-id204}

See Recipe \@ref(recipe-id203), ["Performing Simple Linear Regression"](#recipe-id203), for simple
linear regression.

Getting Regression Statistics {#recipe-id231}
-----------------------------

### Problem {-#problem-id231}

You want the critical statistics and information regarding your
regression, such as *R*^2^, the *F* statistic, confidence intervals for
the coefficients, residuals, the ANOVA table, and so forth.

### Solution {-#solution-id231}

Save the regression model in a variable, say `m`:


``` r
m <- lm(y ~ u + v + w)
```

Then use functions to extract regression statistics and information from
the model:

`anova(m)`

:   ANOVA table

`coefficients(m)`

:   Model coefficients

`coef(m)`

:   Same as `coefficients(m)`

`confint(m)`

:   Confidence intervals for the regression coefficients

`deviance(m)`

:   Residual sum of squares

`effects(m)`

:   Vector of orthogonal effects

`fitted(m)`

:   Vector of fitted *y* values

`residuals(m)`

:   Model residuals

`resid(m)`

:   Same as `residuals(m)`

`summary(m)`

:   Key statistics, such as *R*^2^, the *F* statistic, and the residual
    standard error (*σ*)

`vcov(m)`

:   Variance–covariance matrix of the main parameters

### Discussion {-#discussion-id231}

When we started using R, the documentation said use the `lm` function to
perform linear regression. So we did something like this, getting the
output shown in Recipe \@ref(recipe-id204), ["Performing Multiple Linear Regression"](#recipe-id204):


``` r
lm(y ~ u + v + w)
#> 
#> Call:
#> lm(formula = y ~ u + v + w)
#> 
#> Coefficients:
#> (Intercept)            u            v            w  
#>        4.77         4.17         3.01         1.91
```

How disappointing! The output was nothing compared to other
statistics packages such as SAS. Where is *R*^2^? Where are the
confidence intervals for the coefficients? Where is the *F* statistic,
its *p*-value, and the ANOVA table?

Of course, all that information is available—you just have to ask for
it. Other statistics systems dump everything and let you wade through
it. R is more minimalist. It prints a bare-bones output and lets you
request what more you want.

The `lm` function returns a model object that you can assign to a
variable:


``` r
m <- lm(y ~ u + v + w)
```

From the model object, you can extract important information using
specialized functions. The most important function is `summary`:


``` r
summary(m)
#> 
#> Call:
#> lm(formula = y ~ u + v + w)
#> 
#> Residuals:
#>    Min     1Q Median     3Q    Max 
#> -5.383 -1.760 -0.312  1.856  6.984 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)    4.770      0.969    4.92  3.5e-06 ***
#> u              4.173      0.260   16.07  < 2e-16 ***
#> v              3.013      0.148   20.31  < 2e-16 ***
#> w              1.905      0.266    7.15  1.7e-10 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 2.66 on 96 degrees of freedom
#> Multiple R-squared:  0.885,	Adjusted R-squared:  0.882 
#> F-statistic:  247 on 3 and 96 DF,  p-value: <2e-16
```

The summary shows the estimated coefficients. It shows the critical
statistics, such as *R*^2^ and the *F* statistic. It shows an estimate
of *σ*, the standard error of the residuals. The summary is so important
that there is an entire recipe devoted to understanding it. See also Recipe \@ref(recipe-id240), ["Understanding the Regression Summary"](#recipe-id240).


There are specialized extractor functions for other important
information:

Model coefficients (point estimates)

:   
  
  
  ``` r
      coef(m)
  #> (Intercept)           u           v           w 
  #>        4.77        4.17        3.01        1.91
  ```

Confidence intervals for model coefficients

:   
  
  
  ``` r
      confint(m)
  #>             2.5 % 97.5 %
  #> (Intercept)  2.85   6.69
  #> u            3.66   4.69
  #> v            2.72   3.31
  #> w            1.38   2.43
  ```

Model residuals

:   
  
  ``` r
      resid(m)
  #>       1       2       3       4       5       6       7       8       9      10 
  #> -0.5675  2.2880  0.0972  2.1474 -0.7169 -0.3617  1.0350  2.8040 -4.2496 -0.2048 
  #>      11      12      13      14      15      16      17      18      19      20 
  #> -0.6467 -2.5772 -2.9339 -1.9330  1.7800 -1.4400 -2.3989  0.9245 -3.3663  2.6890 
  #>      21      22      23      24      25      26      27      28      29      30 
  #> -1.4190  0.7871  0.0355 -0.3806  5.0459 -2.5011  3.4516  0.3371 -2.7099 -0.0761 
  #>      31      32      33      34      35      36      37      38      39      40 
  #>  2.0261 -1.3902 -2.7041  0.3953  2.7201 -0.0254 -3.9887 -3.9011 -1.9458 -1.7701 
  #>      41      42      43      44      45      46      47      48      49      50 
  #> -0.2614  2.0977 -1.3986 -3.1910  1.8439  0.8218  3.6273 -5.3832  0.2905  3.7878 
  #>      51      52      53      54      55      56      57      58      59      60 
  #>  1.9194 -2.4106  1.6855 -2.7964 -1.3348  3.3549 -1.1525  2.4012 -0.5320 -4.9434 
  #>      61      62      63      64      65      66      67      68      69      70 
  #> -2.4899 -3.2718 -1.6161 -1.5119 -0.4493 -0.9869  5.6273 -4.4626 -1.7568  0.8099 
  #>      71      72      73      74      75      76      77      78      79      80 
  #>  5.0320  0.1689  3.5761 -4.8668  4.2781 -2.1386 -0.9739 -3.6380  0.5788  5.5664 
  #>      81      82      83      84      85      86      87      88      89      90 
  #>  6.9840 -3.5119  1.2842  4.1445 -0.4630 -0.7867 -0.7565  1.6384  3.7578  1.8942 
  #>      91      92      93      94      95      96      97      98      99     100 
  #>  0.5542 -0.8662  1.2041 -1.7401 -0.7261  3.2701  1.4012  0.9476 -0.9140  2.4278
  ```

Residual sum of squares

:   
  
  ``` r
      deviance(m)
  #> [1] 679
  ```

ANOVA table

:   
  
  ``` r
      anova(m)
  #> Analysis of Variance Table
  #> 
  #> Response: y
  #>           Df Sum Sq Mean Sq F value  Pr(>F)    
  #> u          1   1776    1776   251.0 < 2e-16 ***
  #> v          1   3097    3097   437.7 < 2e-16 ***
  #> w          1    362     362    51.1 1.7e-10 ***
  #> Residuals 96    679       7                    
  #> ---
  #> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
  ```

If you find it annoying to save the model in a variable, you are welcome
to use one-liners such as this:


``` r
summary(lm(y ~ u + v + w))

```
Or you can use `magritr` pipes:


``` r
lm(y ~ u + v + w) %>%
  summary
```


### See Also {-#see_also-id231}

See Recipe \@ref(recipe-id240), ["Understanding the Regression Summary"](#recipe-id240). See Recipe \@ref(recipe-id233), ["Identifying Influential Observations"](#recipe-id233), for regression statistics specific to model diagnostics.

Understanding the Regression Summary {#recipe-id240}
------------------------------------

### Problem {-#problem-id240}

You created a linear regression model, `m`. However, you are confused by
the output from `summary(m)`.

### Discussion {-#discussion-id240}

The model summary is important because it links you to the most critical
regression statistics. Here is the model summary from Recipe \@ref(recipe-id231), ["Getting Regression Statistics"](#recipe-id231):


``` r
summary(m)
#> 
#> Call:
#> lm(formula = y ~ u + v + w)
#> 
#> Residuals:
#>    Min     1Q Median     3Q    Max 
#> -5.383 -1.760 -0.312  1.856  6.984 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)    4.770      0.969    4.92  3.5e-06 ***
#> u              4.173      0.260   16.07  < 2e-16 ***
#> v              3.013      0.148   20.31  < 2e-16 ***
#> w              1.905      0.266    7.15  1.7e-10 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 2.66 on 96 degrees of freedom
#> Multiple R-squared:  0.885,	Adjusted R-squared:  0.882 
#> F-statistic:  247 on 3 and 96 DF,  p-value: <2e-16
```

Let’s dissect this summary by section. We’ll read it from top to
bottom—even though the most important statistic, the *F* statistic,
appears at the end.

Call

:    

  ```
  summary(m)$call
  #> lm(formula = y ~ u + v + w)
  ```
  This shows how `lm` was called when it created the model, which is
  important for putting this summary into the proper context.

Residuals statistics

:    
  
  ```
  #> Residuals:
  #>     Min      1Q  Median      3Q     Max 
  #>  -5.383  -1.760  -0.312   1.856   6.984
  ```
  Ideally, the regression residuals would have a perfect
  normal distribution. These statistics help you identify possible
  deviations from normality. The OLS algorithm is mathematically
  guaranteed to produce residuals with a mean of zero.[^zeromean] Hence the
  sign of the median indicates the skew’s direction, and the magnitude
  of the median indicates the extent. In this case the median is
  negative, which suggests some skew to the left.
  
  If the residuals have a nice, bell-shaped distribution, then the
  first quartile (`1Q`) and third quartile (`3Q`) should have about the
  same magnitude. In this example, the larger magnitude of `3Q` versus
  `1Q` (1.859 versus 1.76) indicates a slight skew to the right in
  our data, although the negative median makes the situation
  less clear-cut.
  
  The `Min` and `Max` residuals offer a quick way to detect extreme
  outliers in the data, since extreme outliers (in the
  response variable) produce large residuals.

Coefficients

:    
  
  ```
   #> Coefficients:
   #>             Estimate Std. Error t value Pr(>|t|)    
   #> (Intercept)    4.770      0.969    4.92  3.5e-06 ***
   #> u              4.173      0.260   16.07  < 2e-16 ***
   #> v              3.013      0.148   20.31  < 2e-16 ***
   #> w              1.905      0.266    7.15  1.7e-10 ***
  ```
  
  The column labeled `Estimate` contains the estimated regression
  coefficients as calculated by ordinary least squares.
  
  Theoretically, if a variable’s coefficient is zero then the variable
  is worthless; it adds nothing to the model. Yet the coefficients
  shown here are only estimates, and they will never be exactly zero.
  We therefore ask: Statistically speaking, how likely is it that the
  true coefficient is zero? That is the purpose of the *t* statistics
  and the *p*-values, which in the summary are labeled (respectively)
  `t` `value` and `Pr(>|t|)`.
  
  The *p*-value is a probability. It gauges the likelihood that the
  coefficient is *not* significant, so smaller is better. Big is bad
  because it indicates a high likelihood of insignificance. In this
  example, the *p*-value for the *u* coefficient is a mere 0.00106, so
  *u* is likely significant. The *p*-value for *w*, however, is
  0.05744; this is just over our conventional limit of 0.05, which
  suggests that *w* is likely insignificant.[^significant] Variables with large
  *p*-values are candidates for elimination.
  
  A handy feature is that R flags the significant variables for
  quick identification. Do you notice the extreme righthand column
  containing double asterisks (`**`), a single asterisk (`*`), and a
  period(`.`)? That column highlights the significant variables. The
  line labeled `Signif. codes` at the bottom gives a cryptic guide
  to the flags’ meanings:
  
  --------- ----------------------------------
  `***`     *p*-value between 0 and 0.001
  `**`      *p*-value between 0.001 and 0.01
  `*`       *p*-value between 0.01 and 0.05
  `.`       *p*-value between 0.05 and 0.1
  (blank)   *p*-value between 0.1 and 1.0
  --------- ----------------------------------
  
  The column labeled `Std.` `Error` is the standard error of the
  estimated coefficient. The column labeled `t` `value` is the *t*
  statistic from which the *p*-value was calculated.

Residual standard error

:    
  
  
  ``` r
  # Residual standard error: 2.66 on 96 degrees of freedom
  ```
  
  This reports the standard error of the residuals (*σ*)—that is, the
  sample standard deviation of *ε*.

*R*^2^ (coefficient of determination) 

:    
  
  ``` 
  # Multiple R-squared:  0.885,	Adjusted R-squared:  0.882 
  ```
  
  *R*^2^ is a measure of the model’s quality. Bigger is better.
  Mathematically, it is the fraction of the variance of *y* that is
  explained by the regression model. The remaining variance is not
  explained by the model, so it must be due to other factors (i.e.,
  unknown variables or sampling variability). In this case, the model
  explains 0.885 (88.5%) of the variance of *y*, and the remaining
  0.115 (11.5%) is unexplained.
  
  That being said, we strongly suggest using the adjusted rather than
  the basic *R*^2^. The adjusted value accounts for the number of
  variables in your model and so is a more realistic assessment of
  its effectiveness. In this case, then, we would use 0.882,
  not 0.885.

*F* statistic

:    
  
  ``` 
  # F-statistic: 246.6 on 3 and 96 DF,  p-value: < 2.2e-16
  ```
  
  The *F* statistic tells you whether the model is significant
  or insignificant. The model is significant if any of the
  coefficients are nonzero (i.e., if *β*~*i*~ ≠ 0 for some *i*). It is
  insignificant if all coefficients are zero (*β*~1~ = *β*~2~ = … =
  *β*~*n*~ = 0).
  
  Conventionally, a *p*-value of less than 0.05 indicates that the
  model is likely significant (one or more *β*~*i*~ are nonzero),
  whereas values exceeding 0.05 indicate that the model is likely
  not significant. Here, the probability is only 2.2e-16 that our
  model is insignificant. That’s good.
  
  Most people look at the *R*^2^ statistic first. The statistician
  wisely starts with the *F* statistic, because if the model is not
  significant then nothing else matters.

### See Also {-#see_also-id240}

See Recipe \@ref(recipe-id231), ["Getting Regression Statistics"](#recipe-id231), for more on
extracting statistics and information from the model object.


Performing Linear Regression Without an Intercept {#recipe-id205}
-------------------------------------------------

### Problem {-#problem-id205}

You want to perform a linear regression, but you want to force the
intercept to be zero.

### Solution {-#solution-id205}

Add "`+` `0`" to the righthand side of your regression formula. That
will force `lm` to fit the model with a zero intercept:


``` r
lm(y ~ x + 0)
```

The corresponding regression equation is:

> *y*~*i*~ = *βx*~*i*~ + *ε*~*i*~

### Discussion {-#discussion-id205}

Linear regression ordinarily includes an intercept term, so that is the
default in R. In rare cases, however, you may want to fit the data while
assuming that the intercept is zero. In this case you make a modeling
assumption: when *x* is zero, *y* should be zero.

When you force a zero intercept, the `lm` output includes a coefficient
for *x* but no intercept for *y*, as shown here:


``` r
lm(y ~ x + 0)
#> 
#> Call:
#> lm(formula = y ~ x + 0)
#> 
#> Coefficients:
#>   x  
#> 4.3
```

We strongly suggest you check that modeling assumption before proceeding.
Perform a regression with an intercept; then see if the intercept could
plausibly be zero. Check the intercept’s confidence interval. In this
example, the confidence interval is (6.26, 8.84):


``` r
confint(lm(y ~ x))
#>             2.5 % 97.5 %
#> (Intercept)  6.26   8.84
#> x            2.82   5.31
```

Because the confidence interval does not contain zero, it is *not* statistically
plausible that the intercept could be zero. So in this case, it is not reasonable to rerun the regression while forcing a zero intercept.

Regressing Only Variables That Highly Correlate with Your Dependent Variable {#title-highcor}
------------------------------------------------------

### Problem {-#problem-highcor}

You have a data frame with many variables and you want to build a multiple linear regression using only the variables that are highly correlated to your response (dependent) variable. 

### Solution {-#solution-highcor}
If `df` is our data frame containing both our response (dependent) and all our predictor (independent) variables and `dep_var` is our response variable, we can figure out our best predictors and then use them in a linear regression. If we want the top four predictor variables, we can use this recipe:


``` r
best_pred <- df %>%
  select(-dep_var) %>%
  map_dbl(cor, y = df$dep_var) %>%
  sort(decreasing = TRUE) %>%
  .[1:4] %>%
  names %>%
  df[.]

mod <- lm(df$dep_var ~ as.matrix(best_pred))
```

This recipe is a combination of many different pieces of logic used elsewhere in this book. We will describe each step here, and then walk through it in the Discussion using some example data. 

First we drop the response variable out of our pipe chain so that we have only our predictor variables in our data flow:

``` r
df %>%
  select(-dep_var)
```

Then we use `map_dbl` from `purrr` to perform a pairwise correlation on each column relative to the response variable:

``` r
  map_dbl(cor, y = df$dep_var) %>%
```

We then take the resulting correlations and sort them in decreasing order:

``` r
  sort(decreasing = TRUE) %>%
```

We want only the top four correlated variables, so we select the top four records in the resulting vector:

``` r
  .[1:4] %>%
```

And we don't need the correlation values, only the names of the rows—which are the variable names from our original data frame, `df`:

``` r
names %>%
```

Then we can pass those names into our subsetting brackets to select only the columns with names matching the ones we want:

``` r
df[.]
```

Our pipe chain assigns the resulting data frame into `best_pred`. We can then use `best_pred` as the predictor variables in our regression and we can use `df$dep_var` as the response:

``` r
mod <- lm(df$dep_var ~ as.matrix(best_pred))
```

### Discussion {-#discussion-title}

By combining the mapping functions discussed in Recipe \@ref(recipe-id157), ["Applying a Function to Every Column"](#recipe-id157), we can create a recipe to remove low-correlation variables from
a set of predictors and use the high-correlation predictors in a regression. 

We have an example data frame that contains six predictor variables named `pred1` through `pred6`. The response variable is named `resp`. Let's walk that data frame through our logic and see how it works. 

Loading the data and dropping the `resp` variable is pretty straightforward. So let's look at the result of mapping the `cor` function:

``` r
# loads the pred data frame
load("./data/pred.rdata")

pred %>%
  select(-resp) %>%
  map_dbl(cor, y = pred$resp) 
#> pred1 pred2 pred3 pred4 pred5 pred6 
#> 0.573 0.279 0.753 0.799 0.322 0.607
```
The output is a named vector of values where the names are the variable names and the values are the pairwise correlations between each predictor variable and `resp`, the response variable. 

If we sort this vector, we get the correlations in decreasing order:

``` r
pred %>%
  select(-resp) %>%
  map_dbl(cor, y = pred$resp) %>%
  sort(decreasing = TRUE)
#> pred4 pred3 pred6 pred1 pred5 pred2 
#> 0.799 0.753 0.607 0.573 0.322 0.279
```

Using subsetting allows us to select the top four records. The `.` operator is a special operator that tells the pipe where to put the result of the prior step.

``` r
pred %>%
  select(-resp) %>%
  map_dbl(cor, y = pred$resp) %>%
  sort(decreasing = TRUE) %>%
  .[1:4] 
#> pred4 pred3 pred6 pred1 
#> 0.799 0.753 0.607 0.573
```

We then use the `names` function to extract the names from our vector. The names are the names of the columns we ultimately want to use as our independent variables:

``` r
pred %>%
  select(-resp) %>%
  map_dbl(cor, y = pred$resp) %>%
  sort(decreasing = TRUE) %>%
  .[1:4] %>%
  names 
#> [1] "pred4" "pred3" "pred6" "pred1"
```

When we pass the vector of names into `pred[.]`, the names are used to select columns from the `pred` data frame. We then use `head` to select only the top six rows for easier illustration:

``` r
pred %>%
  select(-resp) %>%
  map_dbl(cor, y = pred$resp) %>%
  sort(decreasing = TRUE) %>%
  .[1:4] %>%
  names %>%
  pred[.] %>%
  head
#>    pred4   pred3  pred6  pred1
#> 1  7.252  1.5127  0.560  0.206
#> 2  2.076  0.2579 -0.124 -0.361
#> 3 -0.649  0.0884  0.657  0.758
#> 4  1.365 -0.1209  0.122 -0.727
#> 5 -5.444 -1.1943 -0.391 -1.368
#> 6  2.554  0.6120  1.273  0.433
```

Now let's bring it all together and pass the resulting data into the regression:

``` r
best_pred <- pred %>%
  select(-resp) %>%
  map_dbl(cor, y = pred$resp) %>%
  sort(decreasing = TRUE) %>%
  .[1:4] %>%
  names %>%
  pred[.]

mod <- lm(pred$resp ~ as.matrix(best_pred))
summary(mod)
#> 
#> Call:
#> lm(formula = pred$resp ~ as.matrix(best_pred))
#> 
#> Residuals:
#>    Min     1Q Median     3Q    Max 
#> -1.485 -0.619  0.189  0.562  1.398 
#> 
#> Coefficients:
#>                           Estimate Std. Error t value Pr(>|t|)   
#> (Intercept)                  1.117      0.340    3.28   0.0051 **
#> as.matrix(best_pred)pred4    0.523      0.207    2.53   0.0231 * 
#> as.matrix(best_pred)pred3   -0.693      0.870   -0.80   0.4382   
#> as.matrix(best_pred)pred6    1.160      0.682    1.70   0.1095   
#> as.matrix(best_pred)pred1    0.343      0.359    0.95   0.3549   
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 0.927 on 15 degrees of freedom
#> Multiple R-squared:  0.838,	Adjusted R-squared:  0.795 
#> F-statistic: 19.4 on 4 and 15 DF,  p-value: 8.59e-06
```

Performing Linear Regression with Interaction Terms {#recipe-id206}
---------------------------------------------------

### Problem {-#problem-id206}

You want to include an interaction term in your regression.

### Solution {-#solution-id206}

The R syntax for regression formulas lets you specify interaction terms.
To indicate the interaction of two variables, `u` and `v`, we separate their names with an asterisk (`*`):


``` r
lm(y ~ u * v)
```

This corresponds to the model *y*~i~ = *β*~0~ + *β*~1~*u*~*i*~ +
*β*~2~*v*~*i*~ + *β*~3~*u*~*i*~*v*~*i*~ + *ε*~*i*~, which includes the
first-order interaction term *β*~3~*u*~*i*~*v*~*i*~.

### Discussion {-#discussion-id206}

In regression, an interaction occurs when the product of two predictor
variables is also a significant predictor (i.e., in addition to the
predictor variables themselves). Suppose we have two predictors, *u* and
*v*, and want to include their interaction in the regression. This is
expressed by the following equation:

> *y*~*i*~ = *β*~0~ + *β*~1~*u*~*i*~ + *β*~2~*v*~*i*~ +
> *β*~3~*u*~*i*~*v*~*i*~ + *ε*~*i*~

Here the product term, *β*~3~*u*~*i*~*v*~*i*~, is called the *interaction
term*. The R formula for that equation is:


``` r
y ~ u * v
```

When you write `y` `~` `u * v`, R automatically includes *u*, *v*, and
their product in the model. This is for a good reason. If a model
includes an interaction term, such as *β*~3~*u*~*i*~*v*~*i*~, then
regression theory tells us the model should also contain the constituent
variables *u*~*i*~ and *v*~*i*~.

Likewise, if you have three predictors (*u*, *v*, and *w*) and want to
include all their interactions, separate them by asterisks:


``` r
y ~ u * v * w
```

This corresponds to the regression equation:

> *y*~*i*~ = *β*~0~ + *β*~1~*u*~*i*~ + *β*~2~*v*~*i*~ + *β*~3~*w*~*i*~ +
> *β*~4~*u*~*i*~*v*~*i*~ + *β*~5~*u*~*i*~*w*~*i*~ +
> *β*~6~*v*~*i*~*w*~*i*~ + *β*~7~*u*~*i*~*v*~*i*~*w*~*i*~ + *ε*~*i*~

Now we have all the first-order interactions and a second-order
interaction (*β*~7~*u*~*i*~*v*~*i*~*w*~*i*~).

Sometimes, however, you may not want every possible interaction. You can
explicitly specify a single product by using the colon operator (`:`).
For example, `u:v:w` denotes the product term *βu*~*i*~*v*~*i*~*w*~*i*~
but without all possible interactions. So the R formula:


``` r
y ~ u + v + w + u:v:w
```

corresponds to the regression equation:

> *y*~*i*~ = *β*~0~ + *β*~1~*u*~*i*~ + *β*~2~*v*~*i*~ + *β*~3~*w*~*i*~ +
> *β*~4~*u*~*i*~*v*~*i*~*w*~*i*~ + *ε*~*i*~

It might seem odd that colon (`:`) means pure multiplication while
asterisk (`*`) means both multiplication and inclusion of constituent
terms. Again, this is because we normally incorporate the constituents
when we include their interaction, so making that approach the default for
asterisk makes sense.

There is some additional syntax for easily specifying many interactions:

`(u + v + ... + w)^2`

:   Include all variables (*u*, *v*, ..., *w*) and all their
    first-order interactions.

`(u + v + ... + w)^3`

:   Include all variables, all their first-order interactions, and all
    their second-order interactions.

`(u + v + ... + w)^4`

:   And so forth.

Both the asterisk (`*`) and the colon (`:`) follow a “distributive law,”
so the following notations are also allowed:

`x*(u + v + ... + w)`

:   Same as `x*u + x*v + ... +
                  x*w` 
                  (which is the same as `x +
                  u + v + ... + w + x:u + x:v + ... + x:w`).

`x:(u + v + ... + w)`

:   Same as `x:u + x:v + ... + x:w`.

All this syntax gives you some flexibility in writing your formula. For
example, these three formulas are equivalent:


``` r
y ~ u * v
y ~ u + v + u:v
y ~ (u + v) ^ 2
```

They all define the same regression equation, *y*~*i*~ = *β*~0~ +
*β*~1~*u*~*i*~ + *β*~2~*v*~*i*~ + *β*~3~*u*~*i*~*v*~*i*~ + *ε*~*i*~ .

### See Also {-#see_also-id206}

The full syntax for formulas is richer than described here.
See [*R in a Nutshell*](http://oreilly.com/catalog/9780596801717) (O’Reilly)
or the R Language Definition for more details.

Selecting the Best Regression Variables {#recipe-id236}
---------------------------------------

### Problem {-#problem-id236}

You are creating a new regression model or improving an existing model.
You have the luxury of many regression variables, and you want to select
the best subset of those variables.

### Solution {-#solution-id236}

The `step` function can perform stepwise regression, either forward or
backward. Backward stepwise regression starts with many variables and
removes the underperformers:


``` r
full.model <- lm(y ~ x1 + x2 + x3 + x4)
reduced.model <- step(full.model, direction = "backward")
```

Forward stepwise regression starts with a few variables and adds new
ones to improve the model until it cannot be improved further:


``` r
min.model <- lm(y ~ 1)
fwd.model <-
  step(min.model,
       direction = "forward",
       scope = (~ x1 + x2 + x3 + x4))
```

### Discussion {-#discussion-id236}

When you have many predictors, it can be quite difficult to choose the
best subset. Adding and removing individual variables affects the
overall mix, so the search for “the best” can become tedious.

The `step` function automates that search. Backward stepwise regression
is the easiest approach. Start with a model that includes all the
predictors. We call that the *full model*. The model summary, shown here,
indicates that not all predictors are statistically significant:


``` r
# example data
set.seed(4)
n <- 150
x1 <- rnorm(n)
x2 <- rnorm(n, 1, 2)
x3 <- rnorm(n, 3, 1)
x4 <- rnorm(n,-2, 2)
e <- rnorm(n, 0, 3)
y <- 4 + x1 + 5 * x3 + e

# build the model
full.model <- lm(y ~ x1 + x2 + x3 + x4)
summary(full.model)
#> 
#> Call:
#> lm(formula = y ~ x1 + x2 + x3 + x4)
#> 
#> Residuals:
#>    Min     1Q Median     3Q    Max 
#> -8.032 -1.774  0.158  2.032  6.626 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)  3.40224    0.80767    4.21  4.4e-05 ***
#> x1           0.53937    0.25935    2.08    0.039 *  
#> x2           0.16831    0.12291    1.37    0.173    
#> x3           5.17410    0.23983   21.57  < 2e-16 ***
#> x4          -0.00982    0.12954   -0.08    0.940    
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 2.92 on 145 degrees of freedom
#> Multiple R-squared:  0.77,	Adjusted R-squared:  0.763 
#> F-statistic:  121 on 4 and 145 DF,  p-value: <2e-16
```

We want to eliminate the insignificant variables, so we use `step` to
incrementally eliminate the underperformers. The result is called the
*reduced model*:


``` r
reduced.model <- step(full.model, direction="backward")
#> Start:  AIC=327
#> y ~ x1 + x2 + x3 + x4
#> 
#>        Df Sum of Sq  RSS AIC
#> - x4    1         0 1240 325
#> - x2    1        16 1256 327
#> <none>              1240 327
#> - x1    1        37 1277 329
#> - x3    1      3979 5219 540
#> 
#> Step:  AIC=325
#> y ~ x1 + x2 + x3
#> 
#>        Df Sum of Sq  RSS AIC
#> - x2    1        16 1256 325
#> <none>              1240 325
#> - x1    1        37 1277 327
#> - x3    1      3988 5228 539
#> 
#> Step:  AIC=325
#> y ~ x1 + x3
#> 
#>        Df Sum of Sq  RSS AIC
#> <none>              1256 325
#> - x1    1        44 1300 328
#> - x3    1      3974 5230 537
```

The output from `step` shows the sequence of models that it explored. In
this case, `step` removed `x2` and `x4` and left only `x1` and `x3` in
the final (reduced) model. The summary of the reduced model shows that
it contains only significant predictors:


``` r
summary(reduced.model)
#> 
#> Call:
#> lm(formula = y ~ x1 + x3)
#> 
#> Residuals:
#>    Min     1Q Median     3Q    Max 
#> -8.148 -1.850 -0.055  2.026  6.550 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)    3.648      0.751    4.86    3e-06 ***
#> x1             0.582      0.255    2.28    0.024 *  
#> x3             5.147      0.239   21.57   <2e-16 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 2.92 on 147 degrees of freedom
#> Multiple R-squared:  0.767,	Adjusted R-squared:  0.763 
#> F-statistic:  241 on 2 and 147 DF,  p-value: <2e-16
```

Backward stepwise regression is easy, but sometimes it’s not feasible to
start with “everything” because you have too many candidate variables.
In that case use forward stepwise regression, which will start with
nothing and incrementally add variables that improve the regression. It
stops when no further improvement is possible.

A model that “starts with nothing” may look odd at first:


``` r
min.model <- lm(y ~ 1)
```

This is a model with a response variable (*y*) but no predictor
variables. (All the fitted values for *y* are simply the mean of *y*,
which is what you would guess if no predictors were available.)

We must tell `step` which candidate variables are available for
inclusion in the model. That is the purpose of the `scope` argument. `scope` is a formula with nothing on the lefthand side of the tilde
(`~`) and candidate variables on the righthand side:


``` r
fwd.model <- step(
  min.model,
  direction = "forward",
  scope = (~ x1 + x2 + x3 + x4),
  trace = 0
)
```

Here we see that `x1`, `x2`, `x3`, and `x4` are all candidates for
inclusion. (We also included `trace = 0` to inhibit the voluminous output
from `step`.) The resulting model has two significant predictors and no
insignificant predictors:


``` r
summary(fwd.model)
#> 
#> Call:
#> lm(formula = y ~ x3 + x1)
#> 
#> Residuals:
#>    Min     1Q Median     3Q    Max 
#> -8.148 -1.850 -0.055  2.026  6.550 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)    3.648      0.751    4.86    3e-06 ***
#> x3             5.147      0.239   21.57   <2e-16 ***
#> x1             0.582      0.255    2.28    0.024 *  
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 2.92 on 147 degrees of freedom
#> Multiple R-squared:  0.767,	Adjusted R-squared:  0.763 
#> F-statistic:  241 on 2 and 147 DF,  p-value: <2e-16
```

The step-forward algorithm reached the same model as the step-backward
model by including `x1` and `x3` but excluding `x2` and `x4`. This is a
toy example, so that is not surprising. In real applications, we suggest
trying both the forward and the backward regression and then comparing
the results. You might be surprised.

Finally, don’t get carried away with stepwise regression. It is not a
panacea, it cannot turn junk into gold, and it is definitely not a
substitute for choosing predictors carefully and wisely. You might
think: “Oh boy! I can generate every possible interaction term for my
model, then let `step` choose the best ones! What a model I’ll get!”
You’d be thinking of something like this, which starts with all possible interactions
and then tries to reduce the model:


``` r
full.model <- lm(y ~ (x1 + x2 + x3 + x4) ^ 4)
reduced.model <- step(full.model, direction = "backward")
#> Start:  AIC=337
#> y ~ (x1 + x2 + x3 + x4)^4
#> 
#>               Df Sum of Sq  RSS AIC
#> - x1:x2:x3:x4  1    0.0321 1145 335
#> <none>                     1145 337
#> 
#> Step:  AIC=335
#> y ~ x1 + x2 + x3 + x4 + x1:x2 + x1:x3 + x1:x4 + x2:x3 + x2:x4 + 
#>     x3:x4 + x1:x2:x3 + x1:x2:x4 + x1:x3:x4 + x2:x3:x4
#> 
#>            Df Sum of Sq  RSS AIC
#> - x2:x3:x4  1      0.76 1146 333
#> - x1:x3:x4  1      8.37 1154 334
#> <none>                  1145 335
#> - x1:x2:x4  1     20.95 1166 336
#> - x1:x2:x3  1     25.18 1170 336
#> 
#> Step:  AIC=333
#> y ~ x1 + x2 + x3 + x4 + x1:x2 + x1:x3 + x1:x4 + x2:x3 + x2:x4 + 
#>     x3:x4 + x1:x2:x3 + x1:x2:x4 + x1:x3:x4
#> 
#>            Df Sum of Sq  RSS AIC
#> - x1:x3:x4  1      8.74 1155 332
#> <none>                  1146 333
#> - x1:x2:x4  1     21.72 1168 334
#> - x1:x2:x3  1     26.51 1172 334
#> 
#> Step:  AIC=332
#> y ~ x1 + x2 + x3 + x4 + x1:x2 + x1:x3 + x1:x4 + x2:x3 + x2:x4 + 
#>     x3:x4 + x1:x2:x3 + x1:x2:x4
#> 
#>            Df Sum of Sq  RSS AIC
#> - x3:x4     1      0.29 1155 330
#> <none>                  1155 332
#> - x1:x2:x4  1     23.24 1178 333
#> - x1:x2:x3  1     31.11 1186 334
#> 
#> Step:  AIC=330
#> y ~ x1 + x2 + x3 + x4 + x1:x2 + x1:x3 + x1:x4 + x2:x3 + x2:x4 + 
#>     x1:x2:x3 + x1:x2:x4
#> 
#>            Df Sum of Sq  RSS AIC
#> <none>                  1155 330
#> - x1:x2:x4  1      23.4 1178 331
#> - x1:x2:x3  1      31.5 1187 332
```

This does not work well. Most of the interaction terms are meaningless.
The `step` function becomes overwhelmed, and you are left with many
insignificant terms.

### See Also {-#see_also-id236}

See Recipe \@ref(recipe-id232), ["Comparing Models by Using ANOVA"](#recipe-id232).

Regressing on a Subset of Your Data {#recipe-id209}
-----------------------------------

### Problem {-#problem-id209}

You want to fit a linear model to a subset of your data, not to the
entire dataset.

### Solution {-#solution-id209}

The `lm` function has a `subset` parameter that specifies which data
elements should be used for fitting. The parameter’s value can be any
index expression that could index your data. This shows a fitting that
uses only the first 100 observations:


``` r
lm(y ~ x1, subset=1:100)          # Use only x[1:100]
```

### Discussion {-#discussion-id209}

You will often want to regress only a subset of your data. This can
happen, for example, when you're using in-sample data to create the model and
out-of-sample data to test it.

The `lm` function has a parameter, `subset`, that selects the
observations used for fitting. The value of `subset` is a vector. It can
be a vector of index values, in which case `lm` selects only the
indicated observations from your data. It can also be a logical vector,
the same length as your data, in which case `lm` selects the
observations with a corresponding `TRUE`.

Suppose you have 1,000 observations of (*x*, *y*) pairs and want to fit
your model using only the first half of those observations. Use a
`subset` parameter of 1:500, indicating `lm` should use observations 1
through 500:


``` r
## example data
n <- 1000
x <- rnorm(n)
e <- rnorm(n, 0, .5)
y <- 3 + 2 * x + e
lm(y ~ x, subset = 1:500)
#> 
#> Call:
#> lm(formula = y ~ x, subset = 1:500)
#> 
#> Coefficients:
#> (Intercept)            x  
#>           3            2
```

More generally, you can use the expression `1:floor(length(x)/2)` to
select the first half of your data, regardless of size:


``` r
lm(y ~ x, subset = 1:floor(length(x) / 2))
#> 
#> Call:
#> lm(formula = y ~ x, subset = 1:floor(length(x)/2))
#> 
#> Coefficients:
#> (Intercept)            x  
#>           3            2
```

Let’s say your data was collected in several labs and you have a factor,
`lab`, that identifies the lab of origin. You can limit your regression
to observations collected in New Jersey by using a logical vector that
is `TRUE` only for those observations:


``` r
load('./data/lab_df.rdata')
lm(y ~ x, subset = (lab == "NJ"), data = lab_df)
#> 
#> Call:
#> lm(formula = y ~ x, data = lab_df, subset = (lab == "NJ"))
#> 
#> Coefficients:
#> (Intercept)            x  
#>        2.58         5.03
```

Using an Expression Inside a Regression Formula {#recipe-id244}
-----------------------------------------------

### Problem {-#problem-id244}

You want to regress on calculated values, not simple variables, but the
syntax of a regression formula seems to forbid that.

### Solution {-#solution-id244}

Embed the expressions for the calculated values inside the `I(...)`
operator. That will force R to calculate the expression and use the
calculated value for the regression.

### Discussion {-#discussion-id244}

If you want to regress on the sum of *u* and *v*, then this is your
regression equation:

> *y*~*i*~ = *β*~0~ + *β*~1~(*u*~*i*~ + *v*~*i*~) + *ε*~*i*~

How do you write that equation as a regression formula? This won’t work:


``` r
lm(y ~ u + v)    # Not quite right
```

Here R will interpret `u` and `v` as two separate predictors, each with
its own regression coefficient. Likewise, suppose your regression
equation is:

> *y*~*i*~ = *β*~0~ + *β*~1~*u*~*i*~ + *β*~2~*u*~*i*~^2^ + *ε*~*i*~

This won’t work:


``` r
lm(y ~ u + u ^ 2)  # That's an interaction, not a quadratic term
```

R will interpret `u^2` as an interaction term
(see Recipe \@ref(recipe-id206), ["Performing Linear Regression with Interaction Terms"](#recipe-id206))
and not as the square of *u*.

The solution is to surround the expressions by the `I(...)` operator,
which inhibits the expressions from being interpreted as a regression
formula. Instead, it forces R to calculate the expression’s value and
then incorporate that value directly into the regression. Thus, the first
example becomes:


``` r
lm(y ~ I(u + v))
```

In response to that command, R computes *u* + *v* and then regresses *y*
on the sum.

For the second example we use:


``` r
lm(y ~ u + I(u ^ 2))
```

Here R computes the square of *u* and then regresses on the sum *u* +
*u*^2^.

All the basic binary operators (`+`, `-`, `*`, `/`, `^`) have special
meanings inside a regression formula. For this reason, you must use the
`I(...)` operator whenever you incorporate calculated values into a
regression.

A beautiful aspect of these embedded transformations is that R remembers
them and applies them when you make predictions from the
model. Consider the quadratic model described by the second example. It
uses `u` and `u^2`, but we supply the value of *u* only and R does the
heavy lifting. We don’t need to calculate the square of *u* ourselves:


``` r
load('./data/df_squared.rdata')
m <- lm(y ~ u + I(u ^ 2), data = df_squared)
predict(m, newdata = data.frame(u = 13.4))
#>   1 
#> 877
```

### See Also {-#see_also-id244}

See Recipe \@ref(recipe-id243), ["Regressing on a Polynomial"](#recipe-id243), for the special case of
regression on a polynomial. See Recipe \@ref(recipe-id208),
["Regressing on Transformed Data"](#recipe-id208), for incorporating other
data transformations into the regression.

Regressing on a Polynomial {#recipe-id243}
--------------------------

### Problem {-#problem-id243}

You want to regress *y* on a polynomial of *x*.

### Solution {-#solution-id243}

Use the `poly(x,n)` function in your regression formula to regress on an
*n*-degree polynomial of *x*. This example models *y* as a cubic
function of *x*:


``` r
lm(y ~ poly(x, 3, raw = TRUE))
```

The example’s formula corresponds to the following cubic regression
equation:

> *y*~*i*~ = *β*~0~ + *β*~1~*x*~*i*~ + *β*~2~*x*~*i*~^2^ +
> *β*~3~*x*~*i*~^3^ + *ε*~*i*~

### Discussion {-#discussion-id243}

When people first use a polynomial model in R, they often do something clunky like
this:


``` r
x_sq <- x ^ 2
x_cub <- x ^ 3
m <- lm(y ~ x + x_sq + x_cub)
```

Obviously, this is quite annoying, and it litters their workspace with
extra variables.

It’s much easier to write:


``` r
m <- lm(y ~ poly(x, 3, raw = TRUE))
```

The `raw = TRUE` is necessary. Without it, the `poly` function computes
orthogonal polynomials instead of simple polynomials.

Beyond the convenience, a huge advantage is that R will calculate all
those powers of *x* when you make predictions from the model
(see Recipe \@ref(recipe-id214), ["Predicting New Values"](#recipe-id214)). Without that, you are stuck
calculating *x*^2^ and *x*^3^ yourself every time you employ the model.

Here is another good reason to use `poly`. You cannot write your
regression formula in this way:


``` r
lm(y ~ x + x^2 + x^3)     # Does not do what you think!
```

R will interpret `x^2` and `x^3` as interaction terms, not as powers of
*x*. The resulting model is a one-term linear regression, completely
unlike your expectation. You could write the regression formula like
this:


``` r
lm(y ~ x + I(x ^ 2) + I(x ^ 3))
```

But that’s getting pretty verbose. Just use `poly`.

### See Also {-#see_also-id243}

See Recipe \@ref(recipe-id206), ["Performing Linear Regression with Interaction Terms"](#recipe-id206),
for more about interaction terms. See Recipe \@ref(recipe-id208), ["Regressing on Transformed Data"](#recipe-id208), for other
transformations on regression data.

Regressing on Transformed Data {#recipe-id208}
------------------------------

### Problem {-#problem-id208}

You want to build a regression model for *x* and *y*, but they do not
have a linear relationship.

### Solution {-#solution-id208}

You can embed the needed transformation inside the regression formula.
If, for example, *y* must be transformed into log(*y*), then the
regression formula becomes:


``` r
lm(log(y) ~ x)
```

### Discussion {-#discussion-id208}

A critical assumption behind the `lm` function for regression is that
the variables have a linear relationship. To the extent this assumption
is false, the resulting regression becomes meaningless.

Fortunately, many datasets can be transformed into a linear relationship
before applying `lm`.


```
#> `geom_smooth()` using formula = 'y ~ x'
#> `geom_smooth()` using formula = 'y ~ x'
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/obs-trans-1} 

}

\caption{Example of a data transform}(\#fig:obs-trans)
\end{figure}

Figure \@ref(fig:obs-trans) shows an example of
exponential decay. The left panel shows the original data, *z*. The
dotted line shows a linear regression on the original data; clearly,
it’s a lousy fit. If the data is really exponential, then a possible
model is:

*z* = exp[*β*~0~ + *β*~1~*t* + *ε*]

where *t* is time and exp[] is the exponential function (*e*^*x*^).
This is not linear, of course, but we can linearize it by taking
logarithms:

log(*z*) = *β*~0~ + *β*~1~*t* + *ε*

In R, that regression is simple because we can embed the log transform
directly into the regression formula:


``` r
# read in our example data
load(file = './data/df_decay.rdata')
z <- df_decay$z
t <- df_decay$time

# transform and model
m <- lm(log(z) ~ t)
summary(m)
#> 
#> Call:
#> lm(formula = log(z) ~ t)
#> 
#> Residuals:
#>     Min      1Q  Median      3Q     Max 
#> -0.4479 -0.0993  0.0049  0.0978  0.2802 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)   0.6887     0.0306    22.5   <2e-16 ***
#> t            -2.0118     0.0351   -57.3   <2e-16 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 0.148 on 98 degrees of freedom
#> Multiple R-squared:  0.971,	Adjusted R-squared:  0.971 
#> F-statistic: 3.28e+03 on 1 and 98 DF,  p-value: <2e-16
```

The right panel of Figure \@ref(fig:obs-trans)
shows the plot of log(*z*) versus time. Superimposed on that plot is
their regression line. The fit appears to be much better; this is
confirmed by the *R*^2^ = 0.97, compared with 0.82 for the linear
regression on the original data.

You can embed other functions inside your formula. If you thought the
relationship was quadratic, you could use a square-root transformation:


``` r
lm(sqrt(y) ~ month)
```

You can apply transformations to variables on both sides of the formula,
of course. This formula regresses *y* on the square root of *x*:


``` r
lm(y ~ sqrt(x))
```

This regression is for a log-log relationship between *x* and *y*:


``` r
lm(log(y) ~ log(x))
```

### See Also {-#see_also-id208}

See Recipe \@ref(recipe-id210,) ["Finding the Best Power Transformation (Box–Cox Procedure)"](#recipe-id210).

Finding the Best Power Transformation (Box–Cox Procedure) {#recipe-id210}
---------------------------------------------------------

### Problem {-#problem-id210}

You want to improve your linear model by applying a power transformation
to the response variable.

### Solution {-#solution-id210}

Use the Box–Cox procedure, which is implemented by the `boxcox` function
of the `MASS` package. The procedure will identify a power, *λ*, such
that transforming *y* into *y*^*λ*^ will improve the fit of your model:


``` r
library(MASS)
m <- lm(y ~ x)
boxcox(m)
```

### Discussion {-#discussion-id210}

To illustrate the Box–Cox transformation, let’s create some artificial
data using the equation *y*^–1.5^ = *x* + *ε*, where *ε* is an error
term:


``` r
set.seed(9)
x <- 10:100
eps <- rnorm(length(x), sd = 5)
y <- (x + eps) ^ (-1 / 1.5)
```

Then we will (mistakenly) model the data using a simple linear
regression and derive an adjusted *R*^2^ of 0.637:


``` r
m <- lm(y ~ x)
summary(m)
#> 
#> Call:
#> lm(formula = y ~ x)
#> 
#> Residuals:
#>      Min       1Q   Median       3Q      Max 
#> -0.04032 -0.01633 -0.00792  0.00996  0.14516 
#> 
#> Coefficients:
#>              Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)  0.166885   0.007078    23.6   <2e-16 ***
#> x           -0.001465   0.000116   -12.6   <2e-16 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 0.0291 on 89 degrees of freedom
#> Multiple R-squared:  0.641,	Adjusted R-squared:  0.637 
#> F-statistic:  159 on 1 and 89 DF,  p-value: <2e-16
```

When plotting the residuals against the fitted values, we get a clue
that something is wrong. We can get a `ggplot` residual plot using the `broom` library. The `augment` function from `broom` will put our residuals (and other things) into a data frame for easier plotting. Then we can use `ggplot` to plot:


``` r
library(broom)
augmented_m <- augment(m)

ggplot(augmented_m, aes(x = .fitted, y = .resid)) + 
  geom_point()
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/fitplot1-1} 

}

\caption{Fitted values versus residuals}(\#fig:fitplot1)
\end{figure}

If you just need a fast peek at the residual plot and don't care if the result is a `ggplot` figure, you can use Base R's `plot` method on the model object, `m`:

``` r
plot(m, which = 1)  # which = 1 plots only the fitted vs residuals
```

We can see in Figure \@ref(fig:fitplot1) that this plot has a clear parabolic
shape. A possible fix is a power transformation on *y*, so we run the
Box–Cox procedure:


``` r
library(MASS)
#> 
#> Attaching package: 'MASS'
#> The following object is masked from 'package:patchwork':
#> 
#>     area
#> The following object is masked from 'package:dplyr':
#> 
#>     select
bc <- boxcox(m)
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/boxcox-1} 

}

\caption{Output of boxcox on the model (m)}(\#fig:boxcox)
\end{figure}

The `boxcox` function plots values
of *λ* against the log-likelihood of the resulting model, as shown in Figure \@ref(fig:boxcox). We want to
maximize that log-likelihood, so the function draws a line at the best
value and also draws lines at the limits of its confidence interval. In
this case, it looks like the best value is around –1.5, with a
confidence interval of about (–1.75, –1.25).

Oddly, the `boxcox` function does not return the best value of *λ*.
Rather, it returns the (*x*, *y*) pairs displayed in the plot. It’s
pretty easy to find the values of *λ* that yield the largest
log-likelihood *y*. We use the `which.max` function:


``` r
which.max(bc$y)
#> [1] 13
```

Then this gives us the position of the corresponding *λ*:


``` r
lambda <- bc$x[which.max(bc$y)]
lambda
#> [1] -1.52
```

The function reports that the best *λ* is –1.52. In an actual
application, we would urge you to interpret this number and choose the
power that makes sense to you—rather than blindly accepting this “best”
value. Use the graph to assist you in that interpretation. Here, we’ll go
with –1.52.

We can apply the power transform to *y* and then fit the revised model;
this gives a much better *R*^2^ of 0.967:


``` r
z <- y ^ lambda
m2 <- lm(z ~ x)
summary(m2)
#> 
#> Call:
#> lm(formula = z ~ x)
#> 
#> Residuals:
#>     Min      1Q  Median      3Q     Max 
#> -13.459  -3.711  -0.228   2.206  14.188 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept)  -0.6426     1.2517   -0.51     0.61    
#> x             1.0514     0.0205   51.20   <2e-16 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#> 
#> Residual standard error: 5.15 on 89 degrees of freedom
#> Multiple R-squared:  0.967,	Adjusted R-squared:  0.967 
#> F-statistic: 2.62e+03 on 1 and 89 DF,  p-value: <2e-16
```

For those who prefer one-liners, the transformation can be embedded
right into the revised regression formula:


``` r
m2 <- lm(I(y ^ lambda) ~ x)
```

By default, `boxcox` searches for values of *λ* in the range –2 to +2.
You can change that via the `lambda` argument; see the help page for
details.

We suggest viewing the Box–Cox result as a starting point, not as a
definitive answer. If the confidence interval for *λ* includes 1.0, it
may be that no power transformation is actually helpful. As always,
inspect the residuals before and after the transformation. Did they
really improve?

Compare Figure \@ref(fitplot) (transformed data) with Figure \@ref(fitplot1) (no transformation).


``` r
augmented_m2 <- augment(m2)

ggplot(augmented_m2, aes(x = .fitted, y = .resid)) + 
  geom_point()
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/fitplot-1} 

}

\caption{Fitted values versus residuals: m2}(\#fig:fitplot)
\end{figure}


### See Also {-#see_also-id210}

See Recipes \@ref(recipe-id208,) ["Regressing on Transformed Data"](#recipe-id208), and \@ref(recipe-id212),
["Diagnosing a Linear Regression"](#recipe-id212).

Forming Confidence Intervals for Regression Coefficients {#recipe-id211}
--------------------------------------------------------

### Problem {-#problem-id211}

You are performing linear regression and you need the confidence
intervals for the regression coefficients.

### Solution {-#solution-id211}

Save the regression model in an object; then use the `confint` function
to extract confidence intervals:


``` r
load(file = './data/conf.rdata')
m <- lm(y ~ x1 + x2)
confint(m)
#>             2.5 % 97.5 %
#> (Intercept) -3.90   6.47
#> x1          -2.58   6.24
#> x2           4.67   5.17
```

### Discussion {-#discussion-id211}

The Solution uses the model *y* = *β*~0~ + *β*~1~(*x*~1~)~*i*~ +
*β*~2~(*x*~2~)~*i*~ + *ε*~i~. The `confint` function returns the
confidence intervals for the intercept (*β*~0~), the coefficient of
*x*~1~ (*β*~1~), and the coefficient of *x*~2~ (*β*~2~):


``` r
confint(m)
#>             2.5 % 97.5 %
#> (Intercept) -3.90   6.47
#> x1          -2.58   6.24
#> x2           4.67   5.17
```

By default, `confint` uses a confidence level of 95%. Use the `level`
parameter to select a different level:


``` r
confint(m, level = 0.99)
#>             0.5 % 99.5 %
#> (Intercept) -5.72   8.28
#> x1          -4.12   7.79
#> x2           4.58   5.26
```

### See Also {-#see_also-id211}

The `coefplot` function of the `arm` package can plot confidence
intervals for regression coefficients.

Plotting Regression Residuals {#recipe-id216}
-----------------------------

### Problem {-#problem-id216}

You want a visual display of your regression residuals.

### Solution {-#solution-id216}

You can plot the model object by using `broom` to put model results in a data frame, then plot with `ggplot`:


``` r
m <- lm(y ~ x1 + x2)

library(broom)
augmented_m <- augment(m)

ggplot(augmented_m, aes(x = .fitted, y = .resid)) + 
  geom_point()
```

### Discussion {-#discussion-id216}

Using the linear model `m` from the prior recipe, we can create a simply residual plot:


``` r
library(broom)
augmented_m <- augment(m)

ggplot(augmented_m, aes(x = .fitted, y = .resid)) + 
  geom_point()
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/residualplot-1} 

}

\caption{Model residual plot}(\#fig:residualplot)
\end{figure}

The output is shown in Figure \@ref(fig:residualplot). 

You could also use the Base R `plot` method to get a quick peek, but it will produce Base R graphics output, instead of `ggplot`:


``` r
plot(m, which = 1)
```

### See Also {-#see_also-id216}

See Recipe \@ref(recipe-id212), ["Diagnosing a Linear Regression"](#recipe-id212), which contains
examples of residuals plots and other diagnostic plots.

Diagnosing a Linear Regression {#recipe-id212}
------------------------------

### Problem {-#problem-id212}

You have performed a linear regression. Now you want to verify the
model’s quality by running diagnostic checks.

### Solution {-#solution-id212}

Start by plotting the model object, which will produce several
diagnostic plots using Base R graphics:


``` r
m <- lm(y ~ x1 + x2)
plot(m)
```

Next, identify possible outliers either by looking at the diagnostic
plot of the residuals or by using the `outlierTest` function of the
`car` package:


``` r
library(car)
outlierTest(m)
```

Finally, identify any overly influential observations. See Recipe \@ref(recipe-id233), ["Identifying Influential Observations"](#recipe-id233).

### Discussion {-#discussion-id212}

R fosters the impression that linear regression is easy: just use the
`lm` function. Yet fitting the data is only the beginning. It’s your job
to decide whether the fitted model actually works and works well.

Before anything else, you must have a statistically significant model.
Check the *F* statistic from the model summary
(Recipe \@ref(recipe-id240), ["Understanding the Regression Summary"](#recipe-id240)) and be sure that
the *p*-value is small enough for your purposes. Conventionally, it
should be less than 0.05 or else your model is likely not very meaningful. 

Simply plotting the model object produces several useful diagnostic
plots, shown in Figure \@ref(fig:goodfit):


``` r
m <- lm(y ~ x1 + x2)
par(mfrow = (c(2, 2))) # this gives us a 2x2 plot
plot(m)
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/goodfit-1} 

}

\caption{Diagnostics of a good fit}(\#fig:goodfit)
\end{figure}

Figure \@ref(fig:goodfit) shows diagnostic
plots for a pretty good regression:

-   The points in the Residuals vs Fitted plot are randomly scattered
    with no particular pattern.

-   The points in the normal Q–Q plot are more or less on the line,
    indicating that the residuals follow a normal distribution.

-   In both the Scale–Location plot and the Residuals vs Leverage plots,
    the points are in a group with none too far from the center.

In contrast, the series of graphs shown in Figure \@ref(fig:badfit) show
the diagnostics for a not-so-good regression:


``` r
load(file = './data/bad.rdata')
m <- lm(y2 ~ x3 + x4)
par(mfrow = (c(2, 2)))      # this gives us a 2x2 plot
plot(m)
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/badfit-1} 

}

\caption{Diagnostics of a poor fit}(\#fig:badfit)
\end{figure}

Observe that the Residuals
vs Fitted plot has a definite parabolic shape. This tells us that the
model is incomplete: a quadratic factor is missing that could explain
more variation in *y*. Other patterns in residuals would be suggestive of
additional problems: a cone shape, for example, may indicate nonconstant
variance in *y*. Interpreting those patterns is a bit of an art, so we
suggest reviewing a good book on linear regression while evaluating the
plot of residuals.

There are other problems with these not-so-good diagnostics. The normal
Q–Q plot has more points off the line than it does for the good
regression. Both the Scale–Location and Residuals vs Leverage plots show
points scattered away from the center, which suggests that some points
have excessive leverage.

Another pattern is that point number 28 sticks out in every plot. This
warns us that something is odd with that observation. The point could be
an outlier, for example. We can check that hunch with the `outlierTest`
function of the `car` package:


``` r
library(car)
outlierTest(m)
#>    rstudent unadjusted p-value Bonferroni p
#> 28     4.46           7.76e-05       0.0031
```

The `outlierTest` identifies the model’s most outlying observation. In
this case, it identified observation number 28 and so confirmed that it
could be an outlier.

### See Also {-#see_also-id212}

See Recipes \@ref(recipe-id240), ["Understanding the Regression Summary"](#recipe-id240), and \@ref(recipe-id233), ["Identifying Influential Observations"](#recipe-id233). The `car` package
is not part of the standard distribution of R; see Recipe \@ref(recipe-id012), ["Installing Packages from CRAN"](#recipe-id012).

Identifying Influential Observations {#recipe-id233}
------------------------------------

### Problem {-#problem-id233}

You want to identify the observations that are having the most influence
on the regression model. This is useful for diagnosing possible problems
with the data.

### Solution {-#solution-id233}

The `influence.measures` function reports several useful statistics for
identifying influential observations, and it flags the significant ones
with an asterisk (`*`). Its main argument is the model object from your
regression:


``` r
influence.measures(m)
```

### Discussion {-#discussion-id233}

The title of this recipe could be “Identifying *Overly* Influential
Observations,” but that would be redundant. All observations influence
the regression model, even if only a little. When a statistician says
that an observation is influential, it means that removing the
observation would significantly change the fitted regression model. We
want to identify those observations because they might be outliers that
distort our model; we owe it to ourselves to investigate them.

The `influence.measures` function reports several statistics: DFBETAS,
DFFITS, covariance ratio, Cook’s distance, and hat matrix values. If any
of these measures indicate that an observation is influential, the
function flags that observation with an asterisk (`*`) along the
righthand side:


``` r
influence.measures(m)
#> Influence measures of
#> 	 lm(formula = y2 ~ x3 + x4) :
#> 
#>      dfb.1_   dfb.x3   dfb.x4    dffit cov.r   cook.d    hat inf
#> 1  -0.18784  0.15174  0.07081 -0.22344 1.059 1.67e-02 0.0506    
#> 2   0.27637 -0.04367 -0.39042  0.45416 1.027 6.71e-02 0.0964    
#> 3  -0.01775 -0.02786  0.01088 -0.03876 1.175 5.15e-04 0.0772    
#> 4   0.15922 -0.14322  0.25615  0.35766 1.133 4.27e-02 0.1156    
#> 5  -0.10537  0.00814 -0.06368 -0.13175 1.078 5.87e-03 0.0335    
#> 6   0.16942  0.07465  0.42467  0.48572 1.034 7.66e-02 0.1062    
#> 7  -0.10128 -0.05936  0.01661 -0.13021 1.078 5.73e-03 0.0333    
#> 8  -0.15696  0.04801  0.01441 -0.15827 1.038 8.38e-03 0.0276    
#> 9  -0.04582 -0.12089 -0.01032 -0.14010 1.188 6.69e-03 0.0995    
#> 10 -0.01901  0.00624  0.01740 -0.02416 1.147 2.00e-04 0.0544    
#> 11 -0.06725 -0.01214  0.04382 -0.08174 1.113 2.28e-03 0.0381    
#> 12  0.17580  0.35102  0.62952  0.74889 0.961 1.75e-01 0.1406    
#> 13 -0.14288  0.06667  0.06786 -0.15451 1.071 8.04e-03 0.0372    
#> 14 -0.02784  0.02366 -0.02727 -0.04790 1.173 7.85e-04 0.0767    
#> 15  0.01934  0.03440 -0.01575  0.04729 1.197 7.66e-04 0.0944    
#> 16  0.35521 -0.53827 -0.44441  0.68457 1.294 1.55e-01 0.2515   *
#> 17 -0.09184 -0.07199  0.01456 -0.13057 1.089 5.77e-03 0.0381    
#> 18 -0.05807 -0.00534 -0.05725 -0.08825 1.119 2.66e-03 0.0433    
#> 19  0.00288  0.00438  0.00511  0.00761 1.176 1.99e-05 0.0770    
#> 20  0.08795  0.06854  0.19526  0.23490 1.136 1.86e-02 0.0884    
#> 21  0.22148  0.42533 -0.33557  0.64699 1.047 1.34e-01 0.1471    
#> 22  0.20974 -0.19946  0.36117  0.49631 1.085 8.06e-02 0.1275    
#> 23 -0.03333 -0.05436  0.01568 -0.07316 1.167 1.83e-03 0.0747    
#> 24 -0.04534 -0.12827 -0.03282 -0.14844 1.189 7.51e-03 0.1016    
#> 25 -0.11334  0.00112 -0.05748 -0.13580 1.067 6.22e-03 0.0307    
#> 26 -0.23215  0.37364  0.16153 -0.41638 1.258 5.82e-02 0.1883   *
#> 27  0.29815  0.01963 -0.43678  0.51616 0.990 8.55e-02 0.0986    
#> 28  0.83069 -0.50577 -0.35404  0.92249 0.303 1.88e-01 0.0411   *
#> 29 -0.09920 -0.07828 -0.02499 -0.14292 1.077 6.89e-03 0.0361    
etc ...
```

This is the model from Recipe \@ref(recipe-id212), ["Diagnosing a Linear Regression"](#recipe-id212),
where we suspected that observation 28 was an outlier. An asterisk is
flagging that observation, confirming that it’s overly influential.

This recipe can identify influential observations, but you shouldn’t
reflexively delete them. Some judgment is required here. Are those
observations improving your model or damaging it?

### See Also {-#see_also-id233}

See Recipe \@ref(recipe-id212), ["Diagnosing a Linear Regression"](#recipe-id212). Use
`help(influence.measures)` to get a list of influence measures and some
related functions. See a regression textbook for interpretations of the
various influence measures.

Testing Residuals for Autocorrelation (Durbin–Watson Test) {#recipe-id213}
----------------------------------------------------------

### Problem {-#problem-id213}

You have performed a linear regression and want to check the residuals
for autocorrelation.

### Solution {-#solution-id213}

The Durbin–Watson test can check the residuals for autocorrelation. The
test is implemented by the `dwtest` function of the `lmtest` package:


``` r
library(lmtest)
m <- lm(y ~ x)           # Create a model object
dwtest(m)                # Test the model residuals
```

The output includes a *p*-value. Conventionally, if *p* &lt; 0.05 then
the residuals are significantly correlated, whereas *p* &gt; 0.05
provides no evidence of correlation.

You can perform a visual check for autocorrelation by graphing the
*autocorrelation function* (ACF) of the residuals:


``` r
acf(m)                   # Plot the ACF of the model residuals
```

### Discussion {-#discussion-id213}

The Durbin–Watson test is often used in time series analysis, but it was
originally created for diagnosing autocorrelation in regression
residuals. Autocorrelation in the residuals is a scourge because it
distorts the regression statistics, such as the *F* statistic and the
*t* statistics for the regression coefficients. The presence of
autocorrelation suggests that your model is missing a useful predictor
variable or that it should include a time series component, such as a
trend or a seasonal indicator.

This first example builds a simple regression model and then tests the
residuals for autocorrelation. The test returns a *p*-value well above zero,
which indicates that there is no significant autocorrelation:


``` r
library(lmtest)
#> Loading required package: zoo
#> 
#> Attaching package: 'zoo'
#> The following objects are masked from 'package:base':
#> 
#>     as.Date, as.Date.numeric
load(file = './data/ac.rdata')
m <- lm(y1 ~ x)
dwtest(m)
#> 
#> 	Durbin-Watson test
#> 
#> data:  m
#> DW = 2, p-value = 0.4
#> alternative hypothesis: true autocorrelation is greater than 0
```

This second example exhibits autocorrelation in the residuals. The
*p*-value is near zero, so the autocorrelation is likely positive:


``` r
m <- lm(y2 ~ x)
dwtest(m)
#> 
#> 	Durbin-Watson test
#> 
#> data:  m
#> DW = 2, p-value = 0.01
#> alternative hypothesis: true autocorrelation is greater than 0
```

By default, `dwtest` performs a one-sided test and answers this
question: Is the autocorrelation of the residuals greater than zero? If
your model could exhibit negative autocorrelation (yes, that is
possible), then you should use the `alternative` option to perform a
two-sided test:


``` r
dwtest(m, alternative = "two.sided")
```

The Durbin–Watson test is also implemented by the `durbinWatsonTest`
function of the `car` package. We suggested the `dwtest` function primarily
because we think the output is easier to read.

### See Also {-#see_also-id213}

Neither the `lmtest` package nor the `car` package is included in
the standard distribution of R; see Recipes \@ref(recipe-id013), ["Accessing the Functions in a Package"](#recipe-id013), and \@ref(recipe-id012), ["Installing Packages from CRAN"](#recipe-id012). See Recipes \@ref(recipe-id082), ["Plotting the Autocorrelation Function"](#recipe-id082), and \@ref(recipe-id084),
["Finding Lagged Correlations Between Two Time Series"](#recipe-id084), for more regarding tests of autocorrelation.

Predicting New Values {#recipe-id214}
---------------------

### Problem {-#problem-id214}

You want to predict new values from your regression model.

### Solution {-#solution-id214}

Save the predictor data in a data frame. Use the `predict` function,
setting the `newdata` parameter to the data frame:


``` r
load(file = './data/pred2.rdata')

m <- lm(y ~ u + v + w)
preds <- data.frame(u = 3.1, v = 4.0, w = 5.5)
predict(m, newdata = preds)
#>  1 
#> 45
```

### Discussion {-#discussion-id214}

Once you have a linear model, making predictions is quite easy because
the `predict` function does all the heavy lifting. The only annoyance is
arranging for a data frame to contain your data.

The `predict` function returns a vector of predicted values with one
prediction for every row in the data. The example in the Solution
contains one row, so `predict` returned one value.

If your predictor data contains several rows, you get one prediction per
row:


``` r
preds <- data.frame(
  u = c(3.0, 3.1, 3.2, 3.3),
  v = c(3.9, 4.0, 4.1, 4.2),
  w = c(5.3, 5.5, 5.7, 5.9)
)
predict(m, newdata = preds)
#>    1    2    3    4 
#> 43.8 45.0 46.3 47.5
```

In case it’s not obvious: the new data needn’t contain values for
response variables, only predictor variables. After all, you are trying
to *calculate* the response, so it would be unreasonable of R to expect
you to supply it.

### See Also {-#see_also-id214}

These are just the point estimates of the predictions. See Recipe \@ref(recipe-id215),
["Forming Prediction Intervals"](#recipe-id215) for the confidence
intervals.

Forming Prediction Intervals {#recipe-id215}
----------------------------

### Problem {-#problem-id215}

You are making predictions using a linear regression model. You want to
know the prediction intervals: the range of the distribution of the
prediction.

### Solution {-#solution-id215}

Use the `predict` function and specify `interval = "prediction"`:


``` r
predict(m, newdata = preds, interval = "prediction")
```

### Discussion {-#discussion-id215}

This is a continuation of Recipe \@ref(recipe-id214), ["Predicting New Values"](#recipe-id214), which
described packaging your data into a data frame for the `predict`
function. We are adding `interval = "prediction"` to obtain prediction
intervals.

Here is the example from Recipe \@ref(recipe-id214), ["Predicting New Values"](#recipe-id214), now
with prediction intervals. The new `lwr` and `upr` columns are the lower
and upper limits, respectively, for the interval:


``` r
predict(m, newdata = preds, interval = "prediction")
#>    fit  lwr  upr
#> 1 43.8 38.2 49.4
#> 2 45.0 39.4 50.7
#> 3 46.3 40.6 51.9
#> 4 47.5 41.8 53.2
```

By default, `predict` uses a confidence level of 0.95. You can change
this via the `level` argument.

A word of caution: these prediction intervals are extremely sensitive to
deviations from normality. If you suspect that your response variable is
not normally distributed, consider a nonparametric technique, such as
the bootstrap (see Recipe \@ref(recipe-id159), ["Bootstrapping a Statistic"](#recipe-id159)), for prediction intervals.

Performing One-Way ANOVA {#recipe-id218}
------------------------

### Problem {-#problem-id218}

Your data is divided into groups, and the groups are normally
distributed. You want to know if the groups have significantly different
means.

### Solution {-#solution-id218}

Use a factor to define the groups. Then apply the `oneway.test`
function:


``` r
oneway.test(x ~ f)
```

Here, `x` is a vector of numeric values and `f` is a factor that
identifies the groups. The output includes a *p*-value. Conventionally,
a *p*-value of less than 0.05 indicates that two or more groups have
significantly different means, whereas a value exceeding 0.05 provides no
such evidence.

### Discussion {-#discussion-id218}

Comparing the means of groups is a common task. One-way ANOVA performs
that comparison and computes the probability that they are statistically
identical. A small *p*-value indicates that two or more groups likely
have different means. (It does *not* indicate that *all* groups have
different means.)

The basic ANOVA test assumes that your data has a normal distribution or
that, at least, it is pretty close to bell-shaped. If not, use the
Kruskal–Wallis test instead
(see Recipe \@ref(recipe-id228), ["Performing Robust ANOVA (Kruskal–Wallis Test)"](#recipe-id228)).

We can illustrate ANOVA with stock market historical data. Is the stock
market more profitable in some months than in others? For instance, a
common folk myth says that October is a bad month for stock market
investors.[^october] We explored this question by creating a data frame `GSPC_df` containing two columns, `r` and `mon`. The factor `r` is the daily returns in the
Standard & Poor’s 500 index, a broad measure of stock market
performance. The factor `mon` indicates the calendar month in which
that change occurred: Jan, Feb, Mar, and so forth. The data covers the
period 1950 though 2009.

The one-way ANOVA shows a *p*-value of 0.03347:


``` r
load(file = './data/anova.rdata')
oneway.test(r ~ mon, data = GSPC_df)
#> 
#> 	One-way analysis of means (not assuming equal variances)
#> 
#> data:  r and mon
#> F = 2, num df = 11, denom df = 6723, p-value = 0.03
```

We can conclude that stock market changes varied significantly according
to the calendar month.

Before you run to your broker and start flipping your portfolio monthly,
however, we should check something: did the pattern change recently? We
can limit the analysis to recent data by specifying a `subset`
parameter. This works for `oneway.test` just as it does for the `lm`
function. The `subset` contains the indexes of observations to be
analyzed; all other observations are ignored. Here, we give the indexes
of the 2,500 most recent observations, which is about 10 years of data:


``` r
oneway.test(r ~ mon, data = GSPC_df, subset = tail(seq_along(r), 2500))
#> 
#> 	One-way analysis of means (not assuming equal variances)
#> 
#> data:  r and mon
#> F = 0.7, num df = 11, denom df = 976, p-value = 0.8
```

Uh-oh! Those monthly differences evaporated during the past 10 years.
The large *p*-value, 0.8, indicates that changes have not recently
varied according to calendar month. Apparently, those differences are a
thing of the past.

Notice that the `oneway.test` output says “(not assuming equal
variances)”. If you know the groups have equal variances, you’ll get a
less conservative test by specifying `var.equal = TRUE`:


``` r
oneway.test(x ~ f, var.equal = TRUE)
```

You can also perform one-way ANOVA by using the `aov` function like
this:


``` r
m <- aov(x ~ f)
summary(m)
```

However, the `aov` function always assumes equal variances and so is
somewhat less flexible than `oneway.test`.

### See Also {-#see_also-id218}

If the means are significantly different, use Recipe \@ref(recipe-id220),
["Finding Differences Between Means of Groups"](#recipe-id220), to see the
actual differences. Use Recipe \@ref(recipe-id228),
["Performing Robust ANOVA (Kruskal–Wallis Test)"](#recipe-id228), if your
data is not normally distributed, as required by ANOVA.

Creating an Interaction Plot {#recipe-id221}
----------------------------

### Problem {-#problem-id221}

You are performing multiway ANOVA: using two or more categorical
variables as predictors. You want a visual check of possible interaction
between the predictors.

### Solution {-#solution-id221}

Use the `interaction.plot` function:


``` r
interaction.plot(pred1, pred2, resp)
```

Here, `pred1` and `pred2` are two categorical predictors and `resp` is
the response variable.

### Discussion {-#discussion-id221}

ANOVA is a form of linear regression, so ideally there is a linear
relationship between every predictor and the response variable. One
source of nonlinearity is an *interaction* between two predictors: as
one predictor changes value, the other predictor changes its
relationship to the response variable. Checking for interaction between
predictors is a basic diagnostic.

The `faraway` package contains a dataset called `rats`. In it, `treat`
and `poison` are categorical variables and `time` is the response
variable. When plotting `poison` against `time` we are looking for
straight, parallel lines, which indicate a linear relationship. However,
using the `interaction.plot` function produces Figure \@ref(fig:interactionplot), which reveals that something is not
right:


``` r
library(faraway)
data(rats)
interaction.plot(rats$poison, rats$treat, rats$time)
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/interactionplot-1} 

}

\caption{Interaction plot example}(\#fig:interactionplot)
\end{figure}

Each line graphs
`time` against `poison`. The difference between lines is that each line
is for a different value of `treat`. The lines should be parallel, but
the top two are not exactly parallel. Evidently, varying the value of
`treat` “warped” the lines, introducing a nonlinearity into the
relationship between `poison` and `time`.

This signals a possible interaction that we should check. For this data
it just so happens that yes, there is an interaction, but no, it is not
statistically significant. The moral is clear: the visual check is
useful, but it’s not foolproof. Follow up with a statistical check.

### See Also {-#see_also-id221}

See Recipe \@ref(recipe-id206), ["Performing Linear Regression with Interaction Terms"](#recipe-id206).

Finding Differences Between Means of Groups {#recipe-id220}
-------------------------------------------

### Problem {-#problem-id220}

Your data is divided into groups, and an ANOVA test indicates that the
groups have significantly different means. You want to know the
differences between those means for all groups.

### Solution {-#solution-id220}

Perform the ANOVA test using the `aov` function, which returns a model
object. Then apply the `TukeyHSD` function to the model object:


``` r
m <- aov(x ~ f)
TukeyHSD(m)
```

Here, `x` is your data and `f` is the grouping factor. You can plot the
`TukeyHSD` result to obtain a graphical display of the differences:


``` r
plot(TukeyHSD(m))
```

### Discussion {-#discussion-id220}

The ANOVA test is important because it tells you whether or not the
groups’ means are different. But the test does not identify *which*
groups are different, and it does not report their differences.

The `TukeyHSD` function can calculate those differences and help you
identify the largest ones. It uses the “honest significant differences”
method invented by John Tukey.

We’ll illustrate `TukeyHSD` by continuing the example from Recipe \@ref(recipe-id218), ["Performing One-Way ANOVA"](#recipe-id218), which grouped daily stock
market changes by month. Here, we group them by weekday instead,
using a factor called `wday` that identifies the day of the week (Mon,
..., Fri) on which the change occurred. We’ll use the first 2,500
observations, which roughly cover the period from 1950 to 1960:


``` r
load(file = './data/anova.rdata')
oneway.test(r ~ wday, subset = 1:2500, data = GSPC_df)
#> 
#> 	One-way analysis of means (not assuming equal variances)
#> 
#> data:  r and wday
#> F = 13, num df = 4, denom df = 1243, p-value = 5e-10
```

The *p*-value is essentially zero, indicating that average changes
varied significantly depending on the weekday. To use the `TukeyHSD`
function, we first perform the ANOVA test using the `aov` function, which
returns a model object, and then apply the `TukeyHSD` function to the
object:


``` r
m <- aov(r ~ wday, subset = 1:2500, data = GSPC_df)
TukeyHSD(m)
#>   Tukey multiple comparisons of means
#>     95% family-wise confidence level
#> 
#> Fit: aov(formula = r ~ wday, data = GSPC_df, subset = 1:2500)
#> 
#> $wday
#>              diff       lwr       upr p adj
#> Mon-Fri -0.003153 -4.40e-03 -0.001911 0.000
#> Thu-Fri -0.000934 -2.17e-03  0.000304 0.238
#> Tue-Fri -0.001855 -3.09e-03 -0.000618 0.000
#> Wed-Fri -0.000783 -2.01e-03  0.000448 0.412
#> Thu-Mon  0.002219  9.79e-04  0.003460 0.000
#> Tue-Mon  0.001299  5.85e-05  0.002538 0.035
#> Wed-Mon  0.002370  1.14e-03  0.003605 0.000
#> Tue-Thu -0.000921 -2.16e-03  0.000314 0.249
#> Wed-Thu  0.000151 -1.08e-03  0.001380 0.997
#> Wed-Tue  0.001072 -1.57e-04  0.002300 0.121
```

Each line in the output table includes the difference between the means
of two groups (`diff`) as well as the lower and upper bounds of the
confidence interval (`lwr` and `upr`) for the difference. The first line
in the table, for example,compares the Mon group and the Fri group: the
difference of their means is 0.003 with a confidence interval of
(–0.0044 –0.0019).

Scanning the table, we see that the Wed–Mon comparison had the largest
difference, which was 0.00237.

A cool feature of `TukeyHSD` is that it can display these differences
visually, too. Simply plot the function’s return value to get output, as shown in Figure \@ref(fig:tukeyhsd).



``` r
plot(TukeyHSD(m))
```

\begin{figure}

{\centering \includegraphics[width=0.85\linewidth]{11_LinearRegression_files/figure-latex/tukeyhsd-1} 

}

\caption{TukeyHSD Plot}(\#fig:tukeyhsd)
\end{figure}

The horizontal lines plot the
confidence intervals for each pair. With this visual representation you
can quickly see that several confidence intervals cross over zero,
indicating that the difference is not necessarily significant. You can
also see that the Wed–Mon pair has the largest difference because their
confidence interval is farthest to the right. 

### See Also {-#see_also-id220}

See Recipe \@ref(recipe-id218), ["Performing One-Way ANOVA"](#recipe-id218).

Performing Robust ANOVA (Kruskal–Wallis Test) {#recipe-id228}
---------------------------------------------

### Problem {-#problem-id228}

Your data is divided into groups. The groups are not normally
distributed, but their distributions have similar shapes. You want to
perform a test similar to ANOVA—you want to know if the group medians
are significantly different.

### Solution {-#solution-id228}

Create a factor that defines the groups of your data. Use the
`kruskal.test` function, which implements the Kruskal–Wallis test.
Unlike the ANOVA test, this test does not depend upon the normality of
the data:


``` r
kruskal.test(x ~ f)
```

Here, `x` is a vector of data and `f` is a grouping factor. The output
includes a *p*-value. Conventionally, *p* &lt; 0.05 indicates that there
is a significant difference between the medians of two or more groups,
whereas *p* &gt; 0.05 provides no such evidence.

### Discussion {-#discussion-id228}

Regular ANOVA assumes that your data has a normal distribution. It can
tolerate some deviation from normality, but extreme deviations will
produce meaningless *p*-values.

The Kruskal–Wallis test is a nonparametric version of ANOVA, which means
that it does not assume normality. However, it does assume same-shaped
distributions. You should use the Kruskal–Wallis test whenever your data
distribution is nonnormal or simply unknown.

The null hypothesis is that all groups have the same median. Rejecting
the null hypothesis (with *p* &lt; 0.05) does not indicate that *all*
groups are different, but it does suggest that two or more groups are
different.

One year, Paul taught Business Statistics to 94 undergraduate students. The
class included a midterm examination, and there were four homework
assignments prior to the exam. He wanted to know: What is the
relationship between completing the homework and doing well on the exam?
If there is no relation, then the homework is irrelevant and needs
rethinking.

He created a vector of grades, one per student, and he also created a parallel
factor that captured the number of homework assignments completed by
that student. The data is in a data frame named `student_data`:


``` r
load(file = './data/student_data.rdata')
head(student_data)
#> # A tibble: 6 x 4
#>   att.fact hw.mean midterm hw   
#>   <fct>      <dbl>   <dbl> <fct>
#> 1 3          0.808   0.818 4    
#> 2 3          0.830   0.682 4    
#> 3 3          0.444   0.511 2    
#> 4 3          0.663   0.670 3    
#> 5 2          0.9     0.682 4    
#> 6 3          0.948   0.954 4
```

Notice that the `hw` variable—although it appears to be numeric—is
actually a factor. It assigns each midterm grade to one of five groups
depending upon how many homework assignments the student completed.

The distribution of exam grades is definitely not normal: the students
have a wide range of math skills, so there are an unusual number of A
and F grades. Hence, regular ANOVA would not be appropriate. Instead we
used the Kruskal–Wallis test and obtained a *p*-value of essentially
zero (4 × 10^–5^, or 0.00004):


``` r
kruskal.test(midterm ~ hw, data = student_data)
#> 
#> 	Kruskal-Wallis rank sum test
#> 
#> data:  midterm by hw
#> Kruskal-Wallis chi-squared = 26, df = 4, p-value = 4e-05
```

Obviously, there is a significant performance difference between
students who complete their homework and those who do not. But what
could Paul actually conclude? At first, Paul was pleased that the homework
appeared so effective. Then it dawned on him that this was a classic
error in statistical reasoning: he assumed that correlation implied
causality. It does not, of course. Perhaps strongly motivated students
do well on both homework and exams, whereas lazy students do not. In that
case, the causal factor is degree of motivation, not the brilliance of
Paul's homework selection. In the end, he could only conclude something very
simple: students who complete the homework will likely do well on the
midterm exam, but he still doesn't really know why.

Comparing Models by Using ANOVA {#recipe-id232}
-------------------------------

### Problem {-#problem-id232}

You have two models of the same data, and you want to know whether they
produce different results.

### Solution {-#solution-id232}

The `anova` function can compare two models and report if they are
significantly different:


``` r
anova(m1, m2)
```

Here, `m1` and `m2` are both model objects returned by `lm`. The output
from `anova` includes a *p*-value. Conventionally, a *p*-value of less
than 0.05 indicates that the models are significantly different, whereas
a value exceeding 0.05 provides no such evidence.

### Discussion {-#discussion-id232}

In Recipe \@ref(recipe-id231), ["Getting Regression Statistics"](#recipe-id231), we used the `anova`
function to print the ANOVA table for one regression model. Now we are
using the two-argument form to compare two models.

The `anova` function has one strong requirement when comparing two
models: one model must be contained within the other. That is, all the
terms of the smaller model must appear in the larger model. Otherwise,
the comparison is impossible.

The ANOVA analysis performs an *F* test that is similar to the *F* test
for a linear regression. The difference is that this test is between two
models, whereas the regression *F* test is between using the regression
model and using no model.

Suppose we build three models of `y`, adding terms as we go:


``` r
load(file = './data/anova2.rdata')
m1 <- lm(y ~ u)
m2 <- lm(y ~ u + v)
m3 <- lm(y ~ u + v + w)
```

Is `m2` really different from `m1`? We can use `anova` to compare them,
and the result is a *p*-value of 0.0091:


``` r
anova(m1, m2)
#> Analysis of Variance Table
#> 
#> Model 1: y ~ u
#> Model 2: y ~ u + v
#>   Res.Df RSS Df Sum of Sq    F Pr(>F)   
#> 1     18 197                            
#> 2     17 130  1      66.4 8.67 0.0091 **
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

The small *p*-value indicates that the models are significantly
different. Comparing `m2` and `m3`, however, yields a *p*-value of
0.055:


``` r
anova(m2, m3)
#> Analysis of Variance Table
#> 
#> Model 1: y ~ u + v
#> Model 2: y ~ u + v + w
#>   Res.Df RSS Df Sum of Sq    F Pr(>F)  
#> 1     17 130                           
#> 2     16 103  1      27.5 4.27  0.055 .
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

This is right on the edge. Strictly speaking, it does not pass our
requirement to be smaller than 0.05; however, it’s close enough that you
might judge the models to be “different enough.”

This example is a bit contrived, so it does not show the larger power of
`anova`. We use `anova` when, while experimenting with complicated models
by adding and deleting multiple terms, we need to know whether or not the
new model is really different from the original one. In other words: if
we add terms and the new model is essentially unchanged, then the extra
terms are not worth the additional complications.

[^zeromean]: Unless you performed the linear regression without an intercept
    term
    (see Recipe \@ref(recipe-id205), ["Performing Linear Regression Without an Intercept"](#recipe-id205)).

[^significant]: The significance level of *α* = 0.05 is the convention observed in
    this book. Your application might instead use *α* = 0.10, *α* =
    0.01, or some other value. See the Introduction to Chapter \@ref(GeneralStatistics).

[^october]: In the words of Mark Twain, “October: This is one of the
    peculiarly dangerous months to speculate in stocks in. The others
    are July, January, September, April, November, May, March, June,
    December, August, and February.”
