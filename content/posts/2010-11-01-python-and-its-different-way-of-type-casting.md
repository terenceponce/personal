---
title: "Python and its different way of type casting"
date: 2010-11-01T12:00:10+08:00
draft: false
toc: false
images:
tags:
  - programming
  - python
---

I tried to make a Fibonacci program in Python to experiment with getting user input, displaying output and type casting. This was the code that I came up with at first:

```python
def fib(n):
    print 'n =', n
    if n > 1:
        return fib(n - 1) + fib(n - 2)
    else:
        print 'end of the line'
        return 1

n = raw_input('Input number: ')
int(n)
fib(n)
```

When I tried running the program in the CLI, I have been successful in getting the user input. Unfortunately, it came out with an exception when it tried to run the **fib** function:

```shell
Input number: 5
n = 5
Traceback (most recent call last):
File “fibonacci.py”, line 11, in
fib(n)
File “fibonacci.py”, line 4, in fib
return n * fib(n – 1)
TypeError: unsupported operand type(s) for -: ‘str’ and ‘int’
```

I was dumbfounded when I came across this exception because I was certain that I casted the user input from String to Integer already with this:

```python
int(n)
```

Since I didn’t know what caused the exception anymore, I decided to ask [this question](http://stackoverflow.com/q/4066905/395972) on [StackOverflow](http://stackoverflow.com/). It turns out, the function, **int(n)**, returns the integer equivalent of its parameter without changing its original value. It’s kind of like pointers in C where you can manipulate the referenced value without affecting the original value to which it’s pointed to.

If I did the same thing with Java, it’ll automatically assign the type casted value as the parameter’s new value. I’m used to how Java does it but I think Python has a more practical way of doing it.

One of the people who answered my question suggested a different approach to my code and why it should be that way. I’d like to quote him on [his answer](http://stackoverflow.com/questions/4066905/python-newbie-question-i-cant-figure-out-what-my-problem-is-exactly/4066959#4066959):

> "The problem is that raw_input is providing a string, not an integer. Can I suggest just putting the integer value into n in the first place:
>
> n = int(raw_input(‘Input numver: ‘))
> fib(n)

> Avoid n = int(n) as in a longer section of code it would be unclear when you came back to it what type n is, and you have no need for the original string value.
>
> The deeper understanding you need is that Python is strongly typed – it cares what type everything is, but it is also dynamically typed, so n could change from holding a string value to holding an integer value. But Python is still keeping track of what is held, so when you put the return value of raw_input into n, Python knows it is a string, so multiplying that string by a number makes no sense and returns an error."

After reading the solutions to my problem and the suggestions that they made, I came up with this solution:

```python
def fib(n):
  print 'n =', n
  if n > 1:
    return fib(n - 1) + fib(n - 2)
  else:
    print 'end of the line'
    return 1

n = int(raw_input('Input number: '))
print "Fibonacci of %d is %d" % (n, fib(n))
```

I also did a bit of string formatting/concatenation on the last line of the code. This is the simplest and cleanest code I could come up with. I’m beginning to love the cleanliness of Python’s syntax. I’m still getting used to not using indentations instead of curly braces though.
