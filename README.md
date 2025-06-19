# kafka-stock-market-project

## Introduction

I built this project to learn and get hands-on experience with Apache kafka and real-time data streaming. I wanted to explore how large-sclae data pipelines handle continuous streams of data. To do this, I built a small-scale Kafka-based system that simulates real-time stock market data ingestion.

## Architecture
![stock_market_DE_diagram drawio](https://github.com/user-attachments/assets/c6f04a3a-e661-43a6-b8d1-d12936762d4f)


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

## Step-by-step Implementation
* **Step 1**: I created an EC2 Instance in the AWS Console. For this project, I opted for the t2.micro instance type.
  
* **Step 2**: Once the instance is created, I connected to my instance via my local terminal. See [Stephane Maarek's tutorial here.](https://www.youtube.com/watch?v=8UqtMcX_kg0)
  
* **Step 3**: Once connected, I installed Kafka 3.3.1 into my instance using this syntax:
  ```
  wget https://downloads.apache.org/kafka/3.3.1/kafka_2.12-3.3.1.tgz
  ```
  Then, I extracted the contents of the .tgz file:
  ```
  tar -xvf kafka_2.12-3.3.1.tgz
  ```
  
* **Step 4**: Since Kafka is built on the Java Virtual Machine (JVM), I had to install Java as well through the yum package manager:
  ```
  sudo yum install java-1.8.0-openjdk
  ```
  
* **Step 5**: After this, I opened a new terminal window and ran cd to go into the kafka_2.12-3.3.1 folder (run ls to see content of current dirr).
  After that, I started the ZooKeeper:
  ```
  cd kafka_2.12-3.3.1
  bin/zookeeper-server-start.sh config/zookeeper.properties
  ```
  
* **Step 6**: Once the ZooKeeper is running, I opened another window, ran cd into the kafka_12-3.3.1 folder, then set the maximum JVM heap size that
  kafka can use. This limits the memory that Kafka can use since it can be memory intensive. Also, since I used the free t2.micro EC2 instance,
  setting the heap size avoided Out of Memory error (OOM):
  ```
  export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M" 
  ```
  Xmx is the max heap size while Xms is how much the JVM allocates at startup.
  
* **Step 7**: Before starting a kafka server, I changed the ADVERTISED_LISTENERS IP to the public IP of the EC2 instance by running the following
  line:
  ```
  sudo nano config/server.properties
  ```
  After that is set, I then started the kafka server:
  ```
  bin/kafka-server-start.sh config/server.properties
  ```
  
* **Step 8**: Once the server is running, I opened a new terminal window and repeated the steps to get into the kafka folder. Then, I ran the
  following command to create a topic:
  ```
  bin/kafka-topics.sh --create --topic {name of topic} --bootstrap-server {Put the Public IP of your EC2 Instance:9092} --replication-factor 1 --partitions 1
  ```
  A sample would be:
   ```
  bin/kafka-topics.sh --create --topic stock_market --bootstrap-server 18.300.12.43:9092 --replication-factor 1 --partitions 1
  ```
  This creates a kafka topic named stock_market and can be accessed through the Kafka broker at 18.300.12.43:9092 (or your instance's Public IP). --replication-factor 1 means there will be no redundancies for the topic, and --partitions 1 means the topic will store data in one partition only.
  
* **Step 9**: After the topic is created, I created a folder in my desktop with the dataset in csv format. I opened a terminal window and ran cd to get
  into the same folder. Once I'm in the folder, I create a virtual environment using python (Python must be installed in your device for this to work):
  ```
  python3 -m venv venv
  ```
  I activated the virtual environment and installed JupyerLab via pip:
  ```
  source /venv/bin/activate
  pip install jupyterlab
  ```
  Once installed, you can now use jupyter lab to use jupyter notebooks by running:
  ```
  jupyter lab
  ```

* **Step 10**: Using jupyter lab, create two jupyter notebooks (one for the producer and one for the consumer). 
  * For the **producer**, you can view the notebook [here](kafka-files/kafka_producer.ipynb). Basically, I installed
    kafka-python and pandas before importing other necessary packages. In the third line:
    ```
    producer = KafkaProducer(bootstrap_servers=[':9092'], # add your EC2 public IPv4 address before :9092
                         value_serializer=lambda x:
                         dumps(x).encode('utf-8'))
    ```
    I instantiated a KafkaProducer and set the bootstrap_server to the same bootstrap_server assigned to the topic. Kafka expects messages in bytes, so the
    value_serializer is set that way so that the Python objects are converted into JSON string then to UTF-8 bytes before being sent to the Kafka server.

    I also read the dataset csv file into a pandas DataFrame and ran a while loop:
    ```
    while True:
      dict_stock = df.sample(1).to_dict(orient='records')[0] # this formats the data into JSON format
      producer.send('stock_market', value=dict_stock)
      # sleep(1)
    ```
    This while block randomly selected one row from the dataframe and convert it into JSON format. Once formatted, the producer is called to send the row of
    data into the topic "stock_market". You can also add sleep() to add buffer between send processes.

  * For the **consumer**, you can view the notebook [here](kafka-files/kafka-consumer.ipynb). Similar to the producer, the first few lines are for installation
    and importing packages. I used s3fs to send the received data into an S3 path.
    ```
    consumer = KafkaConsumer('stock_market',
                        bootstrap_servers=['18.139.115.86:9092'],
                        value_deserializer=lambda x: loads(x.decode('utf-8')))
    ```
    This block of code instantiates consumer as a KafkaConsumer for the topic 'stock_market', under the specifice bootstrap_server. Opposite to the serialization
    of the rows of data in the producer, the value_deserializer converts the incoming byte stream into UTF-8 string, then the "loads()" function parse it into a
    Python object.
 
    I then instantiated variable s3:
    ```
    s3 = S3FileSystem()
    ```
    s3 will automatically use the AWS CLI Credentials configured in your device. See tutorial [here.](https://www.youtube.com/watch?v=vZXpmgAs91s).

    After that, I created a for loop that reads messages from the Kafka topic using the consumer:
    ```
    for count, i in enumerate(consumer):
     print(i)
     with s3.open("s3://kafka-stock-market-project-raw/stock_market_{}.json".format(count), 'w') as file:
         json.dump(i.value, file)
    ```
    Every message is saved as a JSON file to the specified s3 path.

* **Step 11**: In the  AWS Console, I went to AWS Glue and ran a crawler for the same s3 folder where the data from the consumer is stored. Since the schema of the
  data does not change overtime, I only needed to run the crawler once. As the data keeps on getting placed into the S3 path provided, a quick SQL query of
  ```
  SELECT COUNT(*)
  FROM your-database.your-table-name;
  ```
  in AWS Athena confirms that the data is being ingested and processed to AWS in real-time as the count changes as the number of json files in the s3 folder
  also changes.

**Note**: This implementation may not fully align with best practices for using Kafka and AWS, as it was primarily designed as a learning exercise. That said,               I'd love to learn more about production-grade patterns and welcome any feedback on how this could be improved to better reflect real-world, large-scale
          systems.


## Got Feedback?
**I'd love to hear your thoughts and feedback to further improve my skills! 🙌🏽**

You can connect with me through my [**LinkedIn**](https://www.linkedin.com/in/judgemongcal/)

Huge thanks to [**Darshil Parmar**](https://github.com/darshilparmar) for the project inspiration and guide!
