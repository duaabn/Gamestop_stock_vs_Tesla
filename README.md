# 📊 Tesla vs GameStop — Interactive Data Analysis

**Interactive analysis of Tesla (TSLA) and GameStop (GME) stocks using Python.**  
This project uses `yfinance`, `BeautifulSoup`, and `Plotly` to collect, clean, and visualize real stock prices and revenue data.

---

## 🧠 Overview
- Download **stock prices** from Yahoo Finance using `yfinance`
- Scrape **revenue data** from IBM Skills Network pages using `BeautifulSoup`
- Clean and merge datasets with `pandas`
- Visualize both **price** and **revenue** trends using interactive `Plotly` charts

---

## 🚀 How to Run (in Colab or locally)
### 🔹 Option 1: Google Colab
You can run this notebook directly in Colab:  
[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/duaabn/Gamestop_stock_vs_Tesla/blob/main/Gamestop_vs_Tesla_Analysis.ipynb)

### 🔹 Option 2: Local Environment
```bash
pip install yfinance plotly beautifulsoup4 lxml pandas requests
python src/main.py
