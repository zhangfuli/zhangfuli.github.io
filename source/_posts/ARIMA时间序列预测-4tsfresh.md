---
title: ARIMA时间序列预测-4tsfresh
date: 2020-08-19 18:44:31
tags:
---



```python
import matplotlib.pylab as plt
import seaborn as sns
from sklearn.metrics import classification_report
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
from tsfresh import extract_features, select_features, extract_relevant_features
from tsfresh.examples import download_robot_execution_failures, load_robot_execution_failures
from tsfresh.feature_extraction import ComprehensiveFCParameters
from tsfresh.utilities.dataframe_functions import impute
```

    //anaconda3/lib/python3.7/site-packages/statsmodels/tools/_testing.py:19: FutureWarning: pandas.util.testing is deprecated. Use the functions in the public API at pandas.testing instead.
      import pandas.util.testing as tm
    //anaconda3/lib/python3.7/site-packages/statsmodels/compat/pandas.py:23: FutureWarning: The Panel class is removed from pandas. Accessing it from the top-level namespace will also be removed in the next version
      data_klasses = (pandas.Series, pandas.DataFrame, pandas.Panel)



```python
download_robot_execution_failures()
df, y = load_robot_execution_failures()
print(df.head())


```

       id  time  F_x  F_y  F_z  T_x  T_y  T_z
    0   1     0   -1   -1   63   -3   -1    0
    1   1     1    0    0   62   -3   -1    0
    2   1     2   -1   -1   61   -3    0    0
    3   1     3   -1   -1   63   -2   -1    0
    4   1     4   -1   -1   63   -3   -1    0



```python
df[df.id == 3][['time', 'F_x', 'F_y', 'F_z', 'T_x', 'T_y', 'T_z']].plot(x='time', title="Success example (id 3)",                                                                       figsize=(12, 6))
df[df.id == 20][['time', 'F_x', 'F_y', 'F_z', 'T_x', 'T_y', 'T_z']].plot(x='time', title="Success example (id 20)",                                                                    figsize=(12, 6))
plt.show()
```


![png](/images/ARIMA时间序列预测-4tsfresh/output_2_0.png)



![png](/images/ARIMA时间序列预测-4tsfresh/output_2_1.png)



```python
extracted_features = extract_features(df, column_id="id", column_sort="time")
X = impute(extracted_features)
print(X)
```

    Feature Extraction: 100%|██████████| 10/10 [00:10<00:00,  1.08s/it]
    //anaconda3/lib/python3.7/site-packages/tsfresh/utilities/dataframe_functions.py:173: RuntimeWarning: The columns ['F_x__agg_linear_trend__attr_"intercept"__chunk_len_50__f_agg_"max"'
     'F_x__agg_linear_trend__attr_"intercept"__chunk_len_50__f_agg_"mean"'
     'F_x__agg_linear_trend__attr_"intercept"__chunk_len_50__f_agg_"min"' ...
     'T_z__fft_coefficient__attr_"real"__coeff_98'
     'T_z__fft_coefficient__attr_"real"__coeff_99'
     'T_z__spkt_welch_density__coeff_8'] did not have any finite values. Filling with zeros.
      df.iloc[:, np.where(is_col_non_finite)[0]].columns.values), RuntimeWarning)


    variable  F_x__abs_energy  F_x__absolute_sum_of_changes  \
    id                                                        
    1                    14.0                           2.0   
    2                    25.0                          14.0   
    3                    12.0                          10.0   
    4                    16.0                          17.0   
    5                    17.0                          13.0   
    ..                    ...                           ...   
    84                96833.0                         100.0   
    85                 1683.0                          19.0   
    86                83497.0                         127.0   
    87              1405437.0                         181.0   
    88                 1427.0                          11.0   
    
    variable  F_x__agg_autocorrelation__f_agg_"mean"__maxlag_40  \
    id                                                            
    1                                                 -0.106351   
    2                                                 -0.039098   
    3                                                 -0.029815   
    4                                                 -0.049773   
    5                                                 -0.061467   
    ..                                                      ...   
    84                                                -0.435813   
    85                                                -0.599870   
    86                                                -0.603352   
    87                                                -0.455727   
    88                                                -0.614351   
    
    variable  F_x__agg_autocorrelation__f_agg_"median"__maxlag_40  \
    id                                                              
    1                                             -7.206633e-02     
    2                                             -4.935275e-02     
    3                                              1.301043e-17     
    4                                             -6.417112e-02     
    5                                             -5.172414e-02     
    ..                                                      ...     
    84                                            -8.343714e-01     
    85                                            -4.380362e-01     
    86                                            -4.802260e-01     
    87                                            -4.355676e-01     
    88                                            -5.887814e-01     
    
    variable  F_x__agg_autocorrelation__f_agg_"var"__maxlag_40  \
    id                                                           
    1                                                 0.016879   
    2                                                 0.088790   
    3                                                 0.105435   
    4                                                 0.143580   
    5                                                 0.052642   
    ..                                                     ...   
    84                                                0.538354   
    85                                                0.991429   
    86                                                0.994691   
    87                                                0.516457   
    88                                                1.121329   
    
    variable  F_x__agg_linear_trend__attr_"intercept"__chunk_len_10__f_agg_"max"  \
    id                                                                             
    1                                                       0.0                    
    2                                                       0.0                    
    3                                                       1.0                    
    4                                                       1.0                    
    5                                                       2.0                    
    ..                                                      ...                    
    84                                                    -25.0                    
    85                                                     12.0                    
    86                                                     70.0                    
    87                                                    340.0                    
    88                                                     -6.0                    
    
    variable  F_x__agg_linear_trend__attr_"intercept"__chunk_len_10__f_agg_"mean"  \
    id                                                                              
    1                                                      -0.9                     
    2                                                      -0.7                     
    3                                                      -0.5                     
    4                                                      -0.4                     
    5                                                      -0.5                     
    ..                                                      ...                     
    84                                                    -55.3                     
    85                                                      6.7                     
    86                                                     40.4                     
    87                                                    281.0                     
    88                                                     -8.4                     
    
    variable  F_x__agg_linear_trend__attr_"intercept"__chunk_len_10__f_agg_"min"  \
    id                                                                             
    1                                                      -1.0                    
    2                                                      -3.0                    
    3                                                      -1.0                    
    4                                                      -2.0                    
    5                                                      -2.0                    
    ..                                                      ...                    
    84                                                   -110.0                    
    85                                                      4.0                    
    86                                                     21.0                    
    87                                                    171.0                    
    88                                                    -11.0                    
    
    variable  F_x__agg_linear_trend__attr_"intercept"__chunk_len_10__f_agg_"var"  \
    id                                                                             
    1                                                      0.09                    
    2                                                      0.81                    
    3                                                      0.45                    
    4                                                      1.24                    
    5                                                      1.05                    
    ..                                                      ...                    
    84                                                  1216.61                    
    85                                                     5.01                    
    86                                                   236.84                    
    87                                                  3848.20                    
    88                                                     1.84                    
    
    variable  F_x__agg_linear_trend__attr_"intercept"__chunk_len_50__f_agg_"max"  \
    id                                                                             
    1                                                       0.0                    
    2                                                       0.0                    
    3                                                       0.0                    
    4                                                       0.0                    
    5                                                       0.0                    
    ..                                                      ...                    
    84                                                      0.0                    
    85                                                      0.0                    
    86                                                      0.0                    
    87                                                      0.0                    
    88                                                      0.0                    
    
    variable  ...  T_z__symmetry_looking__r_0.9500000000000001  \
    id        ...                                                
    1         ...                                          0.0   
    2         ...                                          1.0   
    3         ...                                          1.0   
    4         ...                                          1.0   
    5         ...                                          1.0   
    ..        ...                                          ...   
    84        ...                                          1.0   
    85        ...                                          1.0   
    86        ...                                          1.0   
    87        ...                                          1.0   
    88        ...                                          1.0   
    
    variable  T_z__time_reversal_asymmetry_statistic__lag_1  \
    id                                                        
    1                                              0.000000   
    2                                              0.000000   
    3                                              0.000000   
    4                                              0.000000   
    5                                             -0.076923   
    ..                                                  ...   
    84                                         -1254.846154   
    85                                           -34.846154   
    86                                            81.538462   
    87                                          8445.769231   
    88                                            14.230769   
    
    variable  T_z__time_reversal_asymmetry_statistic__lag_2  \
    id                                                        
    1                                              0.000000   
    2                                              0.000000   
    3                                             -0.090909   
    4                                             -0.181818   
    5                                             -0.090909   
    ..                                                  ...   
    84                                         -3182.363636   
    85                                           -57.545455   
    86                                           141.000000   
    87                                         16935.636364   
    88                                            27.181818   
    
    variable  T_z__time_reversal_asymmetry_statistic__lag_3  \
    id                                                        
    1                                              0.000000   
    2                                              0.000000   
    3                                              0.000000   
    4                                              0.000000   
    5                                             -0.222222   
    ..                                                  ...   
    84                                         -6043.333333   
    85                                           -84.000000   
    86                                           280.888889   
    87                                         25770.555556   
    88                                            36.222222   
    
    variable  T_z__value_count__value_-1  T_z__value_count__value_0  \
    id                                                                
    1                                0.0                       15.0   
    2                                4.0                       11.0   
    3                                4.0                       11.0   
    4                                6.0                        8.0   
    5                                4.0                        9.0   
    ..                               ...                        ...   
    84                               1.0                        1.0   
    85                               1.0                        1.0   
    86                               0.0                        0.0   
    87                               0.0                        0.0   
    88                               0.0                        0.0   
    
    variable  T_z__value_count__value_1  T_z__variance  \
    id                                                   
    1                               0.0       0.000000   
    2                               0.0       0.195556   
    3                               0.0       0.195556   
    4                               1.0       0.355556   
    5                               2.0       0.382222   
    ..                              ...            ...   
    84                              0.0      93.315556   
    85                              0.0       4.648889   
    86                              1.0      29.840000   
    87                              0.0      98.088889   
    88                              0.0       0.782222   
    
    variable  T_z__variance_larger_than_standard_deviation  \
    id                                                       
    1                                                  0.0   
    2                                                  0.0   
    3                                                  0.0   
    4                                                  0.0   
    5                                                  0.0   
    ..                                                 ...   
    84                                                 1.0   
    85                                                 1.0   
    86                                                 1.0   
    87                                                 1.0   
    88                                                 0.0   
    
    variable  T_z__variation_coefficient  
    id                                    
    1                          -0.238179  
    2                          -1.658312  
    3                          -1.658312  
    4                          -1.788854  
    5                          -4.636809  
    ..                               ...  
    84                         -0.624569  
    85                         -0.621960  
    86                         -1.011593  
    87                          0.312757  
    88                          0.181733  
    
    [88 rows x 4578 columns]



```python
X_filtered = extract_relevant_features(df, y,
                                       column_id='id', column_sort='time')
print(X_filtered)
```

    Feature Extraction: 100%|██████████| 10/10 [00:10<00:00,  1.06s/it]


    variable  F_x__value_count__value_-1  F_x__abs_energy  \
    id                                                      
    1                               14.0             14.0   
    2                                7.0             25.0   
    3                               11.0             12.0   
    4                                5.0             16.0   
    5                                9.0             17.0   
    ..                               ...              ...   
    84                               0.0          96833.0   
    85                               0.0           1683.0   
    86                               0.0          83497.0   
    87                               0.0        1405437.0   
    88                               0.0           1427.0   
    
    variable  F_x__range_count__max_1__min_-1  F_y__abs_energy  \
    id                                                           
    1                                    15.0             13.0   
    2                                    13.0             76.0   
    3                                    14.0             40.0   
    4                                    10.0             60.0   
    5                                    13.0             46.0   
    ..                                    ...              ...   
    84                                    0.0          42780.0   
    85                                    0.0           1523.0   
    86                                    0.0          21064.0   
    87                                    0.0         308658.0   
    88                                    0.0            113.0   
    
    variable  T_y__standard_deviation  T_y__variance  \
    id                                                 
    1                        0.471405       0.222222   
    2                        2.054805       4.222222   
    3                        1.768867       3.128889   
    4                        2.669998       7.128889   
    5                        2.039608       4.160000   
    ..                            ...            ...   
    84                      39.541483    1563.528889   
    85                       3.841296      14.755556   
    86                      52.807154    2788.595556   
    87                      80.098162    6415.715556   
    88                       2.628054       6.906667   
    
    variable  F_x__fft_coefficient__attr_"abs"__coeff_1  \
    id                                                    
    1                                          1.000000   
    2                                          0.624118   
    3                                          2.203858   
    4                                          0.844394   
    5                                          2.730599   
    ..                                              ...   
    84                                       359.248162   
    85                                        36.770027   
    86                                       312.044052   
    87                                       481.046930   
    88                                        15.524355   
    
    variable  T_y__fft_coefficient__attr_"abs"__coeff_1  T_y__abs_energy  \
    id                                                                     
    1                                          1.165352             10.0   
    2                                          6.020261             90.0   
    3                                          8.235442            103.0   
    4                                         12.067855            124.0   
    5                                          6.445330            180.0   
    ..                                              ...              ...   
    84                                       309.190088         171261.0   
    85                                        26.631007            503.0   
    86                                       429.697740         118013.0   
    87                                       683.196535        2430295.0   
    88                                        16.768541           7630.0   
    
    variable  F_z__standard_deviation  ...  \
    id                                 ...   
    1                        1.203698  ...   
    2                        4.333846  ...   
    3                        4.616877  ...   
    4                        3.833188  ...   
    5                        4.841487  ...   
    ..                            ...  ...   
    84                     291.988082  ...   
    85                      14.501494  ...   
    86                     121.420189  ...   
    87                     204.966621  ...   
    88                      10.627010  ...   
    
    variable  T_x__agg_linear_trend__attr_"intercept"__chunk_len_5__f_agg_"min"  \
    id                                                                            
    1                                                 -3.000000                   
    2                                                 -4.166667                   
    3                                                 -5.833333                   
    4                                                 -9.333333                   
    5                                                -11.833333                   
    ..                                                      ...                   
    84                                               203.333333                   
    85                                               -29.166667                   
    86                                               -53.000000                   
    87                                              -139.333333                   
    88                                               -20.000000                   
    
    variable  T_x__number_peaks__n_1  T_y__number_cwt_peaks__n_1  \
    id                                                             
    1                            1.0                         4.0   
    2                            4.0                         4.0   
    3                            6.0                         3.0   
    4                            5.0                         5.0   
    5                            5.0                         5.0   
    ..                           ...                         ...   
    84                           3.0                         2.0   
    85                           3.0                         3.0   
    86                           2.0                         3.0   
    87                           2.0                         3.0   
    88                           3.0                         3.0   
    
    variable  T_y__count_below__t_0  \
    id                                
    1                      1.000000   
    2                      0.933333   
    3                      0.866667   
    4                      0.733333   
    5                      0.933333   
    ..                          ...   
    84                     0.000000   
    85                     0.066667   
    86                     0.000000   
    87                     0.000000   
    88                     1.000000   
    
    variable  T_x__change_quantiles__f_agg_"var"__isabs_True__qh_0.2__ql_0.0  \
    id                                                                         
    1                                                  0.000000                
    2                                                  0.000000                
    3                                                  0.000000                
    4                                                  0.000000                
    5                                                  0.000000                
    ..                                                      ...                
    84                                                64.000000                
    85                                                 4.666667                
    86                                                 0.250000                
    87                                                 0.000000                
    88                                                 4.666667                
    
    variable  F_z__change_quantiles__f_agg_"mean"__isabs_True__qh_1.0__ql_0.8  \
    id                                                                          
    1                                                       0.0                 
    2                                                       1.0                 
    3                                                       3.0                 
    4                                                       0.0                 
    5                                                       0.0                 
    ..                                                      ...                 
    84                                                     46.0                 
    85                                                      4.5                 
    86                                                      7.0                 
    87                                                     90.5                 
    88                                                      0.0                 
    
    variable  T_x__quantile__q_0.1  F_y__has_duplicate_max  \
    id                                                       
    1                         -3.0                     1.0   
    2                         -9.2                     1.0   
    3                         -6.6                     0.0   
    4                         -9.0                     0.0   
    5                         -9.6                     0.0   
    ..                         ...                     ...   
    84                       203.2                     0.0   
    85                       -41.6                     0.0   
    86                       -84.8                     0.0   
    87                      -139.2                     0.0   
    88                       -24.6                     0.0   
    
    variable  F_y__cwt_coefficients__coeff_13__w_2__widths_(2, 5, 10, 20)  \
    id                                                                      
    1                                                 -0.310265             
    2                                                 -0.202951             
    3                                                  0.539121             
    4                                                 -2.641390             
    5                                                  0.591927             
    ..                                                      ...             
    84                                                38.559593             
    85                                                14.429645             
    86                                                60.760842             
    87                                               109.029954             
    88                                                 2.455593             
    
    variable  F_y__cwt_coefficients__coeff_14__w_5__widths_(2, 5, 10, 20)  
    id                                                                     
    1                                                 -0.751682            
    2                                                  0.057818            
    3                                                  0.912474            
    4                                                 -0.609735            
    5                                                  0.072771            
    ..                                                      ...            
    84                                                71.641254            
    85                                                16.349699            
    86                                                71.095480            
    87                                               173.699573            
    88                                                 3.226801            
    
    [88 rows x 626 columns]



```python
X_train, X_test, X_filtered_train, X_filtered_test, y_train, y_test = train_test_split(X, X_filtered, y, test_size=0.25)
c1 = DecisionTreeClassifier()
c1.fit(X_train, y_train)
print(classification_report(y_test,c1.predict(X_test)))
```

                  precision    recall  f1-score   support
    
           False       1.00      0.93      0.96        14
            True       0.89      1.00      0.94         8
    
        accuracy                           0.95        22
       macro avg       0.94      0.96      0.95        22
    weighted avg       0.96      0.95      0.96        22




```python
print(c1.n_features_)
```

    4578



```python
c2 = DecisionTreeClassifier()
c2.fit(X_filtered_train, y_train)
print(classification_report(y_test,c2.predict(X_filtered_test)))
```

                  precision    recall  f1-score   support
    
           False       0.93      0.93      0.93        14
            True       0.88      0.88      0.88         8
    
        accuracy                           0.91        22
       macro avg       0.90      0.90      0.90        22
    weighted avg       0.91      0.91      0.91        22




```python
print(c2.n_features_)
```

    626



```python

```


```python

```