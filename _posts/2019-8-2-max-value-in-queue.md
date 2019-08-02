---
layout: post
title:  "Max Value in Queue"
date:   2019-8-1 21:00:00
categories: Algorithm
permalink: /archivers/MaxValueInQueue
---
## **问题**
假设有这样一个拥有3个操作的队列：
1. EnQueue(v): 将v加入队列中
2. DeQueue(): 使队列中的队首元素删除并返回此元素
3. MaxElement: 返回队列中的最大元素

设计一种数据结构和算法，让MaxElement操作的时间复杂度尽可能地低。
<!--more-->
* * *
## **思路**
### **栈**
**可以先考虑栈的情况，即获取栈的最大元素**

用两个栈设计一个新的数据类型(数据类型定义为`MyStack`)，其中一个栈(`dataStack`)用来存放数据,另一个栈(`maxStack`)用来存放最大值.

当插入数据时，`dataStack`接受数据进行入栈操作，`maxStack`首先判断一下栈顶元素和插入元素的大小，
如果栈顶元素小于新插入的元素，那么，`maxStack`进行入栈操作，
如果栈顶元素小于新插入的元素的大小，`maxStack`不做操作.

当删除元素时，`dataStack`直接出栈，而`maxStack`将栈顶元素和`dataStack`出栈元素进行比较，
如果相等，`maxStack`进行出栈操作，否则不做操作.

即`maxStack`的栈顶元素就是最大值.

下图模拟`MyStack`元素入栈和出栈时的状态变化：

![pic]({{ "/images/2019-8-2/stack.png" | absolute_url }})

* 元素3、5、2依次入栈，`dataStack`的内容为3、5、2，而`maxStack`内容为3、5.（*2入栈时，由于2小于`maxStack`栈顶元素5，所以`maxStack`无变化*）
* 元素2出栈，`dataStack`栈顶元素2出栈，而`maxStack`栈顶元素为5，大于2，因此`maxStack`无变化.
* 元素5出栈，`dataStack`栈顶元素5出栈，而`maxStack`栈顶元素为5，两者相等，因此`maxStack`栈顶元素出栈.

代码实现如下（*C++*）：
```cpp
#include <stack>
using namespace std;

class MyStack {
private:
    stack<int> dataStack, maxStack;

public:
    void push(int value) {
        dataStack.push(value);
        if (maxStack.size() == 0 || maxStack.top() <= value)
            maxStack.push(value);
    }

    int top() {
        return dataStack.top();
    }
    
    void pop() {
        int topData = this->top();
        dataStack.pop();
        if (topData == maxStack.top())
            maxStack.pop();
    }
    
    int max() {
        if (!maxStack.empty())
            return maxStack.top();
        return -1;
    }
    
    int size() {
        return dataStack.size();
    }
};
```
### **队列**

**接着就用`MyStack`实现该队列的功能**

进队列直接在`stackIn`中入栈；出队列，首先判断`stackOut`是否为空，不为空直接从`stackOut`出栈，
如果为空，先将`stackIn`中的数据全部放到`stackOut`中，再进行`stackOut`的出栈操作.

最大值是`stackIn`和`stackOut`中的最大值，即通过比较`stackIn.max()`和`stackOut.max()`获得.

下图模拟`MyQueue`元素入队和出队时的状态变化：

![pic]({{ "/images/2019-8-2/queue.png" | absolute_url }})

* 元素3、5、2依次入队，因此`stackIn.dataStack`内容为3、5、2，`stackIn.maxStack`内容为3、5；`stackOut`为空（*因为在此之前还未有出队操作*）.
* 元素3出队，此时判断`stackOut`是否为空.`stackOut`为空，因此将`stackIn`内容全部出栈，并且依次将这些元素压栈到`stackOut`.
此时`stackIn`为空；`stackOut.dataStack`内容为2、5、3，`stackIn.maxStack`内容为2、5.然后将`stackOut`的栈顶元素出栈.完成3的出队操作.
* 元素6入队，将6压栈到`stackIn`.

代码实现如下:
```cpp
class MyQueue {
private:
    MyStack stackIn, stackOut;

public:
    void EnQueue(int value){
        stackIn.push(value);
    }
    
    int DeQueue() {
        int topData;
        if (stackOut.size() > 0) {
            topData = stackOut.top();
            stackOut.pop();
        }
        else {
            while (stackIn.size() > 0) {
                stackOut.push(stackIn.top());
                stackIn.pop();
            }
            topData = stackOut.top();
            stackOut.pop();
        }
        return topData;
    }

    int max() {
        return stackIn.max() > stackOut.max() ?
        	stackIn.max() : stackOut.max();
    }
};
```
* * *