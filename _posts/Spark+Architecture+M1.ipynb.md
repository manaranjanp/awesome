---
layout: post
title: "Understanding Spark - part 1: Overview"
date: 2016-05-25
description: "Understanding spark architecture and its components"
main-class: 'spark'
postbook: 'inotebook'
published: true
color: '#7AAB13'
tags:
- spark
- architecture
- driver
- executor
- RDD
categories:
- spark
introduction: "Understanding spark architecture and its components"
---


# Understanding Spark: Part 1: Overview

### 1. Why spark and what are the key reasons spark was born?

Little bit of history of when and how spark was born will answer this question.

Spark came out of UC Berkley. Matei Zaharia and few other graduate students were working on big data problems and soon realized the limitations of existing platforms like Hadoop. Until then Hadoop has been at the forefront of big data solutions and was being adopted by many major enterprises like yahoo, facebook, twitter etc. Hadoop's distributed architecture made possible to store terabytes and petabytes of data and run map reduce algorithms to process large volume of data. The storgae component of Hadoop is called HDFS (Hadoop Distributed File System) and processing component is Map Reduce.

The way map reduce is desinged on hadoop is the maps read data from HDFS, send to reducers and reducers write the result back to HDFS. This works well for algorithm that invovle single stage and very few stages. But algorithms which have hundred or thousands of iterations, this does not work well, as every map reduce stage writes the intermediate data to HDFS. Reading and writing to a file system, which ultimately write to disk, make processing extremely slow. Sometimes, advaned algorithms like machine learning algorithms takes several hours, even days to complete.

The other key issues was Map Reduce on Hadoop is not designed for interactive processing. It is primarily designed for batch processing. But interactie processing is very popular in data analysis world, especially during the data exploration stage, where data analysts or data scientist would like to load large amount of data and then keep sending algorithms or instructions to execute and observe resutls.

So, the team in UC Berkley designed a new framework which would support interactie and large stage iterative processing easier. Spark would load data from sources like HDFS and keep it on RAM and Map Reduce stages will pass intermediate data through RAM to make processing faster. The following diagram depicts the difference clearly.

<img src="./assets/img/python/mrstages.png" width="600">

The other key driver for developing spark was to provide an abstract level programming interface to make analysis easier. There are lot of heavy lifting that need to be done while coding in map reduce on Hadoop framework i.e. lot of verbose in coding. Spark made it easier by providing abstract level interfaces with out thinking much of map reduce programming paradigm. Spark also built a strong Machine Learning library from the beginning. We will discuss in detail about language and API support in Spark later in this chapter.

### 2. How data is represented in spark?

Data is represented in terms of RDDs in spark.

* **RDD - Resilient Distributed Datasets** - RDDs are distributed datasets represented in memory. Each RDD is split into multiple partitions and loaded into memory. These partitions can be on multiple nodes in an underlying cluster or on single node depending on how spark is configured to run.

    - How data is loaded from sources and how many partitions are created, depends on the data source and how data is read and parsed. For example, if data is loaded from HDFS, by default each block converts into a partition.
    - Number of partitions can be controlled in spark.
    - RDDs are immutable i.e. RDDs are not modified. If an RDD is operated on (like filtering, grouping etc.), a new RDD is created to represent the resulting dataset.
    - In a typical analysis, a new RDD is created when source data is loaded and then the dataset goes through multiple transformations to arrive at any insight. The results will be another RDD. But dataset goes through many transformations in between, so the intermediate results will also be RDDs.

    <img src="./assets/img/python/rdds.png" width="600">

    - Internally, spark maintains a lineage of how each RDD is created from other RDDs by keeping track of all depedencies and transformations.
    - Once an RDD is created, it is stored in RAM. But spark can also discard RDDs if there are no or little resources available for computation.
    - When an RDD is required and not found in RAM, it is re-created using the lineage spark maintains.
    - To avoid re-creation of certain RDDs, which will be used frequently, an RDD can be configured to be stored all the time and not discarded. Storage level of RDD can also be configured.
    - RDD storage level can be memory or disk. Replication for each RDD can also be specified. This will ensure RDDs have better fault tolerance, even if nodes fail in a cluster.

### 3. Is Spark an alternative to Hadoop?

No, spark is not an alternative to Hadoop. In fact Hadoop and Spark are complementary. Most of the deployments will have hadoop and spark both.

Hadoop has 3 components
- **HDFS** for storing large amount of data
- **YARN** for managing computational resources across multiple nodes in the cluster for distibuted computing and scheduling algorithms in the cluster in most optimal way.
- **Map Reduce** for executing algorithms in map reduce fashion.

The only limitations in Hadoop is it's map reduce layer, which is not designed well for multi-stage and iterative algorithms.

Spark has only **Map Reduce** layer and well designed to process multiple map reduce stages faster. Spark does not have **storage** and **Cluster Management** components of its own. It depends on other frameworks for provide these layers.

From very beginning, Spark is designed to integrate well into HDFS and YARN layer. So, most enterpises have adopted both platforms and integrated them to solve thier problems.

HDFS can store large amount of data and map reduce in hadoop can be used to prepare raw data and trasformation into required structure (mostly ETL kinds of tasks). Once data is prepared, spark can be used to run advanced algorithms like machine learning to derive deep insights from data. Because data can reside on HDFS and be accessed by both Map Reduce in hadoop and Spark, this co-existence approach is very popular and widely used by enterprises. The diagram below depicts this.

### 4. What functionalities & APIs spark supports?

Spark suppports operating on RDDs using APIs. These APIs support transformations and actions on RDDs. We will discuss these in detail in the next chapter. Some examples of transformations would be parsing, grouping, filtering, sorting, joining or low level map reduce algorithms.

Spark also support support higher level APIs like dataframes. Dataframes are very popular in R and Python data analysis and machine learning frameworks. Most data scientists are familiar working with dataframes as these concepts are there for many years in R and Python. Spark now enables these data scientists to work with very large data sets using dataframe like APIs. It also supports SQL syntax for querying large datasets.

<img src="./assets/img/python/sparkapis.png" width="600">

Spark also provides libraries for most of the popular machine learning algorithms like regression, classification, clustering and neural networks. It also provides APIs for data pipelines. We will explore these in more detail in later chapters. It has also a library for graph algorithms for large scale network analysis.

Spark has inbuilt support for streaming data processing for deriving mode real time insights.

Unlike Hadoop or any other framework, spark's baseline release has support for all these APIs and libraries. So, there is no need to any additional installation.

### 5. What languages spark supports?

Spark supports API in following languages

* Java
* Python
* Scala
* R

The API and library support in R is very minimal at the time of writing this book. But thers is a lot of interest and committment to support for all APIs acorss all languages eventually.

### 6. What data sources spark work with?

Spark is not tightly coupled with any data sources. It can work with local file systems like ext3, XFS or NTFS or can also work with distributed file systems like HDFS.

Spark integrates well with databases like Mysql and Nosql systems like Mongo, HBase and Cassandra.

Spark also work well with cloud storage like S3.

As Spark is not tightly coupled with any storage, custom adaptors can be developed to integrate into new data sources. A list of custom developed data sources adaptors are available at http://spark-packages.org/ under *Data Sources* tab.

### 7. What are different deployment modes of spark and what all cluster management frameworks spark work with?

Spark can run in two different modes

* Local Mode
* Cluster Mode

In *Local Mode*, spark runs all its components driver and executors on the same local machine. This is primarily used for analyzing compartively smaller datasets and development purposes.

In *Cluster Mode*, Spark runs driver and executors in differnet nodes. It can start as many executors as it wants provided enough nodes and resources are available in the cluster.

In cluster mode, Spark can run **standalone** or can integrate into a cluster management framework like **YARN** or **Mesos**.

### 8. What are key resource links for spark?

Spark can be downloaded from

https://spark.apache.org/downloads.html

Spark documentation is available at

https://spark.apache.org/

3rd Party Spark packages (extensions) are available at

http://spark-packages.org/

### 9. How to install and configure spark?

This section assumes that you will be using python interface for spark.

- **Install Java run time environemnt**

    Download and install JRE (Java Runtime Environment). Minimum java version required is 1.7

    Download and install from http://www.oracle.com/technetwork/java/javase/downloads/index.html

    After installation you can verify jdk installation and version using the following command

    *java -version*


- **Install Python and related libraries**

    The best way to install python and related library, that we will be using in the upcoming chapters is to download and install anaconda distribution for python available here https://www.continuum.io/downloads

    Download and install the python 3.5 version for the operating systems you are using.

    After installation you can verify python installation and version using the following command

    *python -V*   (Note: it is capital V)


- **Download and install Spark**

    Download Spark installables from https://spark.apache.org/downloads.html. Suggested download is *Pre-built for Hadoop 2.6 and later*

    It will download a file called *spark-1.6.0-bin-hadoop2.6.tgz*. Now untar the file onto a folder using the following command

    *tar -xvf spark-1.6.0-bin-hadoop2.6.tgz*

    Take note of the directory location, where the above file is untared. This directory location need to be configured as spark home. For examples: *C:\MyTools\spark-1.6.0-bin-hadoop2.6*


- **Configure Spark**

    Configure following environment variables on you system

    *SPARK_HOME=C:\MyTools\spark-1.6.0-bin-hadoop2.6*

    And add *C:\MyTools\spark-1.6.0-bin-hadoop2.6\bin* to your *PATH* system variable.

    - For windows

    *set PATH=C:\MyTools\spark-1.6.0-bin-hadoop2.6\bin;%PATH%*

    - For Mac or Linux

    *export PATH=\$PATH:\$SPARK_HOME/bin*


- **Verfify Spark Installation**

    Enter the following command at the command prompt

    *pyspark*

    It should start the spark shell and show the spark version installed.


<img src="./assets/img/python/pyspark.png" width="500">

### 10. How spark works?

*Now let's look at how computation works and how data is operated upon.*

* **Driver** - Driver is the entry point for a spark program. It acts like a coordinator, which starts and controls a set of distributed processes across multiple nodes called executors. Driver along with executors are responsible for running spark applications. Each application will have its own driver and set of executors. The numeber of executors and resouces available to driver and executors in terms of RAM and number of CPU cores can be configured.

* **Worker** - The executors run on multiple nodes in a cluster and these nodes are called worker nodes. The workers nodes are primarily managed by a cluster management framework like YARN or mesos. Even spark comes with a standalone cluster management component. But in real world most deployments will have either YARN and mesos framework running.

* **Executors** - Executors are responsible for storing partitions of different RDDs and applying transformation to them.

* **Cluster Manager** - Cluster manager is not part of Spark. Cluster manager manages the worker nodes and their resources allocations. For example Resource Manager in YARN component of Hadoop. Spark Driver interacts with cluster manager and obtains resources in worker nodes to start executor processes.

The following figure depicts the architecture and its components.

<img src="./assets/img/python/sparkworks.png" width="600">

Typicall workflow of a spark application is described below

  - When a spark application starts, it starts a driver program. The driver programs creates a SparkContext object, which holds refernce to default configuration parameters and other environement parameters.

  - The Driver then requests the cluster manager to allocate resources in the cluster.

  - Cluster managers starts these process in available worker nodes and then assigns to spark driver to use.

  - Driver initializes these processes as executors.

  - Driver then pushes code to these executors to load data and apply trasformations. So, data is loaded in terms of RDD partitions into multiple executors and transformations are applied to these RDD partitions. The code executes in threads which are called tasks. The executors are designed as multi-threaded processes and hence can execute multiple tasks concurrently.
