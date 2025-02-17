# Simple-Stock-Portfolio-Optimization-wt-CVXPY-

Over the past decade as an active stock trader, I’ve learned that constructing a robust investment portfolio begins with a deep understanding of your individual risk tolerance. Equally important is having a clear, well-defined strategy before committing any capital. Personally, I embrace a high-risk, high-return approach and have developed the discipline to view potential losses as temporary setbacks rather than terminal events. However, managing emotions during market downturns is easier said than done, and not everyone can maintain a level head when investments experience significant declines.

This post outlines a conservative strategy tailored for investors who favor blue-chip stocks—assets known for their stability and modest returns. Once you’ve identified your target stocks, the next challenge is determining the optimal allocation of your funds to maximize returns while managing risk.

By framing this allocation challenge as a convex optimization problem, the code below demonstrates how to employ the cvxpy library to derive an optimal investment strategy. This is a common method in Modern Portfolio Theory (MPT), that finds the Minimum Variance Portfolio (MVP) subject to a required return constraint.

## Data Description

Assuming you plan to invest in Apple, Microsoft, Amazon, Google, and Ndivia the first step is to gather historical market data. Using the _yfinance_ package, we retrieved daily closing prices for these stocks over the past 10 years. As of February 10, 2025, the dataset consists of 2,541 rows and 5 columns, representing the daily closing prices for each stock. Depending on your capital allocation cadency strategy, you can change the data range to just months.

<img width="504" alt="Screenshot 2025-02-16 at 5 18 10 PM" src="https://github.com/user-attachments/assets/e52251e2-9700-4d4a-980e-c5a0b947d6b5" />


## Algorithm implemented

The goal of this optimization problem is to minimize portfolio risk (variance) for a given target return. 
**The objective function:**

<img width="452" alt="Screenshot 2025-02-16 at 5 24 27 PM" src="https://github.com/user-attachments/assets/987ce3b4-6c12-49f0-81e8-f6cb0ee04242" />


where:

	•	 x  is the vector of asset weights (proportions of total investment).
	•	 C  is the covariance matrix of asset returns.
	•	 x^T C x  represents the portfolio variance (risk).

**Constraints:**

  1.	Full Investment Constraint:

<img width="473" alt="Screenshot 2025-02-16 at 5 25 25 PM" src="https://github.com/user-attachments/assets/e09b48c5-9a56-4f93-8bf8-bbbe31d93556" />


All available capital must be fully allocated to the selected assets.

	2.	Return Constraint:

<img width="472" alt="Screenshot 2025-02-16 at 5 25 53 PM" src="https://github.com/user-attachments/assets/a577f87e-f035-4aaf-8d0c-2aa419c4ed5a" />


The portfolio’s expected return must be at least the target return (\text{req\_return}).

	3.	Non-negativity Constraint:

<img width="463" alt="Screenshot 2025-02-16 at 5 26 18 PM" src="https://github.com/user-attachments/assets/26f6a3f6-3042-4892-b4ef-2eb9d1124d37" />


No short selling is allowed (i.e., all portfolio weights must be non-negative).

This is a Quadratic Programming probelm using cvxpy solver. First, it loops through monthly prices to calculate returns for each stocks, then stores the results in mr DataFrame. 
The Expected Returns (r) is the mean return for each asset. 
Covariance Matrix (C) measure how each pair of assests' returns move together. Once the returns for each asset are stored in the matrix, where rows represent time period and columns represent assets, the covariance matrix is computed using NumPy’s np.cov() function. This matrix is crucial for understanding portfolio risk because it captures how the returns of different assets co-vary.
There isn't a risk constraint in this model. Instead, risk is minimized through the objective fuction. The required return constraint ensures that, while risk is minimized, the portfolio must still achieve at least a specified level of return.

## Outcomes

<img width="407" alt="Screenshot 2025-02-16 at 4 48 19 PM" src="https://github.com/user-attachments/assets/8c1ea6a0-777f-4092-9216-dcb62166d41d" />




![image](https://github.com/user-attachments/assets/1643013b-08d8-4ca8-9d28-2a14c8ff0520)

**Understanding Expected Return and Optimal Portfolio Allocation**

The Expected Return is expressed as a percentage change, not an absolute dollar change. For example:

	•	If NVIDIA’s stock price is low, a $10 price movement represents a large percentage change.
 
	•	If NVIDIA’s stock price is high, the same $10 movement represents a smaller percentage change.

For instance, if the Expected Return is 0.15 (15%), it means that, on average, NVIDIA’s daily percentage return is around 15%. Such a high return could occur if the stock price is low or highly volatile.

**Optimal Portfolio Allocation (Last 10 Years)**

Based on the Expected Return of each asset over the last 10 years, the optimal solution recommends the following allocation for maximum return:

	•	NVIDIA (NVDA): 54.8%
 
	•	Amazon (AMZN): 26.58%
 
	•	Apple (AAPL): 15.6%
 
	•	Microsoft (MSFT): 3%
 
	•	Google (GOOGL): 0%

**Impact of Required Return Constraint**

	•	The highest daily return observed among the five stocks is 0.002676 (0.2676%).
	•	If you set the Required Return above 0.002 (0.2%), no optimal solution will be found due to the constraint being too high.
	•	If the Required Return is set below 0.002, the allocation will adjust accordingly to achieve the new target return while minimizing risk.

In summary, portfolio optimization involves balancing expected returns and risk constraints. Adjusting the required return significantly impacts asset allocation, often shifting investments toward higher-return but riskier stocks like NVIDIA.
