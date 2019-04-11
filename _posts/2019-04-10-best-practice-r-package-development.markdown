---
layout: post
title:  "Best practice R package development"
date:   2019-04-10 16:45:28 +0800
categories: data-science R CI/CD
---

In this post a collection of links and tutorials related to good
practice R package design is presented.

## Code Style
A recent up-to-date and well maintained code style document can be found on <https://style.tidyverse.org> from Hadley Wickham.

## General setup
*RStudio* is very well adapted for interactive data analysis but also for package development. For the
latter, especially the `Build` tab in the (default) upper right window is convenient. The icon bar over
the window gives access to many regualarly used build commands while the window itself shows the build
process output. On the right side of the icon bar, the current active project is visible. For most benefits
of RStudio (automated package builds etc) it is advisible to use the RStudio project feature. 

## Starting a new package
### The `usethis` package 
<https://usethis.r-lib.org/reference/use_vignette.html>  
The use this package facilitates package development greatly. It is loaded in the development environment and then used from the command line. Below a number of relevant commands:
- `use_r(name = NULL)` create a new R File in the package structure
- `use_package(package, type = "Imports", min_version = NULL)` adds CRAN dependency to DESCRIPTION file
- `use_vignette(name, title = name)`

### The `testthat`package
<https://testthat.r-lib.org/>  
The use of this package can be automated through the the above mentioned `usethis` package:
- `usethis::use_test("test_name")`

### The `roxygen2` package
<https://cran.r-project.org/web/packages/roxygen2/vignettes/roxygen2.html>  
Roxygen (here always refers to the `roxygen2` package) is used for automatically buidling the
documentation. Further, by using the `@export` tag in the function documentation header, 
it allows to automate the generation of the NAMESPACE document.

Below a simple example example of a roxygen2 function/meathod docstring
header with a simplified function body:
```
#' heatplot
#'
#' creates the heatplot for cytographer
#'
#' @import data.table
#' @export
heatplot <- function(){
    doheatplot()
}
```
