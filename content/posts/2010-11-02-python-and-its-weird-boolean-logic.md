---
title: "Python and its weird boolean logic"
date: 2010-11-02
draft: false
toc: false
images:
tags:
  - programming
  - python
---

I found some pretty interesting keywords in Python: ***and*** and ***or***. Coming from a C background, this is somehow new to me since I’m used to using symbolic operators for boolean logic (&& and ||). One of the things I noticed was how Python interprets these keywords:

```python
'a' and 'b'
```

It returns ‘b’. Surprised? I know I am. One look and I immediately thought it would return something similar to True since both values aren’t null.

A few more examples. I took them from [here](http://diveintopython.org/power_of_introspection/and_or.html):

```python
' ' and 'b'
```

This returns ‘ ‘. Apparently, **and** returns the first *False* value.

```python
'a' and 'b' and 'c'
```

This returns ‘**c**’. If all values are true, **and** returns the last True value.

Aside from **and**, there’s another keyword that I mentioned earlier - **or**:

```python
'a' or 'b'
```

If ‘a’ ***and*** ‘b’ returned ‘b’, this should return ‘a’, right? It does. I was just trying to mess with your head. ***or*** returns the first *True* value it encounters.

I’m not going to paste all of the examples so just read the rest [here](http://diveintopython.org/power_of_introspection/and_or.html).

The reason why I find the keywords weird is because of their return values. They return the values compared instead of returning the usual *True* or *False* value. Well, of course Python has *If-Else-statements* as well, but I guess people needed another way of comparing values that they made those keywords to suit their needs.
