# Trading-Algorithm
Trading algorithm for hackaton

""


import math

def volatility_rebound_buy(candles, period):

    """
    
    Strategy to identify buying opportunities based on sharp price drops 
    followed by increased volatility.
    
    Args:
        candles (list): A list of candle objects containing open, close, and standard deviation.
        period (int): The number of candles to calculate average volatility.
    
    Returns:
        dict: A response indicating whether to buy and, if so, the price and amount.
    """
    if len(candles) < period + 1:
        raise ValueError("Not enough candles to analyze for the given period.")

    current_candle = candles[-1]
    most_recent_candle = candles[-2]
    
    price_drop = most_recent_candle["open"] - current_candle["open"]

    avg_volatility = sum(candle["std_dev"] for candle in candles[-period:]) / period

    volatility_increase = current_candle["std_dev"] / avg_volatility

    A = config["strategy2"]["A"]  
    B = config["strategy2"]["B"]  

    if (
        price_drop > A
        and volatility_increase > B
        and most_recent_candle["close"] < most_recent_candle["open"]
    ):
        return {
            "buy": True,
            "price": current_candle["open"],
            "amount": config["account"]["baseOrderValue"] * price_drop,
        }

    return {"buy": False}

""
    
Core Idea

This strategy identifies a buying opportunity when:
	1.	The stock’s price has dropped sharply compared to the previous candle (price drop detection).
	2.	There’s an increase in market volatility, as measured by the standard deviation of recent candles.

How It Works
	1.	Price Drop Calculation:
	•	Measure the difference between the most recent candle’s open price and the current candle’s open price.
	•	This ensures the strategy reacts to significant downward movements in price.
	2.	Volatility Increase:
	•	Compute the average volatility (standard deviation) of the last N candles.
	•	Compare the current candle’s volatility to this average. A higher ratio means the market is experiencing increased activity, which often precedes price rebounds.
	3.	Triggering the Buy Signal:
	•	A buy is triggered if:
	•	The price drop exceeds a threshold (A).
	•	Volatility has increased by a factor of B or more compared to the average.
	•	The most recent candle is bearish (close price < open price) to confirm downward momentum.
 
Why It’s Effective
	1.	Price and Volatility as Signals:
	•	Sharp price drops often signal undervaluation or panic selling.
	•	Increased volatility suggests growing market interest, which could lead to a price recovery.
	2.	Avoiding False Signals:
	•	By requiring both conditions (price drop and volatility increase), the strategy avoids buying during insignificant market noise.
	3.	Configurable and Adaptive:
	•	Thresholds for price drop and volatility (A and B) can be adjusted based on market conditions or asset type, making it versatile.
