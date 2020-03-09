# BokehBarChart
#Python Code reads in hr data and displays headcount numbers by High, Medium, and Low cost locations by date. This chart includes a drop down allowing you to select different manager sets. This chart also utilizes Bokeh's hover over feature that allows you to dispaly headcount numbers for a specific date by hovering your cursor over the area you want to see.

import os

import pandas as pd

from bokeh.plotting import figure

from bokeh.io import output_file, show

from bokeh.plotting import figure, show, output_file

from bokeh.models.glyphs import VBar

import numpy as np # linear algebra

import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)

import warnings

warnings.filterwarnings('ignore')

from bokeh.plotting import figure, show, output_file, output_notebook

from bokeh.palettes import Spectral11, colorblind, Inferno, BuGn, brewer

from bokeh.models import HoverTool, value, LabelSet, Legend, ColumnDataSource,LinearColorMapper,BasicTicker, PrintfTickFormatter, ColorBar

import datetime

 

 

items = os.listdir(".")

 

newlist = []

for names in items:

    if names.endswith(".xlsx"):

        newlist.append(names)

#print(newlist)

 

new_df_H = []

new_df_M = []

new_df_L = []

new_df_Date = []

 

for i in newlist:

    xcl = pd.read_excel(i, sheet_name=1, parse_dates=['File Date'])

    df=pd.DataFrame(xcl)

    #print(df.head(1))

    #print(df.iloc[1,0])

    #print(df.iloc[1]['File Date'])

###Drop 'Employee Status' = Terminated

    df = df[df['Employee Status']  != 'Terminated']

### drop all but tigar

    df = df[df['Ms L08']  == 9906761]

 

##retrieve values

    D = df.iloc[1]['File Date']

    #D = df.iloc[1][0]

    H = (df['Hml Location Cost'] == 'HIGH').sum()

    M = (df['Hml Location Cost'] == 'MED').sum()

    L = (df['Hml Location Cost'] == 'LOW').sum()

    #print(H)

## Add values to new df

    new_df_Date.append(D)

    new_df_H.append(H)

    new_df_M.append(M)

    new_df_L.append(L)

 

#print(new_df_Date,new_df_H,new_df_M,new_df_L)

 

## Merge Columns to one DF

master_df = pd.DataFrame(index = new_df_Date)

se_H = pd.Series(new_df_H)

se_M = pd.Series(new_df_M)

se_L = pd.Series(new_df_L)

master_df['H'] = se_H.values

master_df['M'] = se_M.values

master_df['L'] = se_L.values

master_df['Date']=master_df.index

master_df['Year'] = master_df.Date.apply(lambda x: x.year)

master_df['Month'] = master_df.Date.apply(lambda x: x.month)

master_df['Week'] = master_df.Date.apply(lambda x: x.week)

 

print(master_df)

 

 

### Create a figure

 

#p = figure(x_axis_label='Date',x_axis_type='datetime', y_axis_label='HML Total')

#p.circle(master_df.index.tolist(),master_df['H'],color='red')

#p.circle(master_df.index.tolist(),master_df['M'],color='orange')

#p.circle(master_df.index.tolist(),master_df['L'], color='blue')

 

 

 

 

 

#output_file('HML_Test.html')

TOOLS = "hover,save,pan,box_zoom,reset,wheel_zoom,tap"

p = figure(plot_height=350,

    title="High Medium Low Cost Locations",

    tools=TOOLS,

    toolbar_location='above')

 

p.vbar(x=master_df.Week, top=master_df.H, width=0.9, fill_color = 'red')

p.vbar(x=master_df.Week, top=master_df.M, width=0.9, fill_color = 'orange')

p.vbar(x=master_df.Week, top=master_df.L, width=0.9, fill_color = 'blue')

 

p.y_range.start = 0

p.x_range.range_padding = 0.1

p.xgrid.grid_line_color = None

p.axis.minor_tick_line_color = None

p.outline_line_color = None

p.xaxis.axis_label = 'Month'

p.yaxis.axis_label = 'Employee Count'

p.select_one(HoverTool).tooltips = [

    ('month', '@x'),

    ('Number of Employees', '@top'),

]

output_file("barchart.html", title="barchart")

show(p)
