---
layout: post
title:  "Embarrassingli-sh..."
date:   2019-10-10 09:40:00 +0200
categories: shell bash cli
---

## Background
There are almost daily some tasks from which I know that they are easily scriptable. On a good day, I google them, on a bad day, I embarrassingly fix it manually. This entry is the trial to improve by at least writing down those that I need the most often in the hope that I will remember them...

## Run git commands on a bunch of sub-directories
```
find . -type d -depth 1 -print -exec git --git-dir={}/.git --work-tree=$PWD/{} status \;
find . -type d -depth 1 -print -exec git --git-dir={}/.git --work-tree=$PWD/{} fetch \;
find . -type d -depth 1 -ok git --git-dir={}/.git --work-tree=$PWD/{} pull origin master \;
```

## Change file extensions of multiple files in a directory
Here for example, change .txt files to .fcs files
```
for f in *.txt; do mv -- "$f" "${f%.fcs}.txt"; done
```

## Change file pattern of multiple files in a directory
```
ls * | sed -e 'p;s/foo/bar/' | xargs -n2 mv
```




