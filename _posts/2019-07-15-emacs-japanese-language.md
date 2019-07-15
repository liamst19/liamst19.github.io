---
layout: post
title: Showing Japanese characters in emacs
date: 2019-07-15
categories: 
---

While working on the [org-notebook-mode](https://github.com/liamst19/org-notebook-mode) minor mode for emacs I noticed that opening text files with a large number of Japanaese characters caused massive lag in emacs. Upon searching around for solutions it seemed that [this is a bug in the Windows version](https://lists.gnu.org/archive/html/bug-gnu-emacs/2016-11/msg00482.html) having to do with rendering some non-alphabetic TrueType fonts.

The workaround (via [Stack Exchange](https://emacs.stackexchange.com/questions/33510/unicode-txt-slowness)) is to set the following somewhere in the init file:

```
(setq inhibit-compacting-font-caches t)
```

Although with this emacs still feels sluggish at times, at least it's now usable.
