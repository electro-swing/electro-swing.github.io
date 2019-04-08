

```python
# Map of the World with mapped countries
```

Countries shape files downloaded from https://www.naturalearthdata.com/downloads/110m-cultural-vectors/ .
The original tutorial link: https://towardsdatascience.com/a-complete-guide-to-an-interactive-geographical-map-using-python-f4c5197e23e0



```python
import geopandas as gpd
shapefile = 'D:/python/geo/exposure map/ne_110m_admin_0_countries.shp'
#Read shapefile using Geopandas
gdf = gpd.read_file(shapefile)[['ADMIN', 'ADM0_A3', 'geometry']]
#Rename columns.
gdf.columns = ['country', 'country_code', 'geometry']
gdf.head()
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
      <th>country</th>
      <th>country_code</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Fiji</td>
      <td>FJI</td>
      <td>(POLYGON ((180 -16.06713266364245, 180 -16.555...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>United Republic of Tanzania</td>
      <td>TZA</td>
      <td>POLYGON ((33.90371119710453 -0.950000000000000...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Western Sahara</td>
      <td>SAH</td>
      <td>POLYGON ((-8.665589565454809 27.65642588959236...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Canada</td>
      <td>CAN</td>
      <td>(POLYGON ((-122.84 49.00000000000011, -122.974...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>United States of America</td>
      <td>USA</td>
      <td>(POLYGON ((-122.84 49.00000000000011, -120 49....</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(gdf[gdf['country'] == 'Antarctica'])
#Drop row corresponding to 'Antarctica'
gdf = gdf.drop(gdf.index[159])
```

            country country_code  \
    159  Antarctica          ATA   
    
                                                  geometry  
    159  (POLYGON ((-48.66061601418252 -78.047018731598...  
    


```python
import pandas as pd
datafile = 'D:/python/geo/exposure map/expgit.xlsx'
#Read excel file using pandas
df = pd.read_excel(datafile, skiprows = 0)
df.head()
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
      <th>Country</th>
      <th>Exposure</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Canada</td>
      <td>2.445828e+05</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Argentina</td>
      <td>1.047080e+07</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Korea</td>
      <td>6.282480e+05</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Poland</td>
      <td>4.891656e+04</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Romania</td>
      <td>1.992000e+07</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Merge dataframes gdf and df.
merged = gdf.merge(df, left_on = 'country', right_on = 'Country')
#Perform left merge to preserve every row in gdf.
merged = gdf.merge(df, left_on = 'country', right_on = 'Country', how = 'left')

#Replace NaN values to string 'No data'.
merged.fillna('No data', inplace = True)

import json
#Read data to json.
merged_json = json.loads(merged.to_json())
#Convert to String like object.
json_data = json.dumps(merged_json)
```


```python
%matplotlib inline

from bokeh.io import output_notebook, show, output_file
from bokeh.plotting import figure
from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
from bokeh.palettes import brewer

#Input GeoJSON source that contains features for plotting.
geosource = GeoJSONDataSource(geojson = json_data)

#Define a sequential multi-hue color palette.
palette = brewer['RdYlBu'][8]

#Reverse color order so that dark blue is highest obesity.
palette = palette[::-1]

#Instantiate LinearColorMapper that maps numbers in a range linearly into a sequence of colors. Input nan_color.
color_mapper = LinearColorMapper(palette = palette, low = 0, high = 23000000, nan_color = '#d9d9d9')

#Define custom tick labels for color bar.
tick_labels = {'0': '0%', '5': '5%', '10':'10%', '15':'15%', '20':'20%', '25':'25%', '30':'30%','35':'35%', '40': '>40%'}

#Create color bar. 
color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)

#Create figure object.
p = figure(title = 'Exposure (EUR)', plot_height = 500, plot_width = 800, toolbar_location = None)
p.xgrid.grid_line_color = None
p.ygrid.grid_line_color = None

#Add patch renderer to figure. 
p.patches('xs','ys', source = geosource,fill_color = {'field' :'Exposure', 'transform' : color_mapper},
          line_color = 'black', line_width = 0.25, fill_alpha = 1)
#Specify figure layout.
p.add_layout(color_bar, 'below')
#Display figure inline in Jupyter Notebook.
output_notebook()
#Display figure.
show(p)
```



    <div class="bk-root">
        <a href="https://bokeh.pydata.org" target="_blank" class="bk-logo bk-logo-small bk-logo-notebook"></a>
        <span id="1420">Loading BokehJS ...</span>
    </div>











  <div class="bk-root" id="badfaf08-9243-4f26-81a5-31dd4f0d89f8"></div>




