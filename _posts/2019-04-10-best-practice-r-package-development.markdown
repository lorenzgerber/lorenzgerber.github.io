---
layout: post
title:  "Best practice R package development"
date:   2019-04-10 16:45:28 +0800
categories: data-science R CI/CD
---

## What
In this post a collection of links and tutorials related to good
practice R package design is presented.

### Code Style
A recent up-to-date and well maintained code style document can be found on 
<https://style.tidyverse.org> from Hadley Wickham.

### Starting a new package
#### The `usethis` package
The use this package facilitates package development greatly. It is loaded
in the development environment and then used from the command line. 

#### The `testthat`package
The use of this package can be automated through the the above mentioned `usethis` package:
- `usethis::use_test("test_name")`