# Imported Libraries

````
from random import randint, randrange
import pandas as pd
import numpy as np
from dash import Dash, html, dcc, dash_table, Input, Output, callback
import plotly.express as px
import plotly.graph_objects as go
from dash.exceptions import PreventUpdate
import dash
import json
import time
import functools

````


# Test Data

````


# Data Lists
months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August','September', 'October','November', 'December']
revenue = []
newCustomers = []
leavingCustomers = []
marketingCost = []
monthlyAcquisitionCosts = []
monthlyAverageRevenue = []
purchases = []
monthlyAvgPurchases = []


i = 0

while i<12:
    randRevenue = randrange(100000, 1000000)
    randCustomers = randrange(1000,5000)
    randLeaving = randrange(500,1000)
    randMarketing = randrange(50000, 100000)
    randPurchasing = randrange(1000,5000)
    
    revenue.append(randRevenue)
    newCustomers.append(randCustomers)
    leavingCustomers.append(randLeaving)
    marketingCost.append(randMarketing)
    purchases.append(randPurchasing)
    
    i += 1
    
    
averageRPU = round(sum(revenue)/sum(newCustomers), 1)
acquisitionCost = round(sum(marketingCost)/sum(newCustomers), 1)

target = [3500] *12

targetmet = newCustomers[11]/target[0]

for i in range(len(newCustomers)):
    mAQC = round(marketingCost[i]/newCustomers[i], 3)
    mARU = round(revenue[i]/newCustomers[i], 3)
    mAP = round(revenue[i]/purchases[i], 3)
    
    monthlyAcquisitionCosts.append(mAQC)
    monthlyAverageRevenue.append(mARU)
    monthlyAvgPurchases.append(mAP)
    
    
    
    
dataDict = {'Month':months, 'Revenue':revenue, 'New Customers': newCustomers, 'Leaving Customers':leavingCustomers, 'Costs':marketingCost, 'Target':target, 'Purchases':purchases}
df = pd.DataFrame(dataDict)



if targetmet<=1:
    targetDict = {'Current Period': ['December', 'DecemberComp'], 'TargetRatio':[targetmet, 1-targetmet]}
    
else:
    targetDict = {'Current Period':['December', 'DecemberComp'], 'TargetRatio':[1, 0]}

df1 = pd.DataFrame(targetDict)   

customerARU = {'Month':months, 'AC':monthlyAcquisitionCosts, 'AR':monthlyAverageRevenue, 'Average Purchase Value':monthlyAvgPurchases}
df2 = pd.DataFrame(customerARU)


            
````

# Callback Test Data

````
# Data for basic callbacks
Set2020 = []
Set2021 = []
Set2022 = []

Cust2020 = []
Cust2021 = []
Cust2022 = []

i = 0

while i<12:
    
    # Revenue Data
    data2020 = randrange(500000, 1000000)
    data2021 = randrange(500000, 1000000)
    data2022 = randrange(500000, 1000000)
    
    
    # Customer Data
    custData2020 = randrange(1000, 5000)
    custData2021 = randrange(1000, 5000)
    custData2022 = randrange(1000, 5000)
    
    
    Set2020.append(data2020)
    Set2021.append(data2021)
    Set2022.append(data2022)
    
    Cust2020.append(custData2020)
    Cust2021.append(custData2021)
    Cust2022.append(custData2022)
    
    i += 1
    
    
dfCallback = pd.DataFrame({'Month':months, '2020':Set2020, '2021':Set2021, '2022':Set2022})
years = [2020, 2021, 2022, 2023]

dfCallbackMulti = pd.DataFrame({'Month':months, '2020':Cust2020, '2021': Cust2021, '2022':Cust2022})

    
````



# Visuals

````
# Figure 1

fig1 = px.line(df, x='Month', y='Revenue', title = 'Sales Revenue (1 Year)', height = 330, labels = {'Month':'', 'Revenue':''})
fig1.update_layout(title_font_size = 13, yaxis = dict(tickfont = dict(size = 11)), xaxis = dict(tickfont = dict(size = 11)))
fig1.update_layout({'plot_bgcolor':'white', 'paper_bgcolor':'white'})
fig1.update_traces(line_color = '#2565AE')
fig1.update_xaxes(showgrid = False)
fig1.update_yaxes(showgrid = False)


# Figure 2
fig2 = px.bar(df, x='Month', y='New Customers', title = 'Number of New Customers (1 Year)', height = 330, labels = {'Month':'', 'New Customers':''} )
fig2.update_layout(title_font_size = 13, yaxis = dict(tickfont = dict(size = 11)), xaxis = dict(tickfont = dict(size = 11)))
fig2.update_layout({'plot_bgcolor':'white', 'paper_bgcolor':'white'})
fig2.update_xaxes(showgrid = False)
fig2.update_yaxes(showgrid = False)
fig2.update_traces(marker_color = '#66D3FA')
fig2.add_trace(
    go.Scatter(x=months, y=target, mode = 'lines', line = go.scatter.Line(color = "gray"), showlegend = False)

)


# Figure 3
fig3 = px.pie(df1, values = 'TargetRatio', color = 'Current Period', title = 'Target Met (100% = Met)', hole = 0.7, height = 290, width = 280, color_discrete_map = {
    'December':'#2565AE', 
    'DecemberComp':'#D5F3FE'
    
})

fig3.update_layout(title_font_size = 13)



# Figure 4
fig4 = px.bar(df2, x = 'Month', y = 'AC', title = 'Customer Average Revenue and Acquisition Cost (1 Year)', height = 330, labels = {'Month':'', 'AC':''})
fig4.update_layout(title_font_size = 13, yaxis = dict(tickfont = dict(size = 11)), xaxis = dict(tickfont = dict(size = 11)))
fig4.update_layout({'plot_bgcolor':'white', 'paper_bgcolor':'white'})
fig4.update_xaxes(showgrid = False)
fig4.update_yaxes(showgrid = False)
fig4.update_traces(marker_color = '#3C99DC')
fig4.add_trace(
    go.Scatter(x = months, y = monthlyAverageRevenue, mode = 'lines', line = go.scatter.Line(color = '#66D3FA'), showlegend = False)


)


# Figure 5
fig5 = px.bar(df2, x ='Month', y = 'Average Purchase Value', title = 'Average Purchase Value (1 Year)', height = 270, labels = {'Month':'', 'Average Purchase Value':''})
fig5.update_layout(title_font_size = 13, yaxis = dict(tickfont = dict(size = 11)), xaxis = dict(tickfont = dict(size = 11)), hovermode = 'y')
fig5.update_layout({'plot_bgcolor':'white', 'paper_bgcolor':'white'})
fig5.update_xaxes(showgrid = False)
fig5.update_yaxes(showgrid = False)
fig5.update_traces(marker_color = '#0F5298', hovertemplate = 'Month: %{x} <br> Average Purchase Value: %{y}')


````

# App Layout

````
app = Dash(__name__, suppress_callback_exceptions = True)

# App Layout
app.layout = html.Div(children = [
    dcc.Location(id = 'url', refresh = False), 
    html.Div(id = 'page-content')
    
])


# Index Page
index_page = html.Div(children = [
    
    dcc.Link('Dashboard', href = '/page-1'), 
    html.Br(), 
    dcc.Link('Page 2', href = '/page-2')
    
])



# Layout 1
layout1 = html.Div(children = [
    
    html.H2('This is the first page')
])



#  Layout 2

layout2 = html.Div(children = [
    
    # Row 1
    html.Div(children = [
        
        # Title
        
        html.H2(children = 'Sales Analytics (January-December)', style = {'text-align':'left', 'margin-left':'15px', 'position':'absolute', 'top':'10px'}), 
        
      
        
        # Index Display
        html.Div(children = [
            
            html.P(id = 'index', style = {'width':'100%', 'text-align':'center'})
            
            
        ], style = {'width':'10%', 'height':'20px', 'position':'absolute', 'margin-left':'500px', 'top':'18px'}), 
        
       
        # Month Dropdown
        
        html.Div(children = [
            
            dcc.Dropdown(months, 'December', id = 'month-dropdown')
            
            
        ], style = {'width':'20%', 'height':'20px', 'position':'absolute', 'margin-left':'650px', 'top':'25px'}), 
        
        
        
     
        # Dropdown
        html.Div(children = [
            
            dcc.Dropdown(years, '2020' ,id = 'year-dropdown')
            
        ], style = {'width':'30%', 'height':'20px', 'background-color':'white', 'position':'absolute', 'margin-left':'950px', 'top':'25px'}), 
        
        
        # Error Display
        html.P(id = 'ErrorDisplay', style = {'color':'red', 'font-size':'10px', 'position':'absolute', 'top':'54px', 'left':'960px'}),
        
        
        

    ], style = {'width':'100%', 'height':'70px', 'background-color':'white', 'box-shadow':'hsl(0, 0%, 80%) 0 0 16px'}), 
    
    
    
    
    # Row 2
    
    html.Div(children = [
        
        # Column 1 
        
        html.Div(children = [
            
          # Figure 1
            
            dcc.Graph(
                id = 'Revenue',
            ),
            
        
            
         
              
           
             
        ], style = {'width':'49.62%', 'height':'100%', 'display':'inline-block', 'box-shadow':'hsl(0, 0%, 80%) 0 0 16px'}),
        
        
        # Column 2
        
        html.Div(children = [
            
            # Sub-Column 1
            html.Div(children = [
             
              
                # Figure 2
                dcc.Graph(
                
                    id = 'New Customers', 
                    figure = fig2
                )
                
                
            ], style = {'width':'60%', 'height':'100%', 'display':'inline-block'}), 
            
            
            # Sub-Column 2
            html.Div(children = [
                
                # Sub-Sub-Column 1
                html.Div(children = [
                    
                    
                    # Figure 3
                    dcc.Graph(
                    
                        id = 'Target', 
                        figure = fig3
                    )
                    
                    
                    
                ], style = {'width':'98%', 'height':'65%', 'display':'block', 'margin-right':'25px', 'margin-top':'20px'}), 
                
                
            ], style = {'width':'39%', 'height':'100%', 'display':'inline-block'}), 
            
            
            
        ], style = {'width':'49.62%', 'height':'100%', 'display':'inline-block','box-shadow':'hsl(0, 0%, 80%) 0 0 16px' , 'margin-left':'10px'} ), 
        
    ], style = {'width':'100%', 'height':'350px', 'display':'block', 'margin-top':'10px'} ), 
    
    
    
    # Row 3
    
    html.Div(children = [
        
        # Column 1
        html.Div(children = [
            
            # Sub-Column 1
            
            html.Div(children = [
               
                # Figure 4
                dcc.Graph(
                
                    id = 'CustomerARAC', 
                    figure = fig4
                
                )
                
            ], style = {'width':'70%', 'height':'100%', 'display':'inline-block'}), 
            
            
            # Sub-Column 2
            html.Div(children = [
                
                # Sub-Sub-Row 1
                html.Div(children = [
                    
                    # Text 1
                    html.Div(children = [
                        
                        html.P(id = 'monthRev', style = {'position':'absolute', 'color':'black', 'margin-left':'50px', 'top':'500px', 'font-size':'13px', 'font-family':'sans-serif'})
                        
                    ], style = {'width':'100%', 'height':'50%', 'display':'block', 'background-color':'#D5F3FE'}), 
                    
                    
                    
                    # Text 2
                    html.Div(children = [
                        
                        html.P(children = '(Average Revenue Per User)', style = {'position':'absolute', 'color':'black', 'margin-left':'25px', 'font-size':'13px', 'font-family':'sans-serif'})
                        
                    ], style = {'width':'100%', 'height':'50%', 'display':'block', 'background-color':'#D5F3FE'}), 
                    
                    
                ], style = {'width':'100%', 'height':'40%','margin-top':'25px', 'margin-left':'-10px', 'background-color':'#D5F3FE'}), 
                
                
                # Sub-Sub-Row 2
                
                html.Div(children = [
                    
                    # Text 1
                    html.Div(children = [
                        
                        html.P(id = 'monthAC', style = {'position':'absolute', 'color':'black', 'margin-left':'50px', 'top':'660px', 'font-size':'13px', 'font-family':'sans-serif'})
                        
                    ], style = {'width':'100%', 'height':'50%', 'display':'block', 'background-color':'#D5F3FE'}), 
                    
                    
                    # Text 2
                    
                    html.Div(children = [
                        
                        html.P(children = '(Acquisition Cost)', style = {'position':'absolute', 'color':'black', 'margin-left':'60px', 'font-size':'13px', 'font-family':'sans-serif'})
                        
                    ], style = {'width':'100%', 'height':'50%', 'display':'block', 'background-color':'#D5F3FE'}), 
                    
                    
                    
                ], style = {'width':'100%', 'height':'40%', 'display':'block', 'margin-top':'15px', 'margin-left':'-10px', 'background-color':'#D5F3FE'}), 
                
                
                
            ], style = {'width':'30%', 'height':'100%', 'display':'inline-block'}),
            
            
        ], style = {'width':'49.62%', 'height':'100%', 'display':'inline-block','box-shadow':'hsl(0, 0%, 80%) 0 0 16px'  }), 
        
        
        
        # Column 2
        html.Div(children = [
            
            # Sub-Row 1
            html.Div(children = [
                
                html.Div(children = [
                    
                    html.H3(children = f"Average Purchase Value (Current Period): {df2.iloc[11]['Average Purchase Value']}", style = {'text-align':'center', 'color':'black', 'font-weight':'normal', 'margin-right':'20px', 'font-size':'14px', 'font-family':'sans-serif'})
                    
                    
                ], style = {'width':'50%', 'height':'100%', 'position':'absolute'}), 
                
                
                
            ], style = {'width':'97%', 'height':'19%', 'display':'block', 'margin-top':'8px', 'margin-left':'10px'}), 
            
            
            # Sub-Row 2
            html.Div(children = [
                
                # Figure 5
                
                dcc.Graph(
                
                    id = 'Average Purchase Value', 
                    figure = fig5
                
                )
                
            ], style = {'width':'100%', 'height':'80%'}), 
            
            
            
            
            
        ], style = {'width':'49.62%', 'height':'100%', 'display':'inline-block', 'box-shadow':'hsl(0, 0%, 80%) 0 0 16px', 'margin-left':'10px'} ), 
        
        
        
        
    ], style = {'width':'100%', 'height':'350px', 'margin-top':'10px', 'display':'block'}), 
        
    
    
], style = {'width':'100%', 'height':'100%'})




@callback(
    Output('Revenue', 'figure'), 
    Output('New Customers', 'figure'),
    Output(component_id = 'ErrorDisplay', component_property = 'children'), 
    Input('year-dropdown', 'value'), 
)



@functools.lru_cache(maxsize = 32)
def update_figure(selected_year):
    
    time.sleep(2)
    
    if int(selected_year) > 2022:
        
        return dash.no_update, dash.no_update, 'Please select another year'
        
        
    
    filtered_df = dfCallback[['Month', str(selected_year)]]
    filtered_Custdf = dfCallbackMulti[['Month', str(selected_year)]]

    # Figure 1
    fig1 = px.line(filtered_df, x='Month', y=str(selected_year), title = 'Sales Revenue (1 Year)', height = 330, labels = {'Month':'', 'Revenue':''})
    fig1.update_layout(title_font_size = 13, yaxis = dict(tickfont = dict(size = 11)), xaxis = dict(tickfont = dict(size = 11)))
    fig1.update_layout({'plot_bgcolor':'white', 'paper_bgcolor':'white'})
    fig1.update_traces(line_color = '#2565AE')
    fig1.update_xaxes(showgrid = False)
    fig1.update_yaxes(showgrid = False)
    # Figure 2
    fig2 = px.bar(filtered_Custdf, x='Month', y=str(selected_year), title = 'Number of New Customers (1 Year)', height = 330, labels = {'Month':'', 'New Customers':''} )
    fig2.update_layout(title_font_size = 13, yaxis = dict(tickfont = dict(size = 11)), xaxis = dict(tickfont = dict(size = 11)))
    fig2.update_layout({'plot_bgcolor':'white', 'paper_bgcolor':'white'})
    fig2.update_xaxes(showgrid = False)
    fig2.update_yaxes(showgrid = False)
    fig2.update_traces(marker_color = '#66D3FA')
    fig2.add_trace(
                go.Scatter(x=months, y=target, mode = 'lines', line = go.scatter.Line(color = "gray"), showlegend = False)

            )
    
    return  fig1, fig2, ''







@callback(
    Output(component_id = 'index', component_property = 'children'), 
    Input('month-dropdown', 'value')

)




def getIndex(month):
    
    return f'Index: {months.index(month)}'
    
    

@callback(

    Output(component_id = 'monthRev', component_property = 'children'), 
    Input(component_id = 'index', component_property = 'children')

)


def updateRev(index):
    split_string = index.split(':')
    index_val = int(split_string[1])
    
    revenue_val = monthlyAverageRevenue[index_val]
    
    return f'Current Period: {revenue_val}'


@callback(
    Output(component_id = 'monthAC', component_property = 'children'), 
    Input(component_id = 'index', component_property = 'children')
)


def updateAC(index):
    split_string = index.split(':')
    index_val = int(split_string[1])
    
    AC_val = monthlyAcquisitionCosts[index_val]
    print(AC_val)
    
    return f'Current Period: {AC_val}'



@app.callback(
    Output('page-content', 'children'), 
    [Input('url', 'pathname')]

)

def display_page(pathname):
    if pathname == '/page-1':
        return layout2
    
    elif pathname == '/page-2':
        return layout1
    
    else:
        return index_page




    





if __name__ == '__main__':
    app.run_server(debug = True, use_reloader = False)


````









            
