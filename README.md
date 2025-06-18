# kafka-stock-market-project

## Introduction

I built this project ot learn and get hands-on experience with Apache kafka and real-time data streaming. I wanted to explore how large-sclae data pipelines handle continuous streams of data. To do this, I built a small-scale Kafka-based system that simulates real-time stock market data ingestion.

## Architecture


## Technology Used

For this project, the following programming languages and tools were used:
- Kafka for real-time data ingestion
- AWS EC2 for deploying and hosting the Kafka brokers in the cloud
- AWS S3 for data storage
- AWS Glue for Data Catalog creation
- AWS Athena for querying the Data Catalog using SQL
- SQL for querying the dataset
- Jupyter Notebook for creating the producer and consumer instances



## Dataset Used

The [dataset used](data/stock_market_dataset.csv) for this project contains historical stock market data with more than 100,000 records. Each entry includes key trading information such as opening, highest, lowest, and closing prices. The dataset also includes the adjusted closing price to account for factors like dividends, stock splits, trading volumne, and the rest. 
- **Index** - stock market index that the data point belongs to
- **Opening** - the price of the stock at the start of the trading day (opening bell)
- **High** - the stock's highest price during the trading day
- **Low** - the stock's lowest price during the trading day
- **Closing** - the price of the stock at the end of the trading day (closing bell)
- **Adj Close** - the closing price adjusted for corporate actions to give a more accurate view of its value over time
- **Volume** - total number of shares traded during the trading day
- **CloseUSD** - the closing price converted to USD

## What is Kafka

Apache Kafka is an open-source distributed event streaming platform designed for high-throughput, fault-tolerant, and real-time data pipelines. It works like a scalable messaging system that allows you to handle large volumes of data where a producer sends data to the topics, and consumers read data from those topics. In this project, Kafka is used as the main hub for ingesting and transmitting stock market data between a producer and a consumer, simulating a real-time trading information flow.

## Why use Kafka

On scenarios where data needs to be ingested and processed immediately rathern than after a scheduled interval, the traditional batch processing will not be enough. Kafka is ideal for real-time data streaming scenarios where timely and reliable delivery of data is crucial. 

- **Low Latency** - by writing messages to the disk immediately in a sequential manner, Kafka is able to minimize delays and also avoid random I/O operations. The consumers can read the message almost instantly after they're published, allowing for near real-time processing.
- **High Throughput** - With its distributed architecture, Kafka can handle large volumes of messages per second. Topics are divided into partitions across brokers, allowin parallel processing and horizontal scaling.
- **Durability** - By configuring the retention policies, Kafka pesists all messages to the disk to ensure that data is not lost even when the consumers become temporarily offline. Data replication can also be configured across brokers to prevent data loss.
- **Fault Tolerance** - You can replicate topic partitions across multiple brokers to provide access to the same partition. If a broker fails, a replica can take over with minimal disruptions. This configuration can be done through the ZooKeeper.

Although these all sound great, it's worth noting that streaming data often costs more since it has more infrastructure needs, and may need to have continuous uptime. It is ideal for when real-time data or responsiveness is required but Batch is ideal for infrequent, large-volumn processing.

## Kafka Architecture Overview
![Kafka-Architecture-01-01](https://github.com/user-attachments/assets/f9d53eb8-fb21-483b-8495-03a24b921bc6)
Photo source: [**GeeksforGeeks**](https://www.geeksforgeeks.org/)

The core components are:
- **Cluster** - Clusters are composed of multiple brokers working together to manage, distribute, and replicate data streams.
- **Brokers** - Servers in a cluster that receive, store, and serve the data. Aside from managing the replciation of data, Brokers are also resposible for the read and write operations from the producers and consumers.
- **Topics** - The data being transmitted through Kafka are organized into Topics, these are channels to which producers can send data and where consumers can read data.
  - **Partitions** - Topics are divided into the basic unit of parallelism in Kafka, called partitions. These makes scaling horizontally by distributing data across brokers possible for load balancing and high throughput.
- **Producers** - Client-side applications or services that publish (write) data to Kafka topics. How the records are distributed among partitions can also be configured through the producers.
- **Consumers** - Client-side applications or services that subscribe to (read) data from Kafka topics. Consumers can be part of a consumer group, which enables parallel data processing, scaling, and resiliency. It ensures that each message is processed once per group, while distributing load across consumers automatically.
- **ZooKeeper** - a centralized service that maintains the configuration, providing distributed synchronization, and group services. It manages and coordinates the borkers. (Replaced by KRaft mode in newer versions of Kafka)
- **Offsets** - unique identifiers for each message within a partition that allows Kafka to track the offset of the last message read by the consumer, allowing the consumer to resume the process from the last message processed even after restarts or failures.

## Kafka Producer and Consumer Scripts
- [**Producer**](/kafka-files/kafka_producer.ipynb)
- [**Consumer**](/kafka-files/kafka_consumer.ipynb)

## Data Flow Summary



## Got Feedback?
**I'd love to hear your thoughts and feedback to further improve my skills! 🙌🏽**

You can connect with me through my [**LinkedIn**](https://www.linkedin.com/in/judgemongcal/)

Huge thanks to [**Darshil Parmar**](https://github.com/darshilparmar) for the project inspiration and guide!
