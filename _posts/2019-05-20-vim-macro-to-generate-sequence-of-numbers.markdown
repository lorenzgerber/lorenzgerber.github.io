---
layout: post
title:  "vim macro to generate sequence of numbers"
date:   2019-05-20 09:34:28 +0800
categories: vim
---

# Macro's in vim
I have to admit that up to now, I was never using macros in vim and I neither saw the need for using them. Macro reminded me somehow of a dark past in ms-office products that I rather want to forget ;)

## Problem at hand: Create a tab separated sequence of increasing integers in a text file
While I immediately turned to vim, it took some quality google time to realize that macros could be the solution. Then the path to a working macro was pretty straight forward. I record the step for one yank/paste/increase step and apply it with the required number of repetition as macro to a number.
Macro recording in vim starts with `q` followed by another character to name the macro. Recording ends by pressing again `q`.

A small issue that had to be solved was yanking of whole, multi digit numbers. Basically, one uses the 'yank word' `yw` command. However, for this to work properly, the cursor should be positioned on the left most digit of the number. So the actual problem is to move to the first character of a word, independent whether the word has one or many characters (or in this case digits).
A viable solution is the *visual select* modus: The combination `viwo<esc>` will do the trick to select the whole multi digit number.

## The full sequence of key strokes to record the macro
```
qq
viwo<Esc>
yw
$
a
c-v
<Tab>
<Esc>
p
c-a
q
```

## Applying the macro
If a sequence of numbers from 1 to 20 has to be generated, initially a `1` is inserted. With the cursor standing on the `1`, `19@q` is then entered. 

This will apply the macro `q` 19 times:
```
1   2   3   4   5   6   7  8   9   10  11   12  13  14  15  16  17  18  19  20
```
