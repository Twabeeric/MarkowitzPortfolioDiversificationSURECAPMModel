# Markowitz-Portfolio-Diversification-with-SURE-CAPM-Model
 A model to show the effects of diversification on a Dow Jones 30 stock portfolio with a SURE CAPM model without the intercept and with Gaussian errors

## Methodology
1. We form 6 portfolios. Each portfolio has 5, 10, 15, 20, 25 and 30 stocks. The smaller set of assets should be a subset of the larger set. For example, if you have IBM in the set of 5 stocks, you must include IBM in the portfolios of 10, 15, 20, 25 and 30 stocks.

2. We assume that the assume stock premium is explained by the SURE CAPM model without the intercept and with Gaussian errors.

3. Now for each portfolio, use the default training sample prior. 

4. We compute the optimal portfolios for each of the groups of assets at the target portfolio return level. We present the weights of each asset in each portfolio as well as the standard deviation of the optimal portfolios. 

5. Use ggplot to plot the standard deviation vs. the number of stocks in the assets. 
