# HASTOC
#Coding HA Strategies/Technical Analysis Strategies:Development of Heiken Ashi Stochastic/https://mpra.ub.uni-muenchen.de/90439/5/MPRA_paper_90439.pdf
#This a the code for the HASTOC strategies described in the above mentioned paper (see link)
#Based on the  Japanese  Heiken  Ashi  (HA)  candles  we  develop  the  Heiken  Ashi  stochastic  or  HASTOC, which can reflect in a single number both the likelihood of #trend generation and momentum in the market. We back test ten strategies using HA and HASTOC for three currencies, one index and one  currency  for  three  major  time  #frame  in  each  market.  We  find  the  strategies  give  consistent profits for these markets. The study contributes to economic literature as well as to practical #trading scenario by  the development of a  new indicator, HASTOC which in  a single number,  gives the trend generation and momentum in the market
#Heiken Ashi Strategies: Development of HA stochastic: Dr. Smita Roy Trivedi
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline
import datetime
import pandas_datareader as webstart= datetime.datetime(2010,1,1)
end=datetime.datetime(2020,7,16)
#Getting data
NIFTY = web.DataReader('^NSEI','yahoo',start, end)
#Coding HA
NIFTY['HA_Close']=NIFTY[['Open','High','Close','Low']].mean(axis=1)
NIFTY['ha_open']=np.nan
for i in range(0, len(NIFTY)):
        if i == 1:
            NIFTY['ha_open'][i]= ( (NIFTY['Open'][i] + NIFTY['Close'][i] )/ 2)
        else:
            NIFTY['ha_open'][i] = ( (NIFTY['ha_open'][i-1] + NIFTY['HA_Close'][i-1] )/ 2)
#coding HASTOC(please see linkeed article for details)
NIFTY['HA_high']=NIFTY.max(axis=1)
NIFTY['HA_low']=NIFTY.min(axis=1)
NIFTY['diff_t']=NIFTY['HA_Close']-NIFTY['ha_open']
NIFTY['has_min']=NIFTY.rolling(10,10)['diff_t'].min()
NIFTY['has_max']=NIFTY.rolling(10,10)['diff_t'].max()
NIFTY['hastoc']=(NIFTY['diff_t']-NIFTY['has_min'])/(NIFTY['has_max']-NIFTY['has_min'])
NIFTY['hastoc'].mean()#simple hatoc STRATEGY
NIFTY['bst_1']=np.where((NIFTY['diff_t']<0)&(NIFTY['hastoc']<0.1),1,0)
NIFTY['st_1']=NIFTY['bst_1'].diff()
NIFTY['log_returns']=np.log(NIFTY['Close']/NIFTY['Close'].shift(1)) 
NIFTY['st_ret_1']=NIFTY['st_1'].shift(1)*NIFTY['log_returns']
st1_aret= round(NIFTY['st_ret_1'].mean() *252, 4)
print(str(st1_aret*100)+'%')
