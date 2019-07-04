---
title: "Introspection in Python"
date: 2010-11-03
draft: false
toc: false
images:
tags:
  - programming
  - python
---

I just finished reading a [chapter about Introspection](http://diveintopython.org/power_of_introspection/index.html) in the [book that I’m reading](http://diveintopython.org/). Learning the concept of introspection and doing it in Python is the trickiest challenge I’ve come across so far.

[Introspection](http://en.wikipedia.org/wiki/Introspection_%28computer_science%29), by definition, is determining and manipulating objects at runtime. The idea is that we know that there are objects that will definitely come across our program during run-time. We don’t know what they are exactly, but we want to do something about them in case our program encounters them.

I still don’t fully understand Introspection, at the moment, but I’m pretty sure I already did this a lot of times in Java. I was just unaware that I was already doing some form of Introspection back then. Anyway, here’s the sample code that I worked on during the chapter:

```python
# apihelper.py

def info(object, spacing = 10, collapse = 1):
  """Print methods and doc strings.

  Takes module, class, list, dictionary, or string."""
  methodList = [method for method in dir(object) if callable(getattr(object, method))]
  processFunc = collapse and (lambda s: " ".join(s.split())) or (lambda s: s)
  print "\n".join(["%s %s" %
            (method.ljust(spacing),
            processFunc(str(getattr(object, method).__doc__)))
          for method in methodList])

if __name__ == "__main__":
  print info.__doc__
```

What this code does is print the doc string of all the callable methods of the object it encounters. Here’s a demonstration of the code being used in the interpreter through the CLI:

```shell
>>> from apihelper import info
>>> li = []
>>> info(li)
append L.append(object) — append object to end
count L.count(value) -> integer — return number of occurrences of value
extend L.extend(list) — extend list by appending list elements
index L.index(value) -> integer — return index of first occurrence of value
insert L.insert(index, object) — insert object before index
pop L.pop([index]) -> item — remove and return item at index (default last)
remove L.remove(value) — remove first occurrence of value
reverse L.reverse() — reverse *IN PLACE*
sort L.sort([cmpfunc]) — sort *IN PLACE*; if given, cmpfunc(x, y) -> -1, 0, 1
```

A *list* (li) object was declared first and was used as an argument for the function info(). Take note that only 1 argument was passed inside (li) and the other arguments in the function, **info**, are [optional arguments](/posts/2010/11/01/optional-arguments-a-gift-from-heaven/). Since the object passed inside the function was a *list* object, the program printed all the list object’s callable methods along with their respective *doc strings*.

Yes, I know the code is pretty intimidating to look at. It should be because it’s that complicated. That’s why I’ll be going over the code line per line to understand how it works.

```python
methodList = [method for method in dir(object) if callable(getattr(object, method))]
```

First of all, an extended [list comprehension](http://docs.python.org/tutorial/datastructures.html#list-comprehensions) is used to filter out all the non-callable methods of the object and the result will be assigned to the declared list object, **methodList**. This is achieved by using the built-in functions, [**getattr()**](http://docs.python.org/library/functions.html#getattr) and [**callable()**](http://docs.python.org/library/functions.html#callable).

I had trouble understanding **getattr()** at first so I asked [this question](http://stackoverflow.com/q/4075190/395972) on [StackOverflow](http://stackoverflow.com/). To be honest, I didn’t really know what I failed to understand about it, specifically, so my question didn’t have clear answers. It only became clear to me after reading more about it.

Anyway, as I understand **getattr()**, it gets 3 arguments: the object, name of the method in string and a default value, which by the way, is an optional argument. It then returns the corresponding method of the name specified, so calling **getattr(foo, “bar”)** returns *bar*, which is the method of **foo**. Also, calling that is the same way as calling **foo**.bar.

Next is **callable()**, it gets 1 method as its argument. It then checks if the method is a callable method or not. It returns True if the method is callable, False if otherwise. A good example of a non-callable method is **string.punctuation**. It’s a method but it isn’t callable because it’s technically a string.

What the line of code (mentioned above) does is get the object’s list of attributes and methods through **dir()**, iterate through each method or attribute using list comprehension, then check if the object’s attribute or method is callable. If it is, append it to **methodList**, otherwise, filter it out.

Now that we got that line of code out of the way, let’s move on to the next one:

```python
processFunc = collapse and (lambda s: " ".join(s.split())) or (lambda s: s)
```

If you look at it closely, you’ll see that an [and-or Trick](http://diveintopython.org/power_of_introspection/and_or.html#d0e9975) was used to assign a value to processFunc. Another new keyword that you’ll see here is [**lambda**](http://docs.python.org/tutorial/controlflow.html#lambda-forms). It lets you define functions in an inline fashion. Anyway, a detailed explanation of **lambda** can be found [here](http://diveintopython.org/power_of_introspection/lambda_functions.html).

Now that we’re already familiar with what **lambda** is, let’s disect some of the logic found in that line of code:

```python
(lambda s: " ".join(s.split()))
```

What this does is take an argument, **s** (the attribute or method), and remove the excess whitespace it has. **split()** removes all the whitespace then place all the elements inside a list. **join()** concatenates all the elements of the list and separate them with a single whitespace. Here’s a demo to elaborate more on the subject:

```shell
>>> s = “this is\na\ttest”
>>> print s
this is
a	test
>>> print s.split()
['this', 'is', 'a', 'test']
>>> print ” “.join(s.split())
‘this is a test’
```

The second half of the line is pretty simple:

```python
(lambda s: s)
```

It just returns the argument passed into it.

Now that we’re already familiar with parts of the line, it’s time to understand the line as a whole:

```python
processFunc = collapse and (lambda s: " ".join(s.split())) or (lambda s: s)
```

In case you forgot, `collapse` is an argument passed into the function, **info**. Remember I mentioned how this is an implementation of the [and-or Trick](http://diveintopython.org/power_of_introspection/and_or.html#d0e9975)? Well, the idea is to have collapse accept one of two values, 0 and 1. If the value is 1, perform `(lambda s: ” “.join(s.split()))`, otherwise, perform `(lambda s: s)`. If the first function was performed, the `doc string` that was passed will be formatted. If the second function was performed, it’ll leave it as it is.

We are finally at the last line of the code. Let’s take a look:

```python
print "\n".join(["%s %s" %
          (method.ljust(spacing),
          processFunc(str(getattr(object, method).__doc__)))
        for method in methodList])
```

This line should be familiar by now. Well, except for the method, **ljust()**. Anyway, this line of code is the bread and butter of the function, **info**. Without this, it wouldn’t be printing anything in the screen.

First of all, there are 2 big parts to this line of code: the function, **join()** and a *list comprehension*. The *list comprehension* is then subdivided into 2 parts: the formatting and the looping. Since we’re already familiar with what **join()** does, let’s move on to the formatting part of the *list comprehension*:

```python
"%s %s" % (method.ljust(spacing), processFunc(str(getattr(object, method).__doc__)))
```

Basically, what this code does is place the method name along with its corresponding doc string in a single line. Now onto the specifics:

```python
method.ljust(spacing)
```

This is a new function introduced to us by the book, **ljust()**. What this function does is add padding to its string argument. It uses whitespace to add padding. I’m pretty sure it’s a shorthand name for *left-justify* since it adds padding to the right of the string only. Now that we got that out of the way, let’s move on to the next one:

```python
processFunc(str(getattr(object, method).__doc__))
```

I forgot to mention that using **lambda** and assigning it to a variable turns that variable into a function. That’s the reason why you’ll see *processFunc* being used as a function in the line above even though we declared it as a variable earlier.

If you look back at earlier line now, you’ll see that it all makes sense now. We used **lambda** and an [and-or Trick](http://diveintopython.org/power_of_introspection/and_or.html#d0e9975) to assign 1 of 2 functions to **processFunc**. It’s a pretty cool way to avoid making too much non-reusable functions. Anyway, let’s divide the line even further so understand it better:

```python
getattr(object, method).__doc__
```

Now that we understand what **getattr()** does, this should be pretty simple. What this code does is get the method of the current object in the iteration. After getting the method, it gets its corresponding *doc string*.

```python
str(getattr(object, method).__doc__)
```

This is pretty much the same as the code before. The only difference is that the function, **str()**, is present. **str()**, just like **int()**, **float()** and other type casting functions, will convert its argument into a string object. Since a *doc string* isn’t really a string, we’re converting it into one so we can manipulate it later.

I’ll just skip on the looping part since I already explained how *list comprehensions* work earlier. Now that we already got the specifics down, let’s go back to the big picture:

```python
print "\n".join(["%s %s" %
          (method.ljust(spacing),
          processFunc(str(getattr(object, method).__doc__)))
        for method in methodList])
```

So after getting the method name, its *doc string*, and putting them in one line, “\n”.**join()** will separate them with a line break.

In conclusion, what it does is pretty simple but the process is complicated. It took me a while to understand the big picture but it was worth it.

If you’re still reading this, I commend you for taking your time to read this really long and boring post. Now celebrate by getting [rickrolled](http://www.youtube.com/watch?v=dQw4w9WgXcQ&ob=av3e).
