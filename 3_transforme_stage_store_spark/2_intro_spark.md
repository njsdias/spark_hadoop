# Spark Intro

## Initialize the Job

- Initialize using spark-shell
  - running in yarn model (client or cluster mode)
  - control arguments
  - deciding on number of executors
  - setting up additional properties
  - reading proprieties at run time

- Programmatic initialization of Job
  - create configuration object
  - create spark context object

### Spark Shell

Spark shell is:

    spark-shell = scala + spark dependencies + implicit variables sc and sqlContext

Connect into Hadoop cluster which has a Spark installation or run Spark
locally.

    // to lunch the spark shell in hadoop cluster
    spark-shell --master yarn \
      --conf spark.ui.port=12654

Inside of spark-shell type *sc* to see if the SparkContext is available.

Once we typed *spark --shell* we see two important parameters:

- --executor-cores NUM
- --executor-memory MEM
- --num-executors NUM

We can initiate a Spark Job setting this parameters if we want:

    spark-shell --master yarn \
      --conf spark.ui.port=12654
      --num-executors 1 \
      --executor-memory 512M

The default values are stores in configuration files:

    - /etc/spar/conf
    - vi spark-defaults.conf
    - vi spark-env.sh

When you run the spark-shell command it generates several information lines.
One of those lines is the tracking URL. You can copy &past this url to
a web browser and check the number of the executors and in environment you
can see the executor.memory and executor.instances values.

### To initialize programmatically the job

    // to create a configuration object
    import org.apache.spark.{SparkConf, Spark.Context}
    // initialize spark context programmatically
    val conf = new SparkConf().setAppName("Daily Revenue").setMaster("yarn-client)"
    val sc = new SparkContext(conf)

    // to get the environment details as you saw in the web browser
    sc.getconf.getAll.foreach(println)
