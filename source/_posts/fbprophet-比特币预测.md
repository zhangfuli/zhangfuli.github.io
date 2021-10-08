---
title: fbprophet-比特币预测
date: 2020-08-20 18:21:40
tags:
---



```python
import re

import pandas as pd
import matplotlib.pylab as plt
import seaborn as sns
from fbprophet import Prophet

plt.style.use('ggplot')
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False
```

    //anaconda3/lib/python3.7/site-packages/statsmodels/tools/_testing.py:19: FutureWarning: pandas.util.testing is deprecated. Use the functions in the public API at pandas.testing instead.
      import pandas.util.testing as tm



```python
df_original = pd.read_csv('./data/bitcoin.csv', index_col=0)
print(df_original.head())
df_original.rename(columns={
    "收盘": "close",
    "日期": 'date'
}, inplace=True)

df_index = [re.sub("[日]", "", re.sub("[年月]", "-", i)) for i in df_original.index]
df_close = [float(re.sub(",", "", i)) for i in df_original['close']]
df = pd.DataFrame({'date': df_index, 'close': df_close}).set_index('date')
df = df.sort_index(axis=0, ascending=True)
print(df.tail())
```

                      收盘        开盘         高         低      交易量     涨跌幅
    日期                                                                 
    2020年8月20日  11,769.4  11,751.0  11,822.2  11,672.7  463.59K   0.16%
    2020年8月19日  11,750.2  11,944.4  12,013.2  11,602.6  550.26K  -1.65%
    2020年8月18日  11,947.6  12,283.0  12,381.4  11,842.0  606.79K  -2.73%
    2020年8月17日  12,282.6  11,898.8  12,444.1  11,764.0  628.41K   3.22%
    2020年8月16日  11,899.0  11,845.5  11,922.5  11,688.9  410.81K   0.45%
                close
    date             
    2020-8-5  11735.1
    2020-8-6  11757.1
    2020-8-7  11592.0
    2020-8-8  11764.3
    2020-8-9  11681.2



```python
df.plot(figsize=(12, 5))
plt.show()
```

    //anaconda3/lib/python3.7/site-packages/matplotlib/font_manager.py:1328: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))



![png](/images/fbprophet-btc/output_2_1.png)



```python
df['ds'] = df.index
df['y'] = df['close']

m = Prophet()
m.fit(df)  # 去训练
future = m.make_future_dataframe(freq='D', periods=7)  # 再现有数据的基础上外推7天
print(future.tail())
```

    INFO:fbprophet:Disabling daily seasonality. Run prophet with daily_seasonality=True to override this.


                 ds
    3066 2020-08-23
    3067 2020-08-24
    3068 2020-08-25
    3069 2020-08-26
    3070 2020-08-27



```python
forecast = m.predict(future)
print(forecast[['ds', 'yhat', 'yhat_lower', 'yhat_upper']].tail())
```

                 ds          yhat   yhat_lower    yhat_upper
    3066 2020-08-23  10811.656882  9364.713434  12508.943192
    3067 2020-08-24  10823.539358  9182.644030  12364.048982
    3068 2020-08-25  10809.708244  9246.669308  12347.574130
    3069 2020-08-26  10808.611203  9219.850904  12331.327537
    3070 2020-08-27  10791.636069  9264.811965  12232.313781



```python
m.plot(forecast).show()  # 绘制预测效果图
```

    //anaconda3/lib/python3.7/site-packages/matplotlib/cbook/__init__.py:2062: FutureWarning: Support for multi-dimensional indexing (e.g. `obj[:, None]`) is deprecated and will be removed in a future version.  Convert to a numpy array before indexing instead.
      x[:, None]
    //anaconda3/lib/python3.7/site-packages/matplotlib/axes/_base.py:250: FutureWarning: Support for multi-dimensional indexing (e.g. `obj[:, None]`) is deprecated and will be removed in a future version.  Convert to a numpy array before indexing instead.
      y = y[:, np.newaxis]
    //anaconda3/lib/python3.7/site-packages/matplotlib/font_manager.py:1328: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))
    //anaconda3/lib/python3.7/site-packages/matplotlib/figure.py:459: UserWarning: matplotlib is currently using a non-GUI backend, so cannot show the figure
      "matplotlib is currently using a non-GUI backend, "



![png](/images/fbprophet-btc/output_5_1.png)



```python
m.plot_components(forecast).show()  # 画出分解图
```

    //anaconda3/lib/python3.7/site-packages/matplotlib/cbook/__init__.py:2062: FutureWarning: Support for multi-dimensional indexing (e.g. `obj[:, None]`) is deprecated and will be removed in a future version.  Convert to a numpy array before indexing instead.
      x[:, None]
    //anaconda3/lib/python3.7/site-packages/matplotlib/axes/_base.py:250: FutureWarning: Support for multi-dimensional indexing (e.g. `obj[:, None]`) is deprecated and will be removed in a future version.  Convert to a numpy array before indexing instead.
      y = y[:, np.newaxis]
    //anaconda3/lib/python3.7/site-packages/matplotlib/cbook/__init__.py:2062: FutureWarning: Support for multi-dimensional indexing (e.g. `obj[:, None]`) is deprecated and will be removed in a future version.  Convert to a numpy array before indexing instead.
      x[:, None]
    //anaconda3/lib/python3.7/site-packages/matplotlib/axes/_base.py:250: FutureWarning: Support for multi-dimensional indexing (e.g. `obj[:, None]`) is deprecated and will be removed in a future version.  Convert to a numpy array before indexing instead.
      y = y[:, np.newaxis]
    //anaconda3/lib/python3.7/site-packages/matplotlib/font_manager.py:1328: UserWarning: findfont: Font family ['sans-serif'] not found. Falling back to DejaVu Sans
      (prop.get_family(), self.defaultFamily[fontext]))
    //anaconda3/lib/python3.7/site-packages/matplotlib/figure.py:459: UserWarning: matplotlib is currently using a non-GUI backend, so cannot show the figure
      "matplotlib is currently using a non-GUI backend, "



![png](/images/fbprophet-btc/output_6_1.png)

