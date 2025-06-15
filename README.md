# kafka-stock-market-project

## Introduction

I built this project ot learn and get hands-on experience with Apache kafka and real-time data streaming. I wanted to explore how large-sclae data pipelines handle continuous streams of data. To do this, I built a small-scale Kafka-based system that simulates real-time stock market data ingestion.

## Architecture


## Technology Used

For this project, the following programming languages and tools where used:
- Kafka for real-time data ingestion
- Jupyter Notebook for creating the producer and consumer instances
- SQL for querying the dataset
- AWS S3 for data storage
- AWS Glue for Data Catalog creation
- AWS Athena for querying the Data Catalog using SQL


## Dataset Used

The dataset used for this project contains historical stock market data with more than 100,000 records. Each entry includes key trading information such as opening, highest, lowest, and closing prices. The dataset also includes the adjusted closing price to account for factors like dividends, stock splits, trading volumne, and the rest. 
- **Index** - stock market index that the data point belongs to
- **Opening** - the price of the stock at the start of the trading day (opening bell)
- **High** - the stock's highest price during the trading day
- **Low** - the stock's lowest price during the trading day
- **Closing** - the price of the stock at the end of the trading day (closing bell)
- **Adj Close** - the closing price adjusted for corporate actions to give a more accurate view of its value over time
- **Volume** - total number of shares traded during the trading day
- **CloseUSD** - the closing price converted to USD

## What is Kafka


## Why use Kafka


## Kafka Architecture Overview


## Kafka Producer and Consumer Scripts
- [**Producer**](/kafka-files/kafka_producer.ipynb)

## Data Flow Summary



## Got Feedback?
**I'd love to hear your thoughts and feedback to further improve my skills! 🙌🏽**

You can connect with me through my [**LinkedIn**](https://www.linkedin.com/in/judgemongcal/)

Huge thanks to [**Darshil Parmar**](https://github.com/darshilparmar) for the project inspiration and guide!
