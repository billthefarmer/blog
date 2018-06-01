---
title: Java Collections
author: Bill Farmer
type: post
date: 2014-11-01T12:18:28+00:00
url: /java-collections/
categories:
  - Hacking
keywords:
  - java
  - android
  - collections
  - list
  - map
  - arraylist
---
I find Java Collections frustrating and insane. I have been through this loop on several occasions. I decide I want a list to do whatever. So I look up [List][1] in the docs and find it&rsquo;s an interface, ie: roll yer own. So I look down the hierarchy and find [AbstractList][2], which is abstract, again: roll yer own. So finally I get to [ArrayList][3] which might do the job, but the docs aren&rsquo;t too clear, so I try it and it works. I just want a list that will do the job. I don&rsquo;t care what it stores the data in, that level of detail is supposed to be obscured and possibly subject to change anyway.

So why can&rsquo;t a List be a list that you can actually do something useful with? I don&rsquo;t get this sort of nonsense with other languages. An AbstractList is fine, it is what it is. It can&rsquo;t be that difficult to dream up a suitable name for a list like interface that isn&rsquo;t just List.

#### Update

Having written several android apps utilising java [Collections][4]
objects, one way to deal with this issue is to declare all variables
as an interface &ndash; [List][1], [Map][5], or [Set][6], both when
initially declared and in function parameters, etc, and use the
particular implementation you want when you create a new one. I
haven't found this suggestion in the docs anywhere.

```java
    private List<String> list;
    private Map<String, Integer> map;
    private Set<String> set;

    // onCreate
    @Override
    public void onCreate()
    {
        list = new ArrayList<String>();
        map = new LinkedHashMap<String, Integer>();
        set = new ArraySet<String>();
        // ...
    }

    // Get map
    public Map<String, Integer> getMap()
    {
        return map;
    }
```

 [1]: http://developer.android.com/reference/java/util/List.html
 [2]: http://developer.android.com/reference/java/util/AbstractList.html
 [3]: http://developer.android.com/reference/java/util/ArrayList.html
 [4]: http://developer.android.com/reference/java/util/Collection.html
 [5]: http://developer.android.com/reference/java/util/Map.html
 [6]: http://developer.android.com/reference/java/util/Set.html
