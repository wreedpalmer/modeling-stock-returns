# modeling-stock-returns

- **This is a recent project that demonstrates my technical and quantitative research skills.**
- The Jupyter Notebook `model_volatility.ipynb` processes market and finacial data, and models stock returns.

## Data description

- **Market data**: In this project I assume the market data is available each day after the markets close. 
    - `openadj.csv`/`closeadj.csv`: the open/close price adjusted for corporate actions
    - `adv.csv`: average daily volume in dollars 
    - `market_cap.csv`: total market value in million dollars
    - `sectors.csv`: the sector classification of the instruments in this universe
    - if the market cap is null for a stock for one day, the stock is out of the universe
- **Other predictors**: These data have different update frequencies. Assume these predictors are available on their given date when markets open. 
    - `technical_indicator.csv`: derived from market data
    - `holding_indicator.csv`: the total value in dollars held by a group of institutional investors
    - `financial_indicator.csv`: two accounting statement items -- net income and sales in million dollars 
         - net income is cumulative over the course of the fiscal year (i.e. Q3 amount is given as Q1 + Q2 + Q3)
         - the sales figure is non-cumulative
         
         
## Project Outline
- **Process the data:** identify potential data issues and clean the data accordingly 
- **Explore the data:** look at the overall distribution and time-series dynamics of the data
- **Design a prediction model:** try to predict future stock returns based on all information available in the data, while prioritizing the following: 
   - avoid MARKET exposure
   - avoid SIZE exposure
   - avoid look-ahead bias.
   - don't want to be good at predicting small stocks and very bad at predicting large ones (small stocks are much more expensive to trade)
- **Conclusion:** summary of work and potential future ideas

## Modeling volatility in the daily log returns
- *Please see `model_volatility.ipynb` for the development of this model and its application to the data*
- As the volatility patterns of aggregated sector returns mirrors some of the volatility patterns of the overall aggregated returns, I believe the volatility patterns of the returns of individuals stocks may mirror the volatility patterns of their sectors
- This motivates my approach for modeling individual stock volatility. In particular I create a model based on the assumption that a given stock return's volatility is proportional to the volatility of the aggregated return across its sector.
- Let $r_{i,t}$ be the daily log return of stock $i$ on day $t$.
- Let $c_{j,t}$ be the daily aggregated log return for sector $j$ on day $t$.
- Let $s(i, t) \in \{0,...,10\}$ be the sector for stock $i$ at time $t$. **Note:** stock $i$'s sector can change so that $s(i, t)$ is not constant over time.
- Let $w_i$ be stock $i$'s volatility proportionality weight.
- The key assumption is:


$$ \text{stock $i$'s sector volatility} \approx w_i * \text{stock $i$'s volatility}\,\, \Longleftrightarrow\,\, \text{Var}\,c_{s(i,t),\, t} \approx w_i^2 * \text{Var}\,r_{i, t}$$


- Let $\text{cv}_{j,t}$ be the conditional volatility of sector $j$ on day $t$ estimated by fitting the GARCH(1,1) model
- Define stock $i$'s adjusted returns on day $t$ as 
$$r^{\text{adj}}_{i,t} = \frac{r_{i,t}}{\text{cv}_{s(i, t),t}}$$
- Then
$$\text{Var}\,r^{\text{adj}}_{i,t} \approx \frac{1}{w_i ^ 2}$$
- Estimate the squared weights $w_i^2$ using the sample variances of the adjusted returns.

**Advantages of this approach:**
- Estimating a GARCH(1,1) model separately for each stock does not take advantage of the shared volatility patterns.
- Moreover, stocks come in and out of the universe and this makes estimating individual stock volatility separately much more difficult
- With this approach we get a volatility estimate for each $(i,t)$ stock-date pair in the training data.
- Using forecasts from the estimated sector-aggregated GARCH(1,1) models, we can establish forecasts for the volatility of indidual stocks. For a $k$-day ahead forecast and with a final training date $T$, we have

$$\hat{\text{Var}}\,r_{i, \, T + k} = \frac{\hat{\text{Var}}\,c_{s(i, T + k),\, T + k}}{\hat{w_i}^2}$$

- Fitting GARCH(1,1) models to aggregated returns has advantages in stability and computational complexity.
- And these advantages become more pronounced when making the rolling window, 1-day look ahead predictions below.
- Dividing by aggregated conditional volatility dampens the effect of market volatility in the adjusted returns.  Working with the adjusted returns should help mitigate MARKET exposure.

**Disadvantages of this approach:**
- The principal disadvantage is that a given stock return's volatility is not always proportional to the volatility of the aggregated returns across its sector (that is, the model's central assumption is too strong).

