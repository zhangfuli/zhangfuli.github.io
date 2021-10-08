---
title: python3的OJ输入
date: 2019-10-13 10:13:22
tags:
---



1、输入整数

```python
case = int(input().strip())
while case:
	case -= 1
```

2、 输入浮点数

```python
test = float(input().strip())
```

3、字符串

```python
test = input().strip()
```

4、 输入  1    2    3    4    5

```python
nums = list(map(int, input().strip().split()))
```

5、输入   1.0    2.0    3.0    4.0

```python
nums = list(map(float, input().strip().split()))
```

6、输入    a    b    c    d   e

```python
test = list(input().strip().split())
```

7、创建二维数组

```
test = [[0 for i in range(col)] for j in range(row)]
```

8、未知多少输入行

```python
while True:
    line = sys.stdin.readline()
    if not line:
        break
    nums = []
    i = -1
    for str_x in line.strip().split(' '):
        i += 1
        if i == 0:
            len_nuns = int(str_x)
            continue
        nums.append(int(str_x))
```

9、满足条件一的情况下对条件二排序

```python
a = [[2, 3], [4, 1], [2, 8], [2, 1], [3, 4]]
a.sort(key=lambda x: (x[1], -x[0]))
print(a)
```

