# Capital Assets Pricing Model(CAPM)

> This project leverages CAPM which is a model used to describe the relationship between the expected return and risk of securities.  

## The  CAPM Formula

***<p style="text-align: center;">   r<sub>i</sub> = r<sub>f</sub> - &beta;(r<sub>m</sub>-r<sub>f</sub>)</p>***

<p style="text-align: left;">  
r<sub>i</sub> = Expected Return of a Security

r<sub>f</sub> = Risk-Free Rate of Return

r<sub>m</sub> = Market Portfolio

&beta; = slope of the regression line(market return vs stock return)

<p>

Above is the formula for CAPM which is used in the model to determine the expected return on an investment, particularly in the context of stocks and portfolios.The variable in the formula represents the key components.

## Normalization and Visualization
Firstly the stock prices and volume data must be normalised for it to be used in the model. The function below is created to take a Pandas dataframe as an arguement to then, normalise by dividing each row by the first row. This will result in all stocks from starting from a value of one. This will showcase how much the price and volume of each stock has grown relative to the others.
```python
# Function to normalize the prices based on the initial price
def normalize(df):
  x = df.copy()
  for i in x.columns[1:]:
    x[i] = x[i]/x[i][0]
  return x
```
For the visualization for each dataset, the following function will take a dataframe and the title for the plot as arguements to create an interactive graph using the Plotly library.
```python
def interactive_plot(df, title):
  fig = px.line(title = title)
  for i in df.columns[1:]:
    fig.add_scatter(x = df['Date'], y = df[i], name = i)
  fig.show()
```

## Calculate for the Stocks daily return

Below is the function `daily_return` that is used to produce the dataframe that contains the daily returns for each stock. It runs a for loop which calculates the percentage daily difference for the value of each stock.

```python
def daily_return(df):
  df_daily_return = df.copy()
  for i in df.columns[1:]:
    for j in range(1, len(df)):

      df_daily_return[i][j] = ((df[i][j] - df[i][j-1])/df[i][j-1])*100

    df_daily_return
    df_daily_return[i][0] = 0
  return df_daily_return
  ```


## <p style="text-align: left;"> Calculating for &beta;</p>

The value for <l style="text-align: left;"> &beta;<l> is determined by the slope of the first order polynomial between the selected stock and the S&P500 which is the entire market. So to visualise this polynomial, a scatter plot is created with the following line for the Apple stock.

```python
# plot a scatter plot between the selected stock and the S&P500 (Market)
stocks_daily_return.plot(kind = 'scatter', x = 'sp500', y = 'AAPL')
```

Once plotted, the numpy function `np.polyfit` is used to determine the slope <l style="text-align: left;"> &beta; </l> as well as the intercept <l style="text-align: left;"> &alpha; </l> which is a measure of an investment's or portfolio's performance relative to the market. The following line shows the values for both <l style="text-align: left;"> &beta; </l> and <l style="text-align: left;"> &alpha; </l>  for the Apple stock.
```python
beta, alpha = np.polyfit(stocks_daily_return['sp500'], stocks_daily_return['AAPL'], 1)
```
Afterwards the values for <l style="text-align: left;"> &beta; </l> and <l style="text-align: left;"> &alpha; </l> are calulated for all the stocks using the following code, looping through the `stocks_daily_return` dataframe to append both values for each stock into empty dictionaries and visualising those for each stock. 

```python
beta = {}
alpha = {}

for i in stocks_daily_return.columns:
  if i != 'Date' and i !='sp500':

    stocks_daily_return.plot(kind = 'scatter', x = 'sp500', y= i)
    b, a=np.polyfit(stocks_daily_return['sp500'], stocks_daily_return[i], 1)
    plt.plot(stocks_daily_return['sp500'], b * stocks_daily_return['sp500'] + a, '-', color = 'r')

    beta[i] = b

    alpha[i] = a
    plt.show

```

## Applying the CAPM formula

First, r<sub>m</sub> is calulated by multiplying the average daily stock return for the S&P500 by the number of working day in the stock market in a year which is 252 days.

```python
rm = stocks_daily_return['sp500'].mean() * 252
```

Then using the following code, the risk free rate is assumed to be zero and the expected return for Apple is caluated using the CAPM formula using the <l style="text-align: left;"> &beta; </l> value for Apple's stock.

```python
rf = 0

# Calculate return for any security (APPL) using CAPM  

ER_AAPL = rf + (beta*(rm -rf))
ER_AAPL
```

Finally, the CAPM formula can be applied to all stocks by using looping thorough the keys of the <l style="text-align: left;"> &beta; </l> dictionary used in the previous section as seen in the code below.

```python
ER = {}

rf = 0
rm = stocks_daily_return['sp500'].mean() * 252

for i in keys:
  ER[i] = (rf + beta[i] * (rm -rf))

for i in keys:
  print('Expected return based on CAPM for {} is {} %'.format(i, ER[i]))
```

