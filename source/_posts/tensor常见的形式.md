---
title: tensor常见的形式
date: 2020-09-01 22:47:39
tags:
---



```python
# Tensor常见形式
# 0:scalar
# 1:vector
# 2:matrix
# 3:n-dimensional tensor
```


```python
import torch
from torch import tensor
```


```python
# scalar
x = tensor(42.)
x
```




    tensor(42.)




```python
 x.dim()
```




    0




```python
x.item()
```




    42.0




```python
# vector 向量
v = tensor([1.5, -0.5, 3.0])
v
```




    tensor([ 1.5000, -0.5000,  3.0000])




```python
v.dim()
```




    1




```python
v.size()
```




    torch.Size([3])




```python
# Matrix
M = tensor([[1,2],[3,4]])
M
```




    tensor([[1, 2],
            [3, 4]])




```python
M.matmul(M)
```




    tensor([[ 7, 10],
            [15, 22]])




```python
tensor([1,0]).matmul(M)
```




    tensor([1, 2])




```python
M * M
```




    tensor([[ 1,  4],
            [ 9, 16]])




```python
tensor([1,2]).matmul(M)
```




    tensor([ 7, 10])




```python

```