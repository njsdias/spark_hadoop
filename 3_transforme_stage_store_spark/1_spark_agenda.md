# Agenda

- Objectives
- Problem statement- Introduction to Spark
- Initializing the job
- Create RDD using data from HDFS
- Read data from different file formats
- Standard transformations
- Saving RDD back to HDFS
- Save data in different file formats
- Solution

# Objectives

- Convert a set of data values in a given format stored in HDFS into new data values or a new data format and write then into HDFS
  - Load RDD data from HDFS for use in Spark applications
  - Write the results from an RDD back into HDFS using Spark
  - Read and write files in a variety of file formats
  - Perform standard ETL process on data

# Problem statement- Introduction to Spark

- Use *retail_db* dataset
- Problem statement
  - get daily revenue by product considering completed and closed orders
  - data need to be sorted by ascending order by date and then descending order by
revenue computed for each product for each day

- Data for *orders* and *orders_items* is available in HDFS
  */public/retail_db/orders* and */public/retail_db/order_items*

- Data for products is available locally under */data/retail_db/products*

- Final output need to be stored under
  - HDFS location -avro format
  - HDFS location - text format
  - Local location

# Spark Intro

- Spark is Distributed computing framework
- Bunch of APIs to process dataset- Higher level modules such as Data Frames/SQL, Streaming, MLLib and more
- Well Integrated with Python, Scala, Java, ext
- Spark uses HDFS API to deal with file systems
- It can run against any distributed or cloud file systems - HDFS, S3, AZure Blob, etc

**Spark Documentation**

Spark Programming Guide 1.6.3 (search for the most recent version)
