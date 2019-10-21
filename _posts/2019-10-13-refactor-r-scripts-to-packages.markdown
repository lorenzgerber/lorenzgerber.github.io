---
layout: post
title:  "Refactor R Scripts into Packages"
date:   2019-10-13 09:40:00 +0200
categories: R package script Rstudio
---

## Background
R Scripts are a convenient way to quickly sketch and automatize data analysis. However, for a larger scale
data analysis platforms, scripts are not suitable as they are not formally testable with common unit test 
packages. Also they tend to grow in size with substantial amount of code duplication  often without a clear 
and logic structure that is more easily enforced when code is setup in a package.  

### Current Setup
In the current project, a data analysis pipeline consists mainly of two files:
* runner script, ment to be used with Rscript from the command line. This one is mostly used for parsing and validating input parameters
* functions, file with usually one to two large functions, often well over 1000 lines of code.

The package dependencies are usually worked into the script to be installed when not available. This spreads out
dependency management all over the code. 

Further, there is a Dockerfile and a shellscript to be used as entrypoint in the docker Image. Additionally, 
a 'buildspec.yml' used for the AWS CI/CD platform and some markdown documentation. 

### Target Setup
The target setup for a package contains only the CI/CD 'buildspec.yml' file and the R package directory in the 
repositorie's base directory. The build target of here is not a Docker image but a R Package. The docker build is
now separated and taken care of by a separate generic build script. 

The runnerscript shall be moved within the R pacakge structure into a directory 'Package-name/inst/Rscript/runnerScript.R'.
As a script, it can ot reside in the packages source code directory but it is still beneficial to have it bundeled
somewhere within the package.

Dependencies are controlled by the usual R package mechanisms.


## Step by Step from Script to Package
Initial work is done as refactoring the current script into a better state for lifting it into package structures.
* create a 'prepare_environment.R' that contains/handles:
  * logger
  * setting ENV variables
  * loading/installing packages
* fix variable names according style convetion
* prefix all used functions with the package name, except for base
* separate code into functions with clear descriptive function names
  * add roxygen headers for each function
* make/keep a main function that shows clearly the high-level steps of the data analysis pipeline
* move all other fuctions in one or many thematic coherent files
  * Functions should not be more than ca. 250 lines of code
    




