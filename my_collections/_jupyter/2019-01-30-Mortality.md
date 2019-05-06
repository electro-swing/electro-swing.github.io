---
layout: defalut2
title: Probability of dying where I come from 
---
  
    
Let me just quickly load the data. 
  
  

```python
from __future__ import print_function # will need these to ask about age
from ipywidgets import interact, interactive, fixed, interact_manual
import ipywidgets as widgets

import pandas as pd
import matplotlib.pyplot as plt    # will need this
%matplotlib inline                 
import seaborn as sns               # will need this
import numpy as np                 # will probably not need this, shall check


```


```python
excel_file = 'mort_data.xlsx'
mort_data = pd.read_excel(excel_file)
```


```python
mort_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Sex</th>
      <th>Lx</th>
      <th>Tx</th>
      <th>lx</th>
      <th>dx</th>
      <th>qx</th>
      <th>ex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>m</td>
      <td>68914.0</td>
      <td>481.0</td>
      <td>100000</td>
      <td>698</td>
      <td>0.006980</td>
      <td>71.96</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>m</td>
      <td>69476.0</td>
      <td>38.0</td>
      <td>99302</td>
      <td>54</td>
      <td>0.000544</td>
      <td>71.46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>m</td>
      <td>69278.0</td>
      <td>21.0</td>
      <td>99248</td>
      <td>30</td>
      <td>0.000302</td>
      <td>70.50</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>m</td>
      <td>68570.0</td>
      <td>13.0</td>
      <td>99218</td>
      <td>19</td>
      <td>0.000191</td>
      <td>69.52</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>m</td>
      <td>69013.0</td>
      <td>5.0</td>
      <td>99199</td>
      <td>7</td>
      <td>0.000071</td>
      <td>68.54</td>
    </tr>
  </tbody>
</table>
</div>




```python
mort_data.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Sex</th>
      <th>Lx</th>
      <th>Tx</th>
      <th>lx</th>
      <th>dx</th>
      <th>qx</th>
      <th>ex</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>197</th>
      <td>96</td>
      <td>f</td>
      <td>703.0</td>
      <td>221.0</td>
      <td>969</td>
      <td>446</td>
      <td>0.460268</td>
      <td>1.46</td>
    </tr>
    <tr>
      <th>198</th>
      <td>97</td>
      <td>f</td>
      <td>551.0</td>
      <td>199.0</td>
      <td>523</td>
      <td>267</td>
      <td>0.510516</td>
      <td>1.28</td>
    </tr>
    <tr>
      <th>199</th>
      <td>98</td>
      <td>f</td>
      <td>366.0</td>
      <td>129.0</td>
      <td>256</td>
      <td>145</td>
      <td>0.566406</td>
      <td>1.09</td>
    </tr>
    <tr>
      <th>200</th>
      <td>99</td>
      <td>f</td>
      <td>219.0</td>
      <td>76.0</td>
      <td>111</td>
      <td>70</td>
      <td>0.630631</td>
      <td>0.87</td>
    </tr>
    <tr>
      <th>201</th>
      <td>100</td>
      <td>f</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>41</td>
      <td>41</td>
      <td>1.000000</td>
      <td>0.50</td>
    </tr>
  </tbody>
</table>
</div>




```python
mort_data.columns
```




    Index(['Age', 'Sex', 'Lx', 'Tx', 'lx', 'dx', 'qx', 'ex'], dtype='object')



The meanings of the column titles are the: 
  
Lx: number of people of certain age #the data are coming from census  
Tx: number of people of ceratin age who died within last 12 months (?) #the data are coming from census
lx: number of living people of certain age (starting from 100000, smoothed)  
dx: number of people of certain age expected to die  
qx: probability of death (smoothed)  
ex: number of additional years that a person of certain age is expected to live (average)


```python
Lx=mort_data['Lx'].tolist()
age_raw=mort_data['Age'].tolist()

men_raw = Lx[:len(Lx)//2]
women = Lx[len(Lx)//2:]
men = [x * (-1) for x in men_raw]
age=age_raw[:len(Lx)//2]

```

This would be the population pyramid from my country:
  


```python
sns.set_style("whitegrid")
sns.color_palette("RdBu")  #, n_colors=4

pm=bar_plot=sns.barplot (y=men, x=age, data=mort_data, label="men")
pw=bar_plot=sns.barplot (y=women, x=age, data=mort_data, label="women")
pm.set(xlabel="Population", ylabel="Age", title = "Population Pyramid")

pm.set(ylabel='men           women')  #is this acceptable as a solution? pure trickery :)


# pm.set(yticks=[-100000,-50000]) 
# pw.set(yticks=[50000,100000])


for label in pm.get_xticklabels():
    if np.int(label.get_text()) % 10 == 0:  
        label.set_visible(True)
    else:
        label.set_visible(False)
for label in pw.get_xticklabels():
    if np.int(label.get_text()) % 10 == 0:  
        label.set_visible(True)
    else:
        label.set_visible(False)
```


![png](/images/output_10_0.png)



```python

```

So.  Back to the more important stuff.     
What is your probability of dying then?  
  
  
Choose your age on the slider. # still need to add the sex. For now you are female :).
  


```python
output_slider_variable = widgets.Text()

def f(x):
    output_slider_variable.value = str(x)

# interact(f, x=40);  ---> automatic slider is constructed with a range of [−10,+3×10]
interact(f, x=widgets.IntSlider(min=0,max=100,step=1,value=40));
```


    interactive(children=(IntSlider(value=40, description='x'), Output()), _dom_classes=('widget-interact',))



```python
output_slider_variable.value
```




    '40'




```python
the_age = [int(output_slider_variable.value)]    #workaround, as for some reason, df.query won't work with anything but lists
```


```python
mort_data.query('Age == @the_age and Sex == "f"').iat[0,6]
```




    0.0012031853823172534




```python

```


```python

```


```python

```
