---
title: pytorch神经网络气温预测
date: 2020-09-02 11:13:42
tags:
---



```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import torch
import torch.optim as optim
import warnings
warnings.filterwarnings('ignore')
%matplotlib inline
```


```python
features = pd.read_csv('temps.csv')
features.head()
# temp_2 前天最高气温
# temp_1 昨天最高气温
# 历史上这一天平均最高气温
```

{% raw %}
<table border="1" style="overflow:scroll; width=500px">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year</th>
      <th>month</th>
      <th>day</th>
      <th>week</th>
      <th>temp_2</th>
      <th>temp_1</th>
      <th>average</th>
      <th>actual</th>
      <th>friend</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016</td>
      <td>1</td>
      <td>1</td>
      <td>Fri</td>
      <td>45</td>
      <td>45</td>
      <td>45.6</td>
      <td>45</td>
      <td>29</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016</td>
      <td>1</td>
      <td>2</td>
      <td>Sat</td>
      <td>44</td>
      <td>45</td>
      <td>45.7</td>
      <td>44</td>
      <td>61</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016</td>
      <td>1</td>
      <td>3</td>
      <td>Sun</td>
      <td>45</td>
      <td>44</td>
      <td>45.8</td>
      <td>41</td>
      <td>56</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016</td>
      <td>1</td>
      <td>4</td>
      <td>Mon</td>
      <td>44</td>
      <td>41</td>
      <td>45.9</td>
      <td>40</td>
      <td>53</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016</td>
      <td>1</td>
      <td>5</td>
      <td>Tues</td>
      <td>41</td>
      <td>40</td>
      <td>46.0</td>
      <td>44</td>
      <td>41</td>
    </tr>
  </tbody>
</table>
{% endraw %}




```python
print(features.shape)
```

    (348, 9)



```python
# 处理时间数据
import datetime
years = features['year']
months = features['month']
days = features['day']

# datetime 格式
dates = [str(int(year)) + "-" + str((int(month))) + '-' + str(int(day)) for year, month, day in zip(years, months, days)]
dates = [datetime.datetime.strptime(date, '%Y-%m-%d') for date in dates]
```


```python
dates[:5]
```




    [datetime.datetime(2016, 1, 1, 0, 0),
     datetime.datetime(2016, 1, 2, 0, 0),
     datetime.datetime(2016, 1, 3, 0, 0),
     datetime.datetime(2016, 1, 4, 0, 0),
     datetime.datetime(2016, 1, 5, 0, 0)]




```python
# 准备画图
plt.style.use('fivethirtyeight')

# 设置布局
fig, ((ax1,ax2),(ax3,ax4)) = plt.subplots(nrows=2,ncols=2,figsize=(10,10))
fig.autofmt_xdate(rotation = 45)

# 标签值
ax1.plot(dates, features['actual'])
ax1.set_xlabel('')
ax1.set_ylabel('Temperature')
ax1.set_title('Max Temp')

# 昨天
ax2.plot(dates, features['temp_1'])
ax2.set_xlabel('')
ax2.set_ylabel('Temperature')
ax2.set_title('Previous Max Temp')

# 前天
ax3.plot(dates, features['temp_2'])
ax3.set_xlabel('Date')
ax3.set_ylabel('Temperature')
ax3.set_title('Two Days Prior Max Temp')

# 我的逗比朋友
ax4.plot(dates, features['friend'])
ax4.set_xlabel('')
ax4.set_ylabel('Temperature')
ax4.set_title('Friend Estimate')

plt.tight_layout(pad = 2)
```


![png](/images/pytorch神经网络气温预测/output_5_0.png)

```python
# one-hot编码
features = pd.get_dummies(features)
features.head(5) # 自动判断
```

{% raw %}
<div style="overflow-x: scroll;">
<table border="1" >
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th width="100">year</th>
      <th width="100">month</th>
      <th width="100">day</th>
      <th width="100">temp_2</th>
      <th width="100">temp_1</th>
      <th width="100">average</th>
      <th width="100">actual</th>
      <th width="100">friend</th>
      <th width="100">week_Fri</th>
      <th width="100">week_Mon</th>
      <th width="100">week_Sat</th>
      <th width="100">week_Sun</th>
      <th width="100">week_Thurs</th>
      <th width="100">week_Tues</th>
      <th width="100">week_Wed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016</td>
      <td>1</td>
      <td>1</td>
      <td>45</td>
      <td>45</td>
      <td>45.6</td>
      <td>45</td>
      <td>29</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016</td>
      <td>1</td>
      <td>2</td>
      <td>44</td>
      <td>45</td>
      <td>45.7</td>
      <td>44</td>
      <td>61</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016</td>
      <td>1</td>
      <td>3</td>
      <td>45</td>
      <td>44</td>
      <td>45.8</td>
      <td>41</td>
      <td>56</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016</td>
      <td>1</td>
      <td>4</td>
      <td>44</td>
      <td>41</td>
      <td>45.9</td>
      <td>40</td>
      <td>53</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016</td>
      <td>1</td>
      <td>5</td>
      <td>41</td>
      <td>40</td>
      <td>46.0</td>
      <td>44</td>
      <td>41</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>	
{% endraw %}



```python
# 标签
labels = np.array(features['actual'])

# 去掉标签
features = features.drop('actual', axis = 1)

feature_list = list(features.columns)

features = np.array(features)

print(features.shape)
```

    (348, 14)



```python
from sklearn import preprocessing
input_features = preprocessing.StandardScaler().fit_transform(features)
input_features[0]
```




    array([ 0.        , -1.5678393 , -1.65682171, -1.48452388, -1.49443549,
           -1.3470703 , -1.98891668,  2.44131112, -0.40482045, -0.40961596,
           -0.40482045, -0.40482045, -0.41913682, -0.40482045])




```python
# 构建网络模型-麻烦方法
x = torch.tensor(input_features, dtype=float)
y = torch.tensor(labels, dtype = float)

# 权重参数初始化
weights = torch.randn((14,128), dtype=float, requires_grad = True)
biases = torch.randn(128, dtype=float, requires_grad = True)
weights2 = torch.randn((128,1), dtype=float, requires_grad = True)
biases2 = torch.randn(1, dtype=float, requires_grad = True)

learning_rate = 0.001
losses = []

device = torch.device('cuda:0' if torch.cuda.is_available() else 'cpu')

for i in range(1000):
    # 计算隐藏层
    hidden = x.mm(weights) + biases
    hidden = torch.relu(hidden)
    predictions = hidden.mm(weights2) + biases2
    loss = torch.mean((predictions - y)**2)
    losses.append(loss.data.numpy())
    
    # 打印损失
    if i %100 == 0:
        print('loss: ', loss)
    loss.backward() # 反向传播
    
    # 更新参数
    weights.data.add_(- learning_rate * weights.grad.data)
    biases.data.add_(- learning_rate * biases.grad.data)
    weights2.data.add_(- learning_rate * weights2.grad.data)
    biases.data.add_(- learning_rate * biases2.grad.data)
    
    # 梯度清空
    weights.grad.data.zero_()
    biases.grad.data.zero_()
    weights2.grad.data.zero_()
    biases2.grad.data.zero_()
    



```

    loss:  tensor(2628.1660, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(155.1873, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(146.8026, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(144.0431, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(142.7121, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(141.9236, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(141.3944, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(141.0007, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(140.7019, dtype=torch.float64, grad_fn=<MeanBackward0>)
    loss:  tensor(140.4667, dtype=torch.float64, grad_fn=<MeanBackward0>)



```python
# 更简单的网络模型
input_size = input_features.shape[1]
hidden_size = 128
output_size = 1
batch_size = 16
my_nn = torch.nn.Sequential(
    torch.nn.Linear(input_size, hidden_size),
    torch.nn.Sigmoid(),
    torch.nn.Linear(hidden_size, output_size),
)
cost = torch.nn.MSELoss(reduction='mean')
optimizer = torch.optim.Adam(my_nn.parameters(), lr=0.001)
```


```python
losses = []
for i in range(1000):
    batch_loss = []
    for start in range(0, len(input_features), batch_size):
        end = start + batch_size if start + batch_size < len(input_features) else len(input_features)
        xx = torch.tensor(input_features[start:end], dtype=torch.float, requires_grad = True)
        yy = torch.tensor(labels[start:end], dtype=torch.float, requires_grad = True)
        prediction = my_nn(xx)
        loss = cost(prediction, yy)
        optimizer.zero_grad()
        loss.backward(retain_graph=True)
        optimizer.step()
        batch_loss.append(loss.data.numpy())
    if i % 100 == 0:
        losses.append(np.mean(batch_loss))
        print(i, np.mean(batch_loss))
```

    0 3880.0461
    100 38.13609
    200 35.70337
    300 35.309303
    400 35.13356
    500 34.999176
    600 34.880596
    700 34.76389
    800 34.643845
    900 34.51949



```python
# 预测
x = torch.tensor(input_features, dtype=torch.float)
predict = my_nn(x).data.numpy()
```


```python
# 转换日期格式
dates = [str(int(year)) + '-' + str(int(month)) + '-' + str(int(day)) for year, month, day in zip(years, months, days)]
dates = [datetime.datetime.strptime(date, '%Y-%m-%d') for date in dates]

# 创建一个表格来存日期和其对应的标签数值
true_data = pd.DataFrame(data = {'date': dates, 'actual': labels})

# 同理，再创建一个来存日期和其对应的模型预测值
months = features[:, feature_list.index('month')]
days = features[:, feature_list.index('day')]
years = features[:, feature_list.index('year')]

test_dates = [str(int(year)) + '-' + str(int(month)) + '-' + str(int(day)) for year, month, day in zip(years, months, days)]

test_dates = [datetime.datetime.strptime(date, '%Y-%m-%d') for date in test_dates]

predictions_data = pd.DataFrame(data = {'date': test_dates, 'prediction': predict.reshape(-1)}) 

```


```python
# 真实值
plt.plot(true_data['date'], true_data['actual'], 'b-', label = 'actual')

# 预测值
plt.plot(predictions_data['date'], predictions_data['prediction'], 'ro', label = 'prediction')
plt.xticks(rotation = '60'); 
plt.legend()

# 图名
plt.xlabel('Date'); plt.ylabel('Maximum Temperature (F)'); plt.title('Actual and Predicted Values');

```


![png](/images/pytorch神经网络气温预测/output_14_0.png)



```python

```