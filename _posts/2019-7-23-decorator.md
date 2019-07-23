---
layout: post
title:  "Decorator in python"
date:   2019-7-22 17:00:00
categories: python
permalink: /archivers/decorator
---

简单来说，可以把装饰器理解为一个包装函数的函数，它一般将传入的函数或者是类做一定的处理，
返回修改之后的对象．

所以能够在不修改原函数的基础上，在执行原函数前后执行别的代码．
<!--more-->
## **装饰器**

比如，计算某一个函数的执行时间，可以在该函数的开始和结尾获取时间，然后相减即可.

但是这样做降低了代码的复用性和可读性.

如果将计算时间差的功能独立为一个函数，然后将需要计算执行时间的函数传入，
这样在需要计算某函数的执行时间时就很方便了.
***(在Python中函数也是被视为对象的，可以作为参数传递.)***

实现如下:
```python
from time import time, sleep

def add(x, y):
    sleep(0.5)
    return x + y

def timer(func, *args, **kwargs):
    start_t = time()
    result = func(*args, **kwargs)
    end_t = time()
    print("result = {}, spend {}s.".format(result, end_t - start_t))

timer(add, 1, 2)
```
但是这个写法需要把`add()`和它的参数分开，都作为`timer()`函数的参数传入.

**把函数和他的参数拆开，不太好.**

因此，可以在`timer()`里将传入的`func`函数包装一层，返回包装后的函数`wrapper`.
以此达到与上例中`timer(add, 1, 2)`一样的效果:
```python
def add(x, y):
    sleep(0.5)
    return x + y

def timer(func):
    def wrapper(*args, **kwargs):
        start_t = time()
        result = func(*args, **kwargs)
        end_t = time()
        print("result = {}, spend {}s.".format(result, end_t - start_t))
    return wrapper

f = timer(add)
f(1, 2)
```

## **语法糖**
上例需要先得到将`func()`包装了的`wrapper()`函数，然后传值.

这里可以通过一个语法糖`@`符号来使用装饰器.
```python
def timer(func):
    def wrapper(*args, **kwargs):
        start_t = time()
        result = func(*args, **kwargs)
        end_t = time()
        print("result = {}, spend {}s.".format(result, end_t - start_t))
    return wrapper

@timer
def add(x, y):
    sleep(0.5)
    return x + y

add(1, 2)
```
通常也是用这种方法来使用装饰器.
