# Create an interactive data app using Streamlit

I actually like the [Streamlit](https://streamlit.io/) after I tried it out to deploy my models. I like the smart code of Streamlit to make an interactive data apps for the visual charts.

In this article, We'll use [Streamlit](https://streamlit.io/) to create an interactive data app; exploring a stock market data for different symbols and quickly visualize the stock prices with technical indicators.

![Alt Text](streamlit-interactive-data-app.gif)

> Note: The writing of this article is only aimed at demonstrating the steps to create an interactive data app using Streamlit. It doesn't serve any purpose of promoting any stock.

## Streamlit

[Streamlit](https://streamlit.io/) is an open-source Python library that makes it easy to create and share beautiful, custom web apps for machine learning and data science.

In just a few minutes you can build and deploy powerful data apps - so let's get started!

## Setup Environment

1. Make sure that you have [Python 3.6+](https://www.python.org/downloads/) installed.

2. Install Streamlit using PIP, Add below dependencies to the requirements file:

    ```bash
    streamlit
    pandas_datareader==0.10.0
    plotly==5.2.2
    cufflinks==0.17.3
    ```

3. Now, Install the required dependencies:

    ```bash
    pip install -r requirements.txt
    ```

Now that we have set up our environment, let's dive into more of how Streamlit works and how to build great data apps.

## Create an app

1. The first step is to create a new Python script. Let's call it `app.py`.

2. Open `app.py` in editor, then add these lines:

    ```python
    import datetime
    import streamlit as st
    import pandas_datareader as pdr
    import cufflinks as cf
    ```

3. Add page configuration:

    ```python
    APP_NAME = "Stock App!"
    st.set_page_config(
        page_title=APP_NAME,
        layout="wide",
        initial_sidebar_state="expanded",
    )
    ```

4. Every good app has a title, so let's add one:

    ```python
    st.sidebar.title(APP_NAME)
    ```

5. Now it's time to run Streamlit from the command line:

    ```bash
    streamlit run app.py
    ```

As usual, the app should automatically open in a new tab in your browser.

## Read Stocks data

Now that we have an app, the next thing we'll need to do is read the stock market data for specific stock symbol (ticker).

Let's define the list of tickers and assign it to the Streamlit select widget:

```python
# List of tickers
TICKERS = ['FB', 'AMZN', 'AAPL', 'NFLX', 'GOOGL', 'MSFT']

# Select ticker
ticker = st.sidebar.selectbox('Select ticker', sorted(TICKERS), index=0)
```

Now let's set the start and end point to fetch data:

```python
# Set start and end point to fetch data
start_date = st.sidebar.date_input('Start date', datetime.datetime(2021, 1, 1))
end_date = st.sidebar.date_input('End date', datetime.datetime.now().date())
```

We'll use [pandas-datareader](https://pydata.github.io/pandas-datareader/) to read data, which extract data from various Internet sources into a pandas DataFrame.

```python
# Fetch the data for specified ticker e.g. AAPL from yahoo finance
df_ticker = pdr.DataReader(ticker, 'yahoo', start_date, end_date)
```

Now that we have fetch the data successfully from yahoo finance using `pandas_datareader`. Let's inspect the stock data.

## Inspect the raw data

It's always a good idea to take a look at the raw data before start working with it. Let's add some code here and write raw data to the app:

```python
if st.checkbox('Show raw data'):
    st.subheader('Raw data')
    st.write(df_ticker)
```

Here `st.write` will render almost anything you pass to it. In this case, we're passing in a dataframe and it's rendering as an interactive table.

## Visualize data

We'll use a powerful yet user-friendly plot library, [Cufflinks](https://github.com/santosjorge/cufflinks) to visualize data. It is an open-source library that is specially dedicated to financial visualization. This library combines the power of [Plotly](https://plotly.com/python/) with the flexibility of Pandas for easy plotting.

```python
# Interactive data visualizations using cufflinks
# Create candlestick chart 
qf = cf.QuantFig(df_ticker, legend='top', name=ticker)
```

As mentioned above, Cufflinks is dedicated to financial visualization. It can expedite the process of building visualization for some common technical indicators such as moving average, Bollinger Band, RSI.

```python
# Technical Analysis Studies can be added on demand
# Add Relative Strength Indicator (RSI) study to QuantFigure.studies
qf.add_rsi(periods=20, color='java')

# Add Bollinger Bands (BOLL) study to QuantFigure.studies
qf.add_bollinger_bands(periods=20,boll_std=2,colors=['magenta','grey'],fill=True)

# Add 'volume' study to QuantFigure.studies
qf.add_volume()

fig = qf.iplot(asFigure=True, dimensions=(800, 600))
```

Finally, render figure using `st.plotly_chart`:

```python
# Render plot using plotly_chart
st.plotly_chart(fig)
```

## Let's put it all together

That's it, We've made it to the end. Here's the complete script for our interactive data app.

```python
import datetime
import streamlit as st
import pandas_datareader as pdr
import cufflinks as cf

APP_NAME = "Stock App!"

# Page Configuration
st.set_page_config(
    page_title=APP_NAME,
    layout="wide",
    initial_sidebar_state="expanded",
)

# Add some markdown
st.sidebar.markdown("Made with love using [Streamlit](https://streamlit.io/).")
st.sidebar.markdown("# :chart_with_upwards_trend:")

# Add app title
st.sidebar.title(APP_NAME)

# List of tickers
TICKERS = ['FB', 'AMZN', 'AAPL', 'NFLX', 'GOOGL', 'MSFT']

# Select ticker
ticker = st.sidebar.selectbox('Select ticker', sorted(TICKERS), index=0)

# Set start and end point to fetch data
start_date = st.sidebar.date_input('Start date', datetime.datetime(2021, 1, 1))
end_date = st.sidebar.date_input('End date', datetime.datetime.now().date())

# Fetch the data for specified ticker e.g. AAPL from yahoo finance
df_ticker = pdr.DataReader(ticker, 'yahoo', start_date, end_date)

st.header(f'{ticker} Stock Price')

if st.checkbox('Show raw data'):
    st.subheader('Raw data')
    st.write(df_ticker)

# Interactive data visualizations using cufflinks
# Create candlestick chart 
qf = cf.QuantFig(df_ticker, legend='top', name=ticker)


# Technical Analysis Studies can be added on demand
# Add Relative Strength Indicator (RSI) study to QuantFigure.studies
qf.add_rsi(periods=20, color='java')

# Add Bollinger Bands (BOLL) study to QuantFigure.studies
qf.add_bollinger_bands(periods=20,boll_std=2,colors=['magenta','grey'],fill=True)

# Add 'volume' study to QuantFigure.studies
qf.add_volume()

fig = qf.iplot(asFigure=True, dimensions=(800, 600))

# Render plot using plotly_chart
st.plotly_chart(fig)
```

## Deploy an app

Now that we have created our data app, and ready to share it! We can use [Streamlit Cloud](https://streamlit.io/cloud) to deploy and share our app. Streamlit Cloud has multiple tiers, the free Community tier is the perfect solution if your app is hosted in a public GitHub repo and you'd like anyone in the world to be able to access it.

You can check steps to deploy apps with the free Community tier [here.](https://docs.streamlit.io/en/stable/deploy_streamlit_app.html).

## Conclusion

Thank you for reading. I hope this article was valuable to you and you enjoyed creating an interactive data app using Streamlit.

Streamlit claims to be "the fastest way to build and share data apps", so it has to be intuitive and quick. All the commands can be summarized in this one page of [cheat sheet](https://share.streamlit.io/daniellewisdl/streamlit-cheat-sheet/app.py).

Go ahead and clone the repos [streamlit-data-app](https://github.com/imnileshd/streamlit-data-app) to view the full code of the project.

Happy Coding!




------------------------------------------------------------------------------------------------

# Investment Platform

This project provides a platform for collaborative investments in stocks and cryptocurrencies. It allows users to search for assets, make investments, create collaborative investment requests, track portfolios, and display data with technical indicators. The app uses **Streamlit**, **Plotly**, and various APIs (such as **Tiingo** for stock data and **CoinCap**, **Cryptopanic** for cryptocurrency data) for real-time data fetching and visualization.

## Features

- **User Authentication**: Secure login and registration system for managing portfolios and investments.
- **Real-Time Data Fetching**: Integration with external APIs for live stock and cryptocurrency data.
- **Portfolio Management**: View and manage investment portfolios with detailed information on holdings.
- **Investment Transactions**: Buy and sell stocks and cryptocurrencies with real-time portfolio updates.
- **Historical Data Visualization**: Display historical price data with charts and graphs for analysis.
- **Watchlist Feature**: Add stocks and cryptocurrencies to a watchlist for easy tracking.
- **Collaborative Investments**: Initiate and accept collaborative investment requests with other users.
- **Pending Requests Management**: View and manage pending collaborative investment requests.
- **Investment Breakdown Visualization**: See a breakdown of contributions and investment status after acceptance.
- **Sentiment Analysis Visualization**: Display sentiment analysis results in a pie chart format, providing insights into market sentiment based on news data.
- **Responsive Design**: User-friendly and responsive design for a seamless experience across devices.


## Technologies Used


- **Python**: The primary programming language used for backend development.
- **Streamlit**: A framework for building interactive web applications in Python.
- **Pandas**: A data manipulation and analysis library for handling data in DataFrames.
- **NumPy**: A library for numerical computations in Python.
- **Matplotlib**: A plotting library for visualizing data.
- **Plotly**: A graphing library for creating interactive plots and visualizations.
- **TextBlob**: A library for processing textual data, used for sentiment analysis.
- **CSV**: A module for reading and writing CSV files for data storage.
- **Requests**: A library for making HTTP requests to fetch live data from external APIs.
- **APIs**: Integration with external APIs for real-time and historical investment data.


## Installation

### Step 1: Clone the repository

```bash
git clone https://github.com/yourusername/collaborative-investment.git
cd collaborative-investment
```

### Step 2: Set up a virtual environment
Create a virtual environment to isolate your project's dependencies. Run the following commands based on your operating system:

For Mac/Linux:
```bash
python3 -m venv .venv
source .venv/bin/activate
```
For Windows:
```bash
python3 -m venv .venv
.venv\Scripts\activate
```

### Step 3: Install dependencies
Install the required Python packages using pip:

```bash
pip install -r requirements.txt
```

### Step 4: Run the app
Now, you can run the Streamlit app using the following command:

```bash
streamlit run app.py
```


## Usage


- **Login or Sign Up**: When the app starts, you'll be prompted to either sign up for a new account or log in to an existing one.
- **Search for Stocks or Cryptos**: Navigate to the Home tab where you can search for stocks or cryptocurrencies by name or symbol.
- **Make an Investment**: Once you select an asset, you can view its real-time data and make an investment using the virtual balance.
- **Portfolio and Watchlist**: You can view your investments and manage your watchlist for stocks and cryptocurrencies in the Portfolio and Watchlist tabs.
- **Collaborative Investments**: Navigate to Collaborative Investments, where you can create a request to invest together with other users. You can contribute to a shared investment by specifying the amount.


