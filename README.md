# Real-Time-Twitter-data-streaming-ETL-Pipeline

Real-Time Twitter Data Streaming ETL Pipeline

This project implements a real-time ETL (Extract, Transform, Load) pipeline for processing and analyzing Twitter data streams. Leveraging the Twitter API and Apache Kafka, it continuously ingests tweets, performs preprocessing and transformation tasks using Apache Spark Streaming, and loads the processed data into a data warehouse or analytics platform.

Key Features:

Twitter Data Ingestion: Utilizes the Twitter API to fetch real-time tweets based on user-defined keywords or hashtags, ensuring a constant stream of data for analysis.

Apache Kafka Integration: Integrates with Apache Kafka to facilitate scalable, fault-tolerant data ingestion and processing. Kafka topics are used to partition and distribute tweet data across multiple processing nodes.

Apache Spark Streaming: Processes streaming data using Apache Spark Streaming, enabling real-time analysis, filtering, and transformation of tweets. Implements windowed operations and stateful processing for advanced analytics.

ETL Pipeline: Implements a robust ETL pipeline to extract relevant information from raw tweet data, transform it into structured formats, and load it into a data warehouse or analytics database for further analysis and visualization.

Data Quality and Governance: Implements data quality checks and validation rules to ensure the integrity and reliability of the processed data. Incorporates mechanisms for error handling, deduplication, and data cleansing to maintain data consistency.

Technologies Used:

Tweepy
Apache Kafka version 2.5.0
Apache Spark version 3.0.0
kafka-python version 2.0.1
pySpark 3.0.0
Delta Lake package

Usage:

Configure Twitter API credentials and Kafka settings in the configuration file.
Start the Kafka cluster and create necessary topics for tweet ingestion.
Run the Spark Streaming application to consume tweets from Kafka, perform ETL operations, and store processed data.

After providing Twitter API credentials in twitter_credentials.py and creating kafka topics and updating the topic in tweet_stream_producer.py and tweet_stream_consumer.py, run the twitter stream using

python tweet_stream_producer.py

and consumer as

spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.0,io.delta:delta-core_2.12:0.7.0 tweet_stream_consumer.py

Contributing:

Contributions to this project are welcome! Whether it's bug fixes, feature enhancements, or documentation improvements, feel free to submit pull requests or open issues.

Architecture

Extraction

Twitter API

Tweets are streamed using Tweepy, a python based library for accessing the Twitter API.
http://docs.tweepy.org/en/latest/index.html

Apache Kafka
Apache Kafka is an open-source stream-processing software platform developed by the Apache Software Foundation.

Creating Kafka topic and configuring the producer
/bin/kafka-topics.sh --create \
    --zookeeper <hostname>:<port> \
    --topic <topic-name> \
    --partitions <number-of-partitions> \
    --replication-factor <number-of-replicating-servers>
Kafka organises records as topics. The tweet stream we get from the API is published to a topic by a producer. Kafka producer is initialized using kafka-python, which is the python client for Kafka.

Transformation
The ingested data is read with the help of Apache Spark structured streaming. Apache Spark structured streaming handles streaming data and provides data in the form of dataframes, as opposed to Spark Streaming which provides data as RDDs (Resilient Distributed Datasets).

Pre-processing data and making predictions
The Tweet data received is then pre-processed and passed into a machine learning pipeline we are about to create.

Building an ML Pipeline for Sentiment Analysis
Stanford's Sentiment140 dataset is used to train a Logistic Regression model to predict sentiment of a text. Spark ML library is used to perform TF-IDF (Term Frequence-Inverse Document Frequency), followed by classification using Logistic Regression on the dataset.

Building the pipeline
A machine learning pipeline is created using Spark ML library.

ML Pipeline

The data is initially tokenized and filtered of urls and punctuations using regular expressions before being passed into the pipeline, where the stop words are removed. It is then vectorized and term frequency is calculated with the help of CountVectorizer. The next step is to calculate the Inverse Document Frequency.
The TF-IDF product is then passed on to a classifier, Logistic Regression in this case, to make predictions. This particular implementation had an accuracy of 0.8323919410115472, as calculated by a binary classification evaluator.
The pipeline as well as trained model is saved for our use with the streaming data.

Loading
The transformed data can be written using the DataStreamWriter API into many built in output sinks. For debugging purposes, the console output sink can be used to display the transformed dataframe. For the purpose of this project, the data is being written into a Databricks Delta Lake storage layer.
The data can further be analyzed or visualized using tools like Tableau or PowerBI to gather insights.

License:

This project is licensed under the MIT License
