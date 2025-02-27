# Gamestop_stock_vs_Tesla
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import warnings

import plotly.io as pio
pio.renderers.default = "browser"

warnings.filterwarnings("ignore", category=FutureWarning)

# 1: Use yfinance to Extract Stock Dat

def get_stock_data(ticker):
    stock = yf.Ticker(ticker)
    stock_data = stock.history(period="max")
    stock_data.reset_index(inplace=True)
    return stock_data
tesla_data = get_stock_data("TSLA")
print(tesla_data.head())

#2: Use Webscraping to Extract Tesla Revenue Data

tesla_revenue = pd.DataFrame(columns=["Date", "Revenue"])
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
response = requests.get(url)
html_data = response.text
soup = BeautifulSoup(html_data, 'html.parser')

table = soup.find_all('table')[1]  
table_body = table.find('tbody')
for row in table_body.find_all('tr'):
    cols = row.find_all('td')
    date = cols[0].text.strip()  
    revenue = cols[1].text.strip()  
    tesla_revenue = pd.concat([tesla_revenue, pd.DataFrame({"Date": [date], "Revenue": [revenue]})], ignore_index=True)

tesla_revenue["Revenue"] = tesla_revenue['Revenue'].str.replace(r',|\$', "", regex=True)
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""] 
print(tesla_revenue.tail())

 
# 3: Use yfinance to Extract Stock Data
gme = yf.Ticker("GME")
gme_data = gme.history(period="max")
gme_data.reset_index(inplace=True)
print(gme_data.head())


#4 Use Webscraping to Extract GME Revenue Data
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"
response = requests.get(url)
html_data_2 = response.text
soup = BeautifulSoup(html_data_2, 'html.parser')
table_body = soup.find_all("tbody")[1]
data = []
for row in table_body.find_all("tr"):
    cols = row.find_all("td")
    if len(cols) > 1:
        date = cols[0].text.strip()
        revenue = cols[1].text.strip().replace('$', '').replace(',', '')
        data.append([date, revenue])

gme_revenue = pd.DataFrame(data, columns=["Date", "Revenue"])

gme_revenue['Revenue'] = pd.to_numeric(gme_revenue['Revenue'], errors='coerce')

print(gme_revenue.tail())


# 5: Plot Tesla Stock Graph
def make_graph(stock_data, revenue_data, stock_name):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing=.3)

    stock_data_specific = stock_data[stock_data.Date <= '2021-06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']

    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date), 
                              y=stock_data_specific.Close.astype("float"), 
                              name="Share Price"), row=1, col=1)

    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date), 
                              y=revenue_data_specific.Revenue.astype("float"), 
                              name="Revenue"), row=2, col=1)

    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)

    fig.update_layout(showlegend=False, height=900, title=stock_name, xaxis_rangeslider_visible=True)
    fig.show()

make_graph(tesla_data, tesla_revenue, "Tesla")

#6: Plot GameStop Stock Graph
def make_graph(stock_data, revenue_data, stock_name):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing=.3)

    stock_data_specific = stock_data[stock_data.Date <= '2021-06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']

    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date), 
                              y=stock_data_specific.Close.astype("float"), 
                              name="Share Price"), row=1, col=1)

    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date), 
                              y=revenue_data_specific.Revenue.astype("float"), 
                              name="Revenue"), row=2, col=1)

    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)

    fig.update_layout(showlegend=False, height=900, title=stock_name, xaxis_rangeslider_visible=True)
    fig.show()
    
make_graph(gme_data, gme_revenue, "GameStop")

