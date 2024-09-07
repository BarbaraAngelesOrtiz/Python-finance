# Python-finance
## How to Take Advantage of RSI and EMA

Many times I have wondered how to automate trading processes through programming. After spending hours observing the charts and understanding the buying and selling conditions according to each sector, I soon learned that there is no secret formula that applies to all sectors. Each specific area of ​​action has a different statistical behavior and I will continue to modify my formula by trial and error, but it would be much easier with indicators that would serve as Flags. So I got to work and found two main tools, there are several but I felt confortable with them, the Yahoo Finance library and online spreadsheets Google Sheet to work online with my partner. For now I will share codes analyzing these two assumptions, creating a CSV file which will then be automatically loaded into my online spreadsheet through a script. Indicators that I will explain and calcule by python:

🔹The relative strength index (RSI) is a momentum oscillator that measures the speed and change of price movements. It helps traders identify overbought or oversold conditions in a market, indicating possible price reversals. They provide traders with information about the speed and magnitude of price changes, indicating whether an asset is overbought or oversold. One of the conditions is usually that if RSI < 30 it is a good time to buy and if RSI> 70 it is a good time to sell. However, it should be noted that these two conditions alone are not enough and more conditionals must be analyzed, the action analyzed, etc. 

🔹Other value really interesting is the Exponential Moving Average or EMA,which is a basic technical analysis indicator that is useful in determining the trend of an asset's value. The EMA is a way of smoothing price data to identify underlying trends more clearly and make trading decisions based on them.

🔹Unlike the Simple Moving Average (SMA), which calculates the simple arithmetic average of prices over a specific period, the EMA gives more weight to more recent prices. This means that the EMA reacts more quickly to changes in prices, making it especially useful in identifying emerging trends.

📌First step Install Yahoo Finance Library:

$ pip install yfinance

In this tutorial, I will try to minimize the use of unnecessary libraries:

import yfinance as yf
import pandas_ta as ta
import pandas as pd
import matplotlib.pyplot as plt

📌Define the list of stock tickers, "GOOGL" is for Google for example. Then define the time period and define the moving average periods for the RSI14, EMA20 and SMA20. To start create an empty DataFrame to store the indicators:

tickers = [ "AAPL","MSFT", "GOOGL", "AMZN", "TSLA"]

start_date = "2023-01-01"
end_date = "2024-01-01"

sma_period = 20
ema_period = 20
rsi_period = 14

indicators_data = pd.DataFrame()

 📌Define the list of stock tickers, "GOOGL" is for Google for example. Then define the time period and define the moving average periods for the RSI, EMA and SMA. To start create an empty DataFrame to store the indicators:

for ticker in tickers:
    data = yf.download(ticker, start=start_date, end=end_date)
    data[f'{ticker}_SMA_{sma_period}'] = data['Close'].rolling(window=sma_period).mean()
    data[f'{ticker}_EMA_{ema_period}'] = data['Close'].ewm(span=ema_period, adjust=False).mean()
    data[f'{ticker}_RSI_{rsi_period}'] = ta.rsi(data['Close'], length=rsi_period)
    
    data[f'{ticker}_Signal'] = 'Hold'
    data.loc[data[f'{ticker}_RSI_{rsi_period}'] < 30, f'{ticker}_Signal'] = 'Buy'
    data.loc[data[f'{ticker}_RSI_{rsi_period}'] > 70, f'{ticker}_Signal'] = 'Sell'
    
📌Loop through each ticker, download the data, and calculate the indicators for ticker in tickers. The next step is download the stock data and calculate the Simple Moving Average (SMA),the Exponential Moving Average (EMA) and RSI. Then initialize a 'Signal' column with 'Hold' as default, define the Buy and Sell conditions based on RSI and add the indicators data to the indicators_data DataFrame.

    if indicators_data.empty:
        indicators_data = data[[f'{ticker}_SMA_{sma_period}', f'{ticker}_EMA_{ema_period}', f'{ticker}_RSI_{rsi_period}', f'{ticker}_Signal']].copy()
    else:
        indicators_data = indicators_data.join(data[[f'{ticker}_SMA_{sma_period}', f'{ticker}_EMA_{ema_period}', f'{ticker}_RSI_{rsi_period}', f'{ticker}_Signal']])
        
📌Finally, display the combined indicators data, save the combined indicators data to a CSV file and optional inform the user that the CSV has been saved.

 print(indicators_data.head(20))

csv_filename = 'C:\Finance\Data.csv'
indicators_data.to_csv(csv_filename)

print(f"Indicators data saved to {csv_filename}")

Here the results:
![image](https://github.com/user-attachments/assets/fa1dc8fc-eb54-425f-a40d-7830b00b333d)

Please note that RSI data will appear after the 14th day of the beginning of its timeframe, as it was calculated as 14-interval RSI.

# Conclusion

Firstly, creating indicators may seem like a hard task, but with some powerful libraries these jobs are much easier and more indicators can be added as conditions.

This article is in no way a financial advice or a suggestion on when to enter or exit the market. The goal is to help you draw your own indicator, such as RSI and EMA.

Have fun playing around and generating more indicators and use them wherever it suits you best. 

Cheers!
