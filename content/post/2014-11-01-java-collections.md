---
title: Java Collections
author: Bill Farmer
type: post
date: 2014-11-01T12:18:28+00:00
url: /java-collections/
categories:
  - Hacking

---
I find Java Collections frustrating and insane. I have been through this loop on several occasions. I decide I want a list to do whatever. So I look up <a href="http://developer.android.com/reference/java/util/List.html" target="_blank">List </a>in the docs and find it&#8217;s an interface, ie: roll yer own. So I look down the hierarchy and find <a href="http://developer.android.com/reference/java/util/AbstractList.html" target="_blank">AbstractList</a>, which is abstract, again: roll yer own. So finally I get to <a href="http://developer.android.com/reference/java/util/ArrayList.html" target="_blank">ArrayList </a>which might do the job, but the docs aren&#8217;t too clear, so I try it and it works. I just want a list that will do the job. I don&#8217;t care what it stores the data in, that level of detail is supposed to be obscured and possibly subject to change anyway.

So why can&#8217;t a List be a list that you can actually do something useful with? I don&#8217;t get this sort of nonsense with other languages. An AbstractList is fine, it is what it is. It can&#8217;t be that difficult to dream up a suitable name for a list like interface that isn&#8217;t just List.