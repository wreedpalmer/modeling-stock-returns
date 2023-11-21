# modeling-stock-returns

- **This is a recent project that demonstrates my technical and quantitative research skills.**
- In the Jupyter Notebook `model_volatility.ipynb` I process market and finacial data, and model stock returns.

## Data description

- **Market data**: assume the market data is available each day after the markets close. 
    - `openadj.csv`/`closeadj.csv`: the open/close price adjusted for corporate actions
    - 'adv.csv`: average daily volume in dollars 
    - `market_cap.csv`: total market value in million dollars
    - **sectors.csv`: the sector classification of the instruments in this universe
    - if the market cap is null for a stock for one day, the stock is out of the universe
- **Other predictors**: these have different update frequencies. Assume these predictors are available on their given date when markets open. 
    - `technical_indicator.csv`: derived from market data
    - `holding_indicator.csv`: the total value in dollars held by a group of institutional investors
    - `financial_indicator.csv` 2 accounting statement items: net income and sales in million dollars 
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
- **Conclusion:** Summary of work and potential future ideas.
