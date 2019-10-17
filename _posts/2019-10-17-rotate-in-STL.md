---
layout: post
title:  "rotate in STL"
date:   2019-10-17 12:00:00
categories: CPP
permalink: /archivers/rotate
comments: true
---
`STL` 的 `rotate` 函数.
<!--more-->
数组翻转，类似于循环移位.

将 [1, 2, 3, 4, 5, 6, 7] 翻转为 [4, 5, 6, 7, 1, 2, 3].
## 算法
`STL` 根据不同的迭代器类型使用了三种不同的算法.

### 1. *`ForwardIterator`*
**分组交换**：

$$ list = AB $$，若 $$ len(A) > len(B) $$，将 $$ A $$ 分为 $$ A_0A_1 $$，其中 $$ len(A_0) = len(B) $$.       
交换 $$ A_0 $$ , $$ B $$ . $$ list = BA_1A_0$$ .     
再迭代交换 $$ A_1A_0 $$ 即可. 时间复杂度为 $$ O(n) $$ .

### 2. *`BidirectionIterator`*
**三次翻转**：

$$
\begin{aligned}
& list = AB.\\\\
& list' = (A^TB^T)^T = BA.
\end{aligned}
$$

### 3. *`RandomAccessIterator`*
**链表循环移位**：

$$ len(list) = n $$ , 翻转位置为 $$ m $$ .     
链表个数为 $$ gcd(m, n) $$ ， 链表中元素个数为 $$ n / gcd(m, n) $$ .        
对每个链表的元素进行循环移位.

**示例**

***1***     
$$ 
\begin{aligned}
& \color{red}{list} = [1,\ 2,\ 3,\ 4,\ 5,\ 6] \to [3,\ 4,\ 5,\ 6,\ 1,\ 2].\\\\
& n = 6, m = 2, gcd(m, n) = 2.\\\\
& list_1 \to 3 \to 5 \to 1.\\\\       
& 循环移位后，list = [3,\ 2,\ 5,\ 4,\ 1,\ 6].\\\\   
& list_2 \to 2 \to 4 \to 6.\\\\       
& 循环移位后，list = [3,\ 4,\ 5,\ 6,\ 1,\ 2].
\end{aligned}
$$

***2***   
$$
\begin{aligned}  
& \color{red}{list} = [1,\ 2,\ 3,\ 4,\ 5] \to [3,\ 4,\ 5,\ 1,\ 2].\\\\        
& n = 5, m = 2, gcd(m, n) = 1.\\\\
& list_1 \to 3 \to 5 \to 2 \to 4 \to 1.\\\\       
& 循环移位后，list = [3,\ 4,\ 5,\ 1,\ 2].
\end{aligned}
$$

**补充-定理**

若有两个正整数 $$ m、n $$ ，且 $$ gcd(m, n) = d $$ ，那么序列 $$ \lbrace m\%n,\ 2m\%n,\ 3m\%n,\ ...,\ nm\%n \rbrace $$
一定是 $$ \{0,\ d,\ 2d,\ ...,\ n-d\} $$ 的某个排列并重复出现 $$ d $$ 次，
其中 $$ \% $$ 号代表求模操作.        
比如若 $$ m=6,\ n=8,\ d=gcd(m, n)=2 $$ ，
那么 $$ \{6\%8,\ 12\%8,\ 18\%8,\ ...,\ 48\%8\} $$ 即为 $$ \{0,\ 2,\ 4,\ 6\} $$ 的某个排列并重复.

特别地，若 $$ m、n $$ 互素，$$ d=1 $$ ，那么序列 $$ \{m\%n,\ 2m\%n,\ 3m\%n,\ ...,\ (n-1)m\%n\} $$
实际上就是 $$ \{1,\ 2,\ 3,\ ...,\ n-1\} $$ 的某个排列.

***但是目前还没有弄懂该定理与循环移位之间的关系...***

## 代码

```cpp
template<class ForwardIterator,class Distance>
void __rotate(ForwardIterator first, ForwardIterator middle, ForwardIterator last, Distance*, forward_iterator_tag)
{
    for (ForwadrIterator i = middle;;)
    {
        iter_swap(first, i);
        ++first;
        ++i;
        if (first == middle)
        {
            if (i == last)
                return;
            middle = i;
        }
        else if (i == last)
            i = middle;
    }
}
```
***

```cpp
template<class BidirectionalIterator,class Distance>
void __rotate(BidirectionalIterator first, BidirectionalIterator middle, BidirectionalIterator last, Distance*, bidirectional_iterator_tag)
{
    reverse(first, middel);
    reverse(middel, last);
    reverse(first, last);
}
```
***

```cpp
template<class RandomAccessIterator,class Distance>
void __rotate(RandomAccessIterator first, RandomAccessIterator middle, RandomAccessIterator last, Distance*, random_access_iterator_tag)
{
    Distance n = __gcd(last - first, middle, first);
    while (n--)
        __rotate_cycle(first, last, first + n, middle - first, value_type(first));
}
 
template<class EuclideanRingElement>
EuclideanRingElement __gcd(EuclideanRingElement m, EuclideanRingElement n)
{
    while (n != 0)
    {
        EuclideanRingElement t = m%n;
        m = n;
        n = t;
    }
    return m;
}
 
template<class RandomAccessIterator,class Distance,class T>
void __rotate_cycle(RandomAccessIterator first, RandomAccessIterator last, RandomAccessIterator initial, Distance shift, T*)
{
    T value = *initial;
    RandomAccessIterator ptr1 = initial;
    RandomAccessIterator ptr2 = ptr1 + shift;
    while (ptr2 != initial)
    {
        *ptr1 = *ptr2;
        ptr1 = ptr2;
        if (last - ptr2 > shift)
            ptr2 += shift;
        else
            ptr2 = first + (shift - (last - ptr2));
    }
    *ptr1 = value;
}
```

