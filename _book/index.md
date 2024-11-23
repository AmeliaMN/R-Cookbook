--- 
title: "R Cookbook, 2nd Edition"
author:
- James (JD) Long
- Paul Teetor
date: "2024-11-22"
output: bookdown::gitbook
description: Second edition of R Cookbook
documentclass: book
github-repo: CerebralMastication/R-Cookbook
link-citations: yes
mainfont: Arial
monofont: Courier New
monofontoptions: Scale=0.7
bibliography:
- book.bib
- packages.bib
site: bookdown::bookdown_site
biblio-style: apalike
---

# Welcome to the R Cookbook 2nd Edition {-}

<a href="https://amzn.to/2LCthli"><img src="images_v2/book_cover.jpg" width="250" height="327" alt="R Cookbook 2nd Edition Cover image" align="right" style="margin: 0 1em 0 1em" /></a> 

R is a powerful tool for statistics, graphics, and statistical
programming. It is used by tens of thousands of people daily to perform
serious statistical analyses. It is a free, open source system whose
implementation is the collective accomplishment of many intelligent,
hard-working people. There are more than 10,000 available add-on packages, and R
is a serious rival to all commercial statistical packages.

But R can be frustrating. It’s not obvious how to accomplish many tasks,
even simple ones. The simple tasks are easy once you know how, yet
figuring out that “how” can be maddening.

This book is full of how-to recipes, each of which solves a specific
problem. The recipe includes a quick introduction to the solution
followed by a discussion that aims to unpack the solution and give you
some insight into how it works. We know these recipes are useful and we
know they work, because we use them ourselves.

The range of recipes is broad. It starts with basic tasks before moving
on to input and output, general statistics, graphics, and linear
regression. Any significant work with R will involve most or all of
these areas.

If you are a beginner, then this book will get you started faster. If you
are an intermediate user, this book is useful for expanding your
horizons and jogging your memory (“How do I do that Kolmogorov–Smirnov
test again?”).

The book is not a tutorial on R, although you will learn something by
studying the recipes. It is not a reference manual, but it does contain
a lot of useful information. It is not a book on programming in R,
although many recipes are useful inside R scripts.

Finally, this book is not an introduction to statistics. Many recipes
assume that you are familiar with the underlying statistical procedure,
if any, and just want to know how it’s done in R.

The Recipes {-}
-----------

Most recipes use one or two R functions to solve a specific problem.
It’s important to remember that we do not describe the functions in
detail; rather, we describe just enough to solve the immediate problem.
Nearly every such function has additional capabilities beyond those
described here, and some have amazing capabilities. We strongly urge you
to read the function’s help page. You will likely learn something
valuable.

Each recipe presents one way to solve a particular problem. Of course,
there are likely several reasonable solutions to each problem. When we
knew of multiple solutions, we generally selected the simplest one. For
any given task, you can probably discover several alternative solutions
yourself. This is a cookbook, not a bible.

In particular, R has literally thousands of downloadable add-on
packages, many of which implement alternative algorithms and statistical
methods. This book concentrates on the core functionality available
through the basic distribution combined with several important packages known
collectively as the *tidyverse*.

The [most concise definition of the tidyverse comes from Hadley Wickham](https://blog.rstudio.com/2016/09/15/tidyverse-1-0-0/), its 
originator and one of its core maintainers:

> The tidyverse is a set of packages that work in harmony because they share 
> common data representations and API design. The tidyverse package is designed 
> to make it easy to install and load core packages from the tidyverse in a 
> single command. The best place to learn about all the packages in the 
> tidyverse and how they fit together is *[R for Data Science](http://r4ds.had.co.nz)*.

A Note on Terminology {-}
------------------------

The goal of every recipe is to solve a problem and solve it quickly.
Rather than laboring in tedious prose, we occasionally streamline the
description with terminology that is correct but not precise. A good
example is the term *generic function*. We refer to `print(x)` and
`plot(x)` as generic functions because they work for many kinds of `x`,
handling each kind appropriately. A computer scientist would wince at our
terminology because, strictly speaking, these are not simply
“functions”; they are polymorphic methods with dynamic dispatching. But
if we carefully unpacked every such technical detail, the essential
solution would be buried in the technicalities. So we just call them
functions, which we think is more readable.

Another example, taken from statistics, is the complexity surrounding
the semantics of statistical hypothesis testing. Using the strict
language of probability theory would obscure the practical application
of some tests, so we use more colloquial language when describing each
statistical test. See the introduction to Chapter \@ref(GeneralStatistics) for more about how hypothesis tests are
presented in the recipes.

Our goal is to make the power of R available to a wide audience by
writing readably, not formally. We hope that experts in their respective
fields will understand if our terminology is occasionally informal.

Software and Platform Notes {-}
---------------------------

The base distribution of R has frequent and planned releases, but the
language definition and core implementation are stable. The recipes in
this book should work with any recent release of the base distribution.

Some recipes have platform-specific considerations, and we have carefully
noted them. Those recipes mostly deal with software issues, such as
installation and configuration. As far as we know, all other recipes will
work on all three major platforms for R: Windows, macOS, and Linux/Unix.

Other Resources {-}
---------------

On the web

:   The mothership for all things R is the [R project
    site](http://www.r-project.org). From there you can download
    R for your platform, add-on packages, documentation, and source code as well as
    many other resources.

    Beyond the R project site, we recommend using an R-specific search
    engine, such as [RSeek](http://rseek.org), created by Sasha Goodman.
    You can use a generic search engine, such as Google, but the “R”
    search term brings up too much extraneous stuff. See Recipe \@ref(recipe-id005),
    "Searching the Web for Help" for more about searching the web.

    Reading blogs is a great way to learn about R and stay abreast of
    leading-edge developments. There are surprisingly many such blogs,
    so we recommend following two blog-of-blogs:
    [R-bloggers](http://www.r-bloggers.com/), created by Tal Galili, and
    [PlanetR](http://planetr.stderr.org/). By subscribing to their RSS
    feeds, you will be notified of interesting and useful articles from
    dozens of websites.

R books

:   There are many, many books about learning and using R.
    Listed here are a few that we have found useful. Note that the R project site
    contains an [extensive bibliography of books related to
    R](http://www.r-project.org/doc/bib/R-books.html).

    [*R for Data Science*](http://shop.oreilly.com/product/0636920034407.do), by
    Hadley Wickham and Garrett Grolemund (O’Reilly), is an excellent introduction
    to the tidyverse packages, especially for using them in data
    analysis and statistics.
    It is also available online at <http://r4ds.had.co.nz>.

    We find the *[R Graphics Cookbook, 2nd ed.](http://shop.oreilly.com/product/0636920063704.do)*, by Winston Chang (O'Reilly) is
    indispensible for creating graphics.
    The book *ggplot2: Elegant Graphics for Data Analysis* by Hadley Wickham (Springer)
    is the definitive reference for the graphics package `ggplot2`,
    which we use in this book.
    
    Anyone doing serious graphics work in R will want *R Graphics* by Paul Murrell (Chapman & Hall/CRC).

    [*R in a Nutshell*](http://oreilly.com/catalog/9780596801717), by
    Joseph Adler (O’Reilly), is the quick tutorial and reference you’ll
    keep by your side. It covers many more topics than this cookbook.

    New books on programming in R appear regularly.
    We suggest [*Hands On Programming with R*](https://www.oreilly.com/library/view/hands-on-programming-with/9781449359089/) by Garrett Grolemund (O'Reilly) for an introduction,
    or *The Art of R Programming* by Normal Matloff (No Starch Press).
    Hadley Wickham's *Advanced R Programming* is available either as a printed book or [free online](http://adv-r.had.co.nz/)
    and is a great deeper dive into advanced R topics. 
    [*Efficient R Programming*](http://shop.oreilly.com/product/0636920047995.do), by Colin Gillespie and Robin Lovelace (O'Reilly),
    is another good guide to learning the deeper concepts about R programming.
    
    *Modern Applied Statistics with S, 4th ed.*, by William Venables and
    Brian Ripley (Springer), uses R to illustrate many advanced
    statistical techniques. The book’s functions and datasets are
    available in the `MASS` package, which is included in the standard
    distribution of R.
    
    Serious geeks can download the
    [R Language Definition](http://cran.r-project.org/doc/manuals/R-lang.pdf) from the R Core Team.
    The Definition is a work in progress, but it can answer many of your
    detailed questions regarding R as a programming language.

Statistics books

:   For learning statistics, a great choice is *Using R for Introductory Statistics*
    by John Verzani (Chapman & Hall/CRC).
    It teaches statistics and R together, giving you the necessary computer skills
    to apply the statistical methods.

    You will need a good statistics textbook or reference book to
    accurately interpret the statistical tests performed in R. There are
    many such fine books—far too many for us to recommend any one above
    the others.

    Increasingly, statistics authors are using R to illustrate
    their methods. If you work in a specialized field, then you will
    likely find a useful and relevant book in the [R project
    bibliography](http://www.r-project.org/doc/bib/R-books.html).

Conventions Used in This Book {-}
-----------------------------

The following typographical conventions are used in this book:

Italic

:   Indicates new terms, URLs, email addresses, filenames, and
    file extensions.

`Constant width`

:   Used for program listings, as well as within paragraphs to refer to
    program elements such as variable or function names, databases, packages, data
    types, environment variables, statements, and keywords.

**`Constant width bold`**

:   Shows commands or other text that should be typed literally by
    the user.

*`Constant width italic`*

:   Shows text that should be replaced with user-supplied values or by
    values determined by context.

> **Tip**
>
> This icon signifies a tip, suggestion, or general note.

> **Caution**
>
> This icon indicates a warning or caution.

Using Code Examples {-}
-------------------

Supplemental material (code examples, source code for the book, exercises, etc.) is available for download at (http://rc2e.com). The Twitter account for content associated with this book is [\@R_cookbook](https://twitter.com/R_cookbook). 

This book is here to help you get your job done. In general, you may use
the code in this book in your programs and documentation. You do not
need to contact us for permission unless you’re reproducing a
significant portion of the code. For example, writing a program that
uses several chunks of code from this book does not require permission.
Selling or distributing a CD-ROM of examples from O’Reilly books does
require permission. Answering a question by citing this book and quoting
example code does not require permission. Incorporating a significant
amount of example code from this book into your product’s documentation
does require permission.

We appreciate, but do not require, attribution. An attribution usually
includes the title, author, publisher, and ISBN. For example: “*R
Cookbook*, 2nd edition, by JD Long and Paul Teetor. Copyright 2019 JD Long and Paul Teetor,
978-1-492-04068-2.” 

If you feel your use of code examples falls outside fair use or the
permission just described, feel free to contact us at
<permissions@oreilly.com>.


Safari® Books Online {-}
--------------------

> **Note**
>
> For almost 40 years, O’Reilly Media has provided technology and business training, 
> knowledge, and insight to help companies succeed.

Our unique network of experts and innovators share their knowledge and expertise through books, articles, conferences, and our online learning platform. O’Reilly’s online learning platform gives you on-demand access to live training courses, in-depth learning paths, interactive coding environments, and a vast collection of text and video from O'Reilly and 200+ other publishers. For more information, please visit (http://oreilly.com).  


How to Contact Us {-}
-----------------

Please address comments and questions concerning this book to the
publisher:

O’Reilly Media, Inc.
1005 Gravenstein Highway North
Sebastopol, CA 95472
800-998-9938 (in the United States or Canada)
707-829-0515 (international or local)
707-829-0104 (fax)
We have a web page for this book, where we list errata, examples, and
any additional information. You can access this page at:

To comment or ask technical questions about this book, send email to:

bookquestions@oreilly.com
For more information about our books, courses, conferences, and news,
see our website at [http://oreilly.com](http://oreilly.com).

Find us on Facebook: [facebook.com/oreilly](http://facebook.com/oreilly)

Follow us on Twitter: [twitter.com/oreillymedia](http://twitter.com/oreillymedia)

Watch us on YouTube: [www.youtube.com/oreillymedia](http://www.youtube.com/oreillymedia)

Acknowledgments {-}
---------------

With gratitude we thank the R community in general and the R Core Team in
particular. Their selfless contributions are enormous. The world of
statistics is benefiting tremendously from their work. The R Studio Community Discussion participants were very helpful in workshopping ideas around how to explain many things. And the staff and leadership of R Studio were supportive in so many little and big ways. We owe them a debt of gratitude for all they have given back to the R community.

We wish to thank the book’s technical reviewers: David Curran, Justin Shea, and
MAJ Dusty Turner. Their feedback was critical
for improving the quality, accuracy, and usefulness of this book. Our editors, Melissa Potter and Rachel Monaghan, were helpful beyond imagination and they
frequently prevented us from publicly demonstrating our ignorance. Our production editor, Kristen Brown, is the envy of all technical authors because of her speed and her proficiency with both markdown and git. 

Paul would like to thank his family for their support and patience during the creation of this book.

JD would like to thank his wife Mary Beth and daughter Ada for their patience with all the early mornings and weekends that JD spent with his face in the laptop working on this book. 
