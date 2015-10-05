---
layout: post
title: Visualizing stock market charts with rCharts in R
subtitle:
tags: [R, visualization, rCharts]
---

I came across [rCharts](http://rcharts.io/) package developed by [Ramnath Vaidyanathan](https://ramnathv.github.io/) early this year and quickly become a big fan of it. rCharts is a R interface to multiple javascript charting libraries, allowing one to quickly generate a interactive visualization in R environments. Currently the official rCharts supports may popular javascript charting libraries, however Highstock is not one of them.

Ramnathv pointed out in the [thread](https://github.com/ramnathv/rCharts/issues/320), that the work to integrate highstock has already been [done](https://github.com/nemonominem/Highstock-rCharts-hack). I download this implementation and did a few improvements.  

The following is an example of using HighStock-rCharts to visualize a stock and add couple TAs.
You can check out the code from my [github](https://github.com/dingh/HighStock-rCharts)

<p>
<iframe frameborder="0" width="800" height="560"
        sandbox="allow-same-origin allow-scripts"
        scrolling="no" seamless="seamless"
        src="/files/R/HighStock.test.html">
</iframe>
</p>



{% highlight r %}
require(rCharts)
require(quantmod)
require(plyr)

# set working directory
setwd('set the working director')

## source the highstock class and plotting functions
# Function sourceDir is defined in the Examples section of ?source:
sourceDir <- function(path, trace = TRUE, ...) {
  for (nm in list.files(path, pattern = "[.][RrSsQq]$")) {
    if(trace) cat(nm,":")
    source(file.path(path, nm), ...)
    if(trace) cat("\n")
  }
}
sourceDir(path = 'highstock/R/')

## Download data
# Specify period of time we are interested in
startDate <- as.Date("2010-01-01")
endDate   <- as.Date("2013-12-31")

# Define the tickers we are interested in
tickers <- c('AAPL')
getSymbols(tickers, src = "google", from = startDate, to = endDate)
stocks <- do.call(rbind, lapply(tickers, getQData(OHLCV)))
aapl <- subset(stocks, stock=='AAPL')

# 1. Standard candle plot
p <- candlePlot(data = aapl, name='AAPL');p

# 2. add Volume to plot
hs.addVOL(p, data = aapl, height = 60);p

# 3. add moving average
hs.addEMA(p, data = aapl, n = 10);p

# 4. add BBands
hs.addBBands(p, data = aapl, n = 20, sd = 2);p

# 5. add MACD
hs.addMACD(p, data = aapl);p
{% endhighlight %}
