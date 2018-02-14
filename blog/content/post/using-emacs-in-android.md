---
title: "Using Emacs in Android Development"
author: Bill Farmer
type: post
date: 2017-10-18T15:51:28+01:00
url: /using-emacs-in-android-development/
categories:
  - Hacking
keywords:
  - emacs
  - sort
  - fill
  - android
---

I've got a folder full of country flags which I have renamed using
scripts so the names correspond to currency symbols. I want an integer
array with references to the flags as android resources. This can be
automated using emacs.

I get a list of the file names into a buffer using find, which I then
open with emacs.

```shell
  % find src/ -name flag* > data/sym-flags.lst
```

![Flags](images/2017/10/flags.png)

They are already sorted, but just to make sure I use `M-x sort-lines`.

![Sort](images/2017/10/sort.png)

I need to replace the path with `R.drawable.`, so I use `M-% replace
string`. There is a bulk replace command in the docs somewhere, but it
is easier to press '!' than look it up.

![Replace](images/2017/10/replace.png)

Same again, to get rid of the `.png` and replace with a comma.

![Replace png](images/2017/10/replace-png.png)

I want the list in a neat block, so `M-q` to fill the notional
paragraph.

![Fill](images/2017/10/fill.png)

It's then trivial to copy the list to your code and wrap it with an
array declaration.

![Fill](images/2017/10/java.png)
