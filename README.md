# market-volatility-analysis
Fintech-focused market time-series and volatility analysis using Python language
Market data sourced from Yahoo Finance due to accessibility; analysis methdology is platform agnostic and transferable to institutional data feeds.
Initial attempts using Yahoo Finance were affected by rate-limiting, so the pipeline was adapted to use Stooq via pandas-datareader for improved reliability.