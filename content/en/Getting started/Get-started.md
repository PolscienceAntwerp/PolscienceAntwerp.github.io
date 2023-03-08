---
title: Getting started with flempar
author: ["Evelien Willems", "Frederik Heylen"]

# Summary for listings and search engines
summary: Your gateway to the Flemish parliament data

# Link this post with a project
projects: []

# Date published
date: '2022-09-25T00:00:00Z'

# Date updated
lastmod: '2022-09-07T00:00:00Z'

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false


# tags:
#  - Academic
#  - 开源

# categories:
#  - Demo
#  - 教程
---

## Download and installation

You can install the `flempar` package from the GitHub account of the Political Science Department of the University of Antwerp, <https://github.com/PolscienceAntwerp/flempar>. **Mac users** will also need to install [Xcode](https://apps.apple.com/be/app/xcode/id497799835?mt=12). 

```r
require(devtools) 
install_github("PolscienceAntwerp/flempar") 
```

We then load the `flempar` package along with the [tidyverse](https://www.tidyverse.org/packages/) suite of R packages to manipulate the data and visualize some results. Additionally, we load in `data.table`, making working with data frames easier (more info [here](https://cran.r-project.org/web/packages/data.table/vignettes/datatable-intro.html)). 

```r
library(flempar)

library(dplyr) 
library(tidyr) 
library(tibble) 
library(ggplot2) 
library(stringr)

library(data.table)
```

In case you don't have these extra R-packages already installed. Run the following code first, *then* run the above chunk of code.

```r
install.packages("dplyr") 
install.packages("tidyr") 
install.packages("tibble") 
install.packages("ggplot2") 

install.packages("data.table")
```

## First try out

Now, let's test whether the installation worked! You can try running the `get_mp()` function with its default options, specifying no parameters. This delivers you the demographics of the current MPs. At the moment of writing this blog post, this comprises all MPs seating in the 2019-2024 legislature.

```r
get_mp()
```

When making calls, you get feedback about the number of calls that will be made. Depending on the number of calls, this might take a couple of seconds to several minutes. If the call succeeds, the exact time needed to do so gets displayed.

In this regard, the build-in `use_parallel=TRUE` argument is one of the main advantages of using our `flempar` package. This functionality ensures that instead of serial processing (i.e. one batch after the other), your computer is told to divide the work among its processing cores to execute the work in parallel (i.e. simultaneously); which significantly speeds up the data retrieving process. So, in almost all instances, you will want keep the default-setting `use_parallel=TRUE`, which makes it unnecessary to explicitely specificy this as a parameter in the function.

Example of feedback:

```r
Making 124 calls.
Made 124 calls in 143.7 seconds.  
```

What you get is a simple overview of the data. This is how the output looks like:

{{< figure src="/images/DefaultOutput_get_mp.png" title="Default output `get_mp()`" >}}

So on to the `get_work()`function. In contrast to the `get_mp` function, you *always* need to specify a date range using the following arguments:

* date_range_from: the start date YYYY-MM-DD
* date_range_to: the end date YYYY-MM-DD

As an enormous amount of data can be retrieved through the `get_work()` function, it is highly **recommended to test your query on a limited date range** before expanding the date range to comprise multiple months to years. As no other parameters are specified, you end up with the default data, namely info about plenary debates. 

```r
get_work(date_range_from="2022-03-01"
         , date_range_to="2022-03-15")
```
Again, you get some performance feedback about the calls being made and a first rough look at the output.

**That's it, you've now covered the basics of the `flempar` package!**


