---
layout: post
title:  "Deep Flattens a List in Python"
date:   2019-9-15 16:00:00
categories: Algorithm python
permalink: /archivers/DeepFlatten
comments: true
---
将一个嵌套的List降维.
展成一维List.
例如:

`[1, [2, [3], [4, [5, [6]]]], 0]`  

👉  `[1, 2, 3, 4, 5, 6, 0]`
<!--more-->
## **思路**
使用**递归**遍历`list`.

* 定义一个`spread`函数，使用`list.extend()`和`list.append()`可以将一层嵌套展开.

* 递归调用`spread()`，逐层展开`list`里的每一个元素.

## **代码**
```python
def spread(arg):
    ret = []
    for i in arg:
        if isinstance(i, list):
            ret.extend(i)
        else:
            ret.append(i)
    return ret
    
def deep_flatten(lst):
    result = []
    result.extend(
        spread(list(map(lambda x: deep_flatten(x) if type(x) == list else x, lst))))
    return result

a = [1, [2, [3], [4, [5, [6]]]], 0]
print(deep_flatten(a))
```

## **参考**
[deep_flatten[https://github.com/30-seconds/30-seconds-of-python]](https://github.com/30-seconds/30-seconds-of-python#deep_flatten)