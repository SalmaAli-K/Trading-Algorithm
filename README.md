# Trading-Algorithm
Trading algorithm for hackaton

""


def momentum_reversal(candles, period):
    
    """
    
    Strategy to detect momentum reversals after a strong downward trend.
    
    Args:
        candles (list): A list of candle objects containing open, close, high, and low prices.
        period (int): The number of candles to analyze for momentum.
    
    Returns:
        dict: A response indicating whether to buy and, if so, the price and amount.
    """
    if len(candles) < period + 1:
        raise ValueError("Not enough candles to analyze for the given period.")
    
    # Variables to track bearish momentum
    bearish_count = 0
    total_price_drop = 0

    # Analyze the last 'period' candles
    for i in range(-period - 1, -1):
        if candles[i]["close"] < candles[i]["open"]:  # Bearish candle
            bearish_count += 1
            total_price_drop += candles[i]["open"] - candles[i]["close"]
    
    # Calculate the average price drop per candle during the bearish period
    avg_price_drop = total_price_drop / max(bearish_count, 1)

    # Check for a bullish reversal on the most recent candle
    current_candle = candles[-1]
    if (
        current_candle["close"] > current_candle["open"]  # Bullish candle
        and (current_candle["close"] - current_candle["open"]) > avg_price_drop  # Significant range
        and bearish_count >= period // 2  # At least half the period was bearish
    ):
        return {
            "buy": True,
            "price": current_candle["open"],
            "amount": config["account"]["base

""
Momentum Reversal Strategy

Concept:

	1.	Tracks price trends over a moving window of candles.
	2.	Identifies when a price trend has been strongly downward but shows signs of reversing upward (e.g., bullish close after multiple bearish candles).
	3.	Triggers a buy when the reversal is confirmed.

Algorithm Explanation
	1.	Identify Downward Momentum:
	•	Check if the majority of the past N candles were bearish (close < open).
	•	Calculate the overall price drop during this bearish phase.
	2.	Reversal Confirmation:
	•	Look for a bullish candle (close > open) following the downward trend.
	•	Ensure the bullish candle’s range is significant compared to recent candles (e.g., greater than the average range).
	3.	Buy Signal:
	•	Trigger a buy when both conditions are met:
	•	Downward momentum over N candles.
	•	Clear reversal confirmed by the most recent bullish candle.

    
Advantages
	1.	Avoids Overreactions:
	•	Doesn’t buy on minor price drops or isolated volatility spikes, as it requires a full downward trend.
	2.	Higher Accuracy:
	•	Waits for a clear reversal signal before entering the market, reducing false positives.
	3.	Configurable:
	•	The period (N) can be adjusted for different timeframes or asset types.
