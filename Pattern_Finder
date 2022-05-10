import mplfinance as mpf
import pandas as pd
import datetime
import requests
import json

"""
input:   Python dictionary containing the parameters for the Binance API
returns: Pandas dataframe that maps the requested kline data  
"""
def getKlineData(parameters):
    klineData = json.loads(requests.get(url, params=parameters).text)
    df = pd.DataFrame(klineData)
    
    df.columns = ['time_period_start', 'Open', 'High', 'Low', 'Close', 'delete1', 'delete2', 'delete3', 'delete4', 'delete5', 'delete6', 'delete7']
    del df['delete1']; del df['delete2']; del df['delete3']; del df['delete4']; del df['delete5']; del df['delete6']; del df['delete7']

    for x in range(0,500):
        df.loc[x, ['time_period_start']] = datetime.datetime.fromtimestamp(float(df['time_period_start'][x]) / 1e3)

    df = df.set_index("time_period_start")

    df[['Open','High','Low','Close']] = df[['Open','High','Low','Close']].astype('float')


    return df

"""
input:  Dataframe holding the kline data
output: Python list holding the timestamps in which a shooting star formation is present at
"""    
def markShootingStarTimestamps(klineDF):
    timestamps = []
    for x in range(0,len(klineDF)):
        pgChange = abs(((klineDF.iloc[x]['Close'] - klineDF.iloc[x]['Open']) / klineDF.iloc[x]['Close']) * 100)
        if pgChange < 0.55:
            if klineDF.iloc[x]['Close'] > klineDF.iloc[x]['Open']:
                lowNeedle = abs(((klineDF.iloc[x]['Open'] - klineDF.iloc[x]['Low']) / klineDF.iloc[x]['Open']) * 100)
                if lowNeedle < 0.4:
                    highNeedle = abs(((klineDF.iloc[x]['High'] - klineDF.iloc[x]['Close']) / klineDF.iloc[x]['High']) * 100)
                    if highNeedle > 1:
                        pgChangeNext = ((klineDF.iloc[x+1]['Close'] - klineDF.iloc[x+1]['Open']) / klineDF.iloc[x+1]['Close']) * 100
                        if pgChangeNext > 1.5:
                            pass
                        else:
                            timestamps.append(klineDF.index[x])
            else:
                lowNeedle = abs(((klineDF.iloc[x]['Close'] - klineDF.iloc[x]['Low']) / klineDF.iloc[x]['Close']) * 100)
                if lowNeedle < 0.4:
                    highNeedle = abs(((klineDF.iloc[x]['High'] - klineDF.iloc[x]['Open']) / klineDF.iloc[x]['High']) * 100)
                    if highNeedle > 1:
                        pgChangeNext = ((klineDF.iloc[x+1]['Close'] - klineDF.iloc[x+1]['Open']) / klineDF.iloc[x+1]['Close']) * 100
                        if pgChangeNext > 1.5:
                            pass
                        else:
                            timestamps.append(klineDF.index[x])
    
    return timestamps                

"""
input:   Original dataframe
returns: Pandas dataframe to be used to initialize an additional plot  
"""
def getShootingStarDataFrame(ogDF):
    timestamps = markShootingStarTimestamps(ogDF)
    ap = [float('nan')]*len(ogDF)
    apdf = pd.DataFrame(dict(point=ap),index=ogDF.index)
    
    for x in timestamps:
        apdf.loc[x] = (ogDF.loc[x]['Open'] + ogDF.loc[x]['Close']) / 2
    
    return apdf

"""
input:  Original dataframe, Dataframe for the initialization of the additional plot
output: Plots the chart
"""   
def plotMarkedChart(ogDF, apDF):
    ap = mpf.make_addplot(apDF, type='scatter', markersize=200, marker='.', color='#FFFF00')
    
    mpf.plot(
    ogDF,
    type = 'candle',
    style = 'yahoo',
    addplot = ap
    )


def main():
    print("Enter the symbol:", end = " ")
    symb = str(input())    
    print("Enter the interval:", end = " ")
    intrvl = str(input())
    
    parameters = {'symbol': symb, 'interval':intrvl}
    df = getKlineData(parameters)

    ssdf = getShootingStarDataFrame(df)

    plotMarkedChart(df, ssdf)
    
    
url = "https://api.binance.com/api/v3/klines"
main()
