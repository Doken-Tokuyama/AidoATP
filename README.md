Isis ATP
========

Isis Advance Trading Platform courtesy of the Open Payment Alliance.

forked from openpay/OpenPay 

What is IsisATP?
================

IsisATP is an Automated Trading Platform primarily used for trading bitcoins (BTC) on various bitcoin exchanges.

How does it work?
=================

On startup IsisATP goes into a learning mode for a configurable amount of time where it collects market data to be used by the trading algorithms. After the learning period is over the trading algorithms make a decision on whether it is favourable to trade at any given time. The trading algorithms currently implemented are:

Arbitrage algorithm:
--------------------

Quite frequently there is a difference in the price of BTC in different currency pairs.

For instance, at time of writing, USD/BTC rate is $11.80 whereas the EUR/BTC rate is �9.14

At the same time the USD/EUR rate is 0.77 and the EUR/USD rate is 1.29

You could buy BTC in USD, then turn around and sell that same BTC back into EUR and make a profit of 0.52 EUR.  Next you buy BTC with all the 0.52EUR profit you just made and sell that BTC back to USD for a profit of 0.67USD

	1 BTC = 11.80 USD
	1 BTC = 9.14 EUR

	1 USD = 0.76 EUR
	1 EUR = 1.29 USD

The arbitrage engine uses the current trading algorithm to find the highest profit place to sell when it detects that the market conditions are right to sell.  

Buys take place as normal, but only do so on the pair with the lowest cost real cost (BTCAsk * normalizing factor (pair1/pair2))

Advance/Decline Spread Algorithm
--------------------------------

A simple Advance/Decline Spread algorithm is used to interpret the breadth of the market. This oscillator is extremely fast, so a moving average is usually applied to slow the signals.

Simple Moving Average based trend following algorithm
-----------------------------------------------------

Coming soon

Exponential Moving Average based trend following algorithm
----------------------------------------------------------

Coming soon

Volume Participation Algorithm
------------------------------

The trend observer functionality constantly monitors the market for trends. A combination of the Advance/Decline Spread, SMA and EMA algorithms decide what way the market is trending.

	Market Trending Down = Look at buying
	Market Trending Up = Look at selling 

Once it is decided the trend is up (ask) or down (bid) it then compares the last transaction to the VWAP.

The ratio of last price versus VWAP is used as a waterline to make the final determination that we will take an action. 

	If trend = down & last < VWAP then buy
	If trend = up & last > VWAP then sell

Once we have decided to buy or sell, then we look at the ask, bid and trend arrows calculated from the Advance/Decline Spread Algorithm. This gives us an indication of how much momentum is in the current trend and is used to calculate a weight.

	current balance (BTC or local currency depending on Bid or Ask) * weight = how much we will be trading with.


Exchanges
=========

IsisATP currently trades on the MtGOX exchange. More exchanges are planned.

Risk
====

The user has a choice of high risk or conservative trading.

The Trend Observer calculates the Advance/Decline spread for ask, bid and last prices to give a askArrow, bidArrow and trendArrow values. These values are then used to calculate a weight.

The weight is calculated one of two ways based on the choice of high risk or conservative and compared to stop loss value. If the calculated weight is above the stop loss value, weight is reduced to the configured stop loss value.

Here's how the weight is calculated for bid/buy:

	if(algorithm == 1) {
		weight = (askArrow + trendArrow) / ticker.size();
	}else {
		weight = (askArrow / ticker.size()) * (trendArrow / ticker.size());
	}
			
Algorithm 1 is high risk, algorithm 2 is conservative risk

Usage
=====

IsisATP may be launched using the following command line:

	java -jar aido.jar

When run for the first time a configuration needs to be created. This can be done by adding the --clear-config switch and the user will then be interviewed:

	java -jar aido.jar --clear-config=true

The following switches are also available from the command line:

	--simulation-mode=true/false	Enable or disable a test/simulation mode where live trades will not be performed
	--use-arbitrage=true/false		Enable or disable the arbitrage trading engine
	--use-trend=true/false			Enable or disable the trend-following trading agent

The configuration file is stored in the ~/.java/.userPrefs/org/open/payment/alliance/isis/atp/prefs.xml file on Linux/UNIX systems or the HKEY_CURRENT_USER\Software\JavaSoft\Prefs\org\open\payment\alliance\isis\atp registry on Microsoft Windows systems.

When interviewed the user will be asked for the following information:

	MtGOX API key
	MtGOX secret key
	ISO Code for Prefered Currency (i.e. USD, GBP, JPY, EUR etc)
	Maximum number of bitcoins to trade in a single order
	Minimum number of bitcoins to trade in a single order
	Maximum amount of local currency to trade in a single order
	Minimum amount of local currency to trade in a single order
	Overall maximum loss tolerance (eg 25% = 0.25)
	Enable Arbitrage trading engine
	Minimum Profit to seek for Arbitrage (eg 10% = 0.10)
	Enable Trend-following trading engine
	Minimum ticker size for trend following trade decisions
	Maximum ticker age for trend following trade decisions (in minutes)
	Use Advance/Decline Spread algorithm
	Use Simple Moving Average algorithm (SMA)
	Use Exponential Moving Average algorithm (EMA)
	Number of ticks used to calculate short Moving Average
	Trading fee (eg 0.6% = 0.006)
	Which risk algorithm would you like to use? (1 or 2)
		1: High Risk
		2: Conservative
		
If used, the --use-arbitrage and --use-trend command line switches will over-ride their respective configuration file values. 
		
Further reading
===============

A detailed discussion about this project may be found at: https://bitcointalk.org/index.php?topic=109831.0