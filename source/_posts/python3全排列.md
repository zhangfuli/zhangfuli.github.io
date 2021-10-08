---
title: python3全排列
date: 2019-10-13 10:02:23
tags:
---



#### 方式一

> itertools.permutations(list, num) 

```python
import itertools

num1 = list(map(int, input().split()))
num2 = list(map(int, input().split()))
num = num1 + num2

for item in itertools.permutations(num):
	print(item) //tuple 
  print(list(item))

```

### 方式二

分治思想：就是当指针指向第一个元素a时，它可以是其本身a(即和自己进行交换)，还可以和b，c进行交换，故有3种可能，当第一个元素a确定以后，指针移向第二位置，第二个位置可以和其本身b及其后的元素c进行交换，又可以形成两种排列，当指针指向第三个元素c的时候，这个时候其后没有元素了，此时，则确定了一组排列，输出。但是每次输出后要把数组恢复为原来的样子。

简单来说，它的思想即为，确定第1位，对n-1位进行全排列，确定第二位，对n-2位进行全排列。。。显然，这是一种递归的思想。

```python
def permutations(arr, position, end):
 
    if position == end:
        print(arr)
 
    else:
        for index in range(position, end):
 
            arr[index], arr[position] = arr[position], arr[index]
            permutations(arr, position+1, end)
            arr[index], arr[position] = arr[position], arr[index]
 
 
arr = ["a","b","c"]
permutations(arr, 0, len(arr))
```

### 方式三

深度搜索

```python

visit = [True, True, True]
temp = ["" for x in range(0, 3)]
def dfs(position):
    if position == len(arr):
        print(temp)
        return
 
    for index in range(0,len(arr)):
        if visit[index] == True:
            temp[position] = arr[index]
            visit[index] = False
            dfs(position + 1)
            visit[index] = True
 
arr = ["a","b","c"]
dfs(0)
```

