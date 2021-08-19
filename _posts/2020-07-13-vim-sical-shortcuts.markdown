---
layout: post
title:  "vim-sical shortcuts"
date:   2020-07-13 09:40:00 +0200
categories: vim
---

## Background
I just can't remember those darn vim shortcuts...

## there you go...

### file nav
* `gg` - jump to start of file
* `Shift G` - jump to end of file 

### line nav
* `$` - jump to end of line
* `0` - jump to start of line

### editing
* `:[range]s/{pattern}/{string}/[flags] [count]` - Search and Replace, general form, default range is the current line. Userful range `%` for whole file,  useful flags `g` (global) for all occurences, `c` (confirm) to ask the user for each replace
* `:%s/foo/bar/gc` - Search and Replace in the whole document `foo` with `bar` but ask everytime the user

### file ops
* `:edit` or `:e` plus file name - open file for editing

### scren splitting
* `:split` or `:sp`  - split screen horizontal
* `:vsplit` or `:vsp` - split screen vertical
* `[Ctrl] + w [arrow keys]` - jump between split screens