---
layout: post
title:  "Find and Replace in Files Recursively"
date:   2018-11-11 09:34:28 +0800
categories: console bash grep xargs sed osx snippet
---

This snippet is one of several ways to find and replace words or strings in multiple files. 

`grep -rl 'Fickel Fritze' . | xargs sed -i '' 's/Fickel Fritze/Frickel Fritze/g'`

Here grep returns the filenames of files in the current directory and recursively below. Flag *-r* results in recursively visiting files starting at the current directory. The *-l* flag causes grep to output only filenames instead of the matched line. 

xargs reads input from stdin, which here is piped from grep, and iteratively applied to a  command to the right of xargs. In the current example, the filenames are applied to sed.

Sed is run with the flag *-i* (in-place edit). While in most sed versions, the *-i* flag takes an optional parameter of a file extenstion to make file backups before in-place modificiation, this entry is mandatory on all to me known OSX versions (here currently 10.14.1 / Mojave). If no backups are desired, an empty string *''* has to be provided for the syntax to be correct.  