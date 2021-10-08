---
title: autograd机制
date: 2020-09-01 22:47:27
tags:
---



```python
import torch
```


```python
# 求导方法1
x = torch.randn(3,4, requires_grad=True)
x
```




    tensor([[ 2.2854,  0.9512, -2.4467, -0.7123],
            [-0.7575,  1.1755,  0.4198, -0.2310],
            [-0.9698,  1.2129,  1.3573, -0.6167]], requires_grad=True)




```python
# 方法2
x = torch.randn(3,4)
x.requires_grad = True
x
```




    tensor([[-0.5929,  0.5802, -0.1118, -2.5752],
            [-0.1841, -0.5498, -0.0166, -1.0571],
            [-0.3336,  0.5076, -0.1564, -0.3230]], requires_grad=True)




```python
b = torch.randn(3,4, requires_grad=True)
```


```python
t = x+b
y = t.sum()
y
```




    tensor(-0.6402, grad_fn=<SumBackward0>)




```python
y.backward() # 自动反向传播
```


```python
b.grad
```




    tensor([[1., 1., 1., 1.],
            [1., 1., 1., 1.],
            [1., 1., 1., 1.]])




```python
t.requires_grad
```




    True




```python
# 小例子
# z = b + y 
# y = WX
x = torch.rand(1)
b = torch.rand(1, requires_grad = True)
w = torch.rand(1, requires_grad = True)
y = w*b
z = y+b
```


```python
# 反向传播
z.backward(retain_graph=True)
w.grad  # w的梯度
```




    tensor([0.4825])




```python
b.grad  # 若对梯度不清零，梯度会默认累加，要清零
```




    tensor([1.1871])




```python
# 线性回归
import numpy as np
x_values = [i for i in range(11)]
x_train = np.array(x_values, dtype=np.float32)
x_train = x_train.reshape(-1, 1)
x_train.shape
```




    (11, 1)




```python
y_values = [2*i + 1 for i in x_values]
y_train = np.array(y_values, dtype=np.float32)
y_train = y_train.reshape(-1, 1)
y_train.shape
```




    (11, 1)




```python
import torch
import torch.nn as nn
```


```python
# 线性回归模型
class LinearRegressionMode(nn.Module):
    def __init__(self, input_dim, output_dim):
        super(LinearRegressionMode, self).__init__()
        self.linear = nn.Linear(input_dim, output_dim)
    def forward(self, x):
        out = self.linear(x)
        return out
```


```python
input_dim = 1
output_dim = 1
model = LinearRegressionMode(input_dim, output_dim)

# 使用GPU训练
device = torch.device('cuda:0' if torch.cuda.is_available() else 'cpu')
model.to(device)
# 模型与输入
model
```




    LinearRegressionMode(
      (linear): Linear(in_features=1, out_features=1, bias=True)
    )




```python
# 指定参数与损失函数
epochs = 1000
learning_rate = 0.01
optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)
criterion = nn.MSELoss() # 损失函数
```


```python
# 训练模型
for epoch in range(epochs):
    epoch += 1
    inputs = torch.from_numpy(x_train)
    labels = torch.from_numpy(y_train)
    
    # 使用GPU
    # inputs = torch.from_numpy(x_train).to(device)
    # labels = torch.from_numpy(y_train).to(device)
    
    # 梯度清零
    optimizer.zero_grad()
    
    # 前向传播
    outputs = model(inputs)
    
    # 计算损失
    loss = criterion(outputs, labels)
    
    # 反向传播
    loss.backward()
    
    # 更新权重参数
    optimizer.step()
    if epoch%50 == 0:
        print('epoch {}, loss {}'.format(epoch, loss.item()))
```

    epoch 50, loss 0.061197102069854736
    epoch 100, loss 0.03490455821156502
    epoch 150, loss 0.019908230751752853
    epoch 200, loss 0.011354891583323479
    epoch 250, loss 0.0064764260314404964
    epoch 300, loss 0.0036938709672540426
    epoch 350, loss 0.0021068572532385588
    epoch 400, loss 0.00120164779946208
    epoch 450, loss 0.0006853902596049011
    epoch 500, loss 0.0003909121442120522
    epoch 550, loss 0.00022296742827165872
    epoch 600, loss 0.0001271772343898192
    epoch 650, loss 7.253594958456233e-05
    epoch 700, loss 4.137093128520064e-05
    epoch 750, loss 2.359419704589527e-05
    epoch 800, loss 1.3457529348670505e-05
    epoch 850, loss 7.676342647755519e-06
    epoch 900, loss 4.378400262794457e-06
    epoch 950, loss 2.49663571594283e-06
    epoch 1000, loss 1.424497213520226e-06



```python
# 模型测试
predicted = model(torch.from_numpy(x_train).requires_grad_()).data.numpy()
predicted
```




    array([[ 0.99778  ],
           [ 2.9980998],
           [ 4.9984193],
           [ 6.9987392],
           [ 8.999059 ],
           [10.999378 ],
           [12.999699 ],
           [15.000018 ],
           [17.000338 ],
           [19.000658 ],
           [21.000977 ]], dtype=float32)




```python
# 保存模型
torch.save(model.state_dict(),'model.pkl')
```


```python
model.load_state_dict(torch.load('model.pkl'))
```




    <All keys matched successfully>




```python

```