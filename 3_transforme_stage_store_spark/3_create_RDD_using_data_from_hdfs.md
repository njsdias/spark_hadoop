# Create RDD using data from HDFS

RDD: Resilient Distributed Dataset (fault tolerance and high availability)

  - in-memory
  - distributed
  - Resilient

  Resilient:
  For example if a RDD is processed by a task inside of a executor.
  If this task fail the RDD will be processed by a task inside of another node by some other executor


- Reading Files from HDFS
- Reading files from local file system and create redundancy
- Quick overview of Transformations and applications
- DAG and lazy evaluation
- Previewing the data using Actions

    // creating a list
    val l = (1 to 1000).toList

RDD is in memory distributed collection(list). It is an extension of Scala list.
The difference among the two is RDD is in memory distributed and resilient.

Let's see the records that are in dataset:

    hadoop fs -tail /foldername/filename

    // creating a RDD dataset
    val orders = sc.textFile("/foldername/filename")

    // access values in RDD
    orders.take(10)

# Create a RDD from local system

    // read data and convert it to a list
    val productsRaw = scala.io.Source.fromFile("/foldername/filename").getLines.toList

    //create a RDD
    val productsRDD = sc.parallelize(productsRaw)

    //take the firs ten records
    productsRDD.take(10

    // convert list to a RDD  
    val l_rdd = sc.parallelize(l)

# Quick overview of Transformations and applications

    val orders = sc.textFile("/foldername/orders")
    val productsRaw = scala.io.Source.fromFile("/foldername/part-00000").getLines.toList
    val products = sc.parallelize(productsRaw)


**Note:** To see all transformations available for a RDD consult the spark
 documentation transformations.

Lazy Evaluation means:
  - Lazy: don't execute immediately before until certain an action is perform
  - Evaluation: executing the code

To see it in actions execute the last three lines of code in a new spark-shell.
Pick the tracking ural and copy&past into a web browser and you will see nothing was executed.
Why? Becasue we are working with RDD. They are expecting to receive some actions to execute.
Thus, RDD only have a DAG (Directed Acyclic Graph).

    // collect the first record of an RDD
    orders.first

After this execution you can check in your web browser in the *Jobs tab*
 that you have an one job. If you click in the link of Column Description it
shows to you the DAG visualization

    // take a sample
    orders.takeSample(true,100).foreach(println)

**convert**: avoid to use it because of problems related with Memory Errors

    // convert a distributed RDD into a simple data collection
    // if the data is very big you can have a error OutOfMemory
    orders.collect

# Read Data From Different formats

- Data Frames- APIs provided by sqlContext
- Supported file format
  - orc
  - json
  - parquet
  - avro
- Previewing the data
  - show

# Read different formats  

To read diffrent files format use *sqlContext.read.* and press Tab keyboard to see the formats available.

When we use *sqlContext* we are using Dataframes and not RDD.

For example:

    // create a dataframe
    val ordersDF = sqlContext.read.json("/foldername/filename")
    // show all content of a dataframe
    orderDF.show
    // show the conten of only a few collumns
    orderDF.select("colname_1", "colname_2").show

Other way to do the same is:

    val ordersDF = sqlContext.load(""/foldername/filename", "json")
    orderDF.show
