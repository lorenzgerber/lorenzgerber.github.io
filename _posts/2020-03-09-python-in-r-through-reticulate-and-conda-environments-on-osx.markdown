---
layout: post
title:  "Python in R through Reticulate and Conda Environments on OSX"
date:   2020-03-09 09:40:00 +0200
categories: R Python conda reticulate
---

## Background
Conda is a package manager for Python. Miniconda is the no-frills-small-footprint version with no pre-installed packages. One of the most useful features of Conda are the so called 'conda environments'. Environments are conda managed directories that contain complete python installations with specific python versions and packages. Packages can be installed using both conda and pip. The conda cli has commands to create, populate, activate, deactivate and remove environments. Many current IDEs (eg. visual studio code) have built-in support for managing conda environments and allow as such hassle-free managment of dependencies from various python projects.

## Using Python and Conda in R through Reticualte
Reticulate is a package that provides an interface to Python code from R. Among others, it also supports creating, managing and using conda environments. Reticulate even offers the possibility to download the miniconda installer and make the installation from within R. This is however not the author's choice as it is prefered to manage as many installations as possible on the mac using homebrew package manager. Hence below, miniconda is installed using homebrew.


## Step by Step Guide to use with R reticulate
Installing miniconda using homebrew
* shell: `brew cask install miniconda`
* shell: `conda init zsh`
* shell: `conda config --set auto_activate_base false`
* restart shell

Installation of Reticulate
* open RStudio
* R: `install.packages('reticulate')`

The following command will return the python interpreter to use
* R: `reticulate::conda_create("r-reticulate")`
* Shell: `conda activate r-reticulate`

Install a specific Python package (here 'phate')
* Shell: `pip install phate`

Everytime when using a package that depends on reticulate, here 'highdim'
* R: `reticulate::use_condaenv('r-reticulate')`
* R: `library(highdim)`

## Useful Conda CLI commands
* `conda info` - general installation infos
* `conda activate envname` - activate conda environment 'envname'
* `conda deactivate` - deactivate current conda environment
* `conda list env` - list conda environments
* `conda list` - list available packages within a conda env
* `conda search "^python$"` - list available python versions
* `conda create -n newenvname python=3.8` - create new conda environment 'newenvname' with python 3.8  installed
* `conda remove -n newenvname --all` - remove conda environment 'newenvname' and all it's installed packages



