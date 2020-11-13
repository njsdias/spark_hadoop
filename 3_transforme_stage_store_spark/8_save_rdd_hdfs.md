# Save RDDs back to HDFS

- Make sure is saved with proper delimiters
- Compression

- saveAsTextFile, saveAsSequenceFile, saveAsObjectFile

      // read data
      val orders = sc.textFile("foldername/filename")

      // count order by status
      val orderCountByStatus = orders.
        map(order => (order.split(",")(3), 1)).
        reduceByKey((total,element) => total + element)

      // saving RDD abck to HDFS
      orderCountByStatus.saveAsTextFile("/foldername/filename")

      // check if we save it right
      sc.textFile(/foldername/filename).foreach(println)

In real world we not save data like this. We save it following the technical
specification defined (tab, comma, etc). This examples are used for text files.
For files like json, avro, parquet, orc delimiters are not matters because
the data will be saved using column names.

    // saving RDD back to HDFS with tab delimiter
    orderCountByStatus.
      map(reac => reac._1 + "\t" + rec._2).
      saveAsTextFile("/foldername/filename")

If the data is not stored properly we can delete file as:

    haddop fs -rm -R /foldername/filename

To see the content of the data we can use hadoop:

    haddop fs -cat /foldername/filename

# Compression

Compression is the way the HDFS stores data. The parameters that controls the
Compression are in file:

    cd etc/hadoop/conf/core-site.xml

search for *io.compression.codecs*

We can get the same information from Ambari. In your browse type the *clustername:port*
to access Ambari. In the menu choose HDFS and Configs tab. In search type *io.comp*

This two ways shows the possibility avaliable for Compression parameter. We need to
provide this information to our *saveAsTextFile* command. For instance if we want
to save the file in Snappy compression format we need to type the next command:

    orderCountByStatus.saveAsTextFile("fodlername/filename",classOf[org.apache.hadoop.io.compress.SnappyCodec])

To read the file is not necessary to specify the compression type. Thus we read
the compress file using the usual command:

    sc.textFile("/foldername/filename").collect.foreach(println)

# Save data in different file format

Data can be saved in those formats:
- orc
- json
- parquet
- avro (with databricks plugin)

To save data in these formats:
- make sure data is presented as DataFrame
- use write or save API to save Data Frame into different file formats
- Use compression algorithm if required

    // writing data into different file formats
    val ordersDF = sqlContext.read.json(foldername/filename)

If we do *ordersDF.* + *press Tab* we can see the option *save*. To save in *parquet*
file format:

    // save data in aprquet file format
    ordersDF.save("foldername/filename", "parquet")

    // read parquet file
    sqlContext.load("foldername/filename", "parquet").show

    // save as ORC format
    ordersDF.write.orc("foldername/filename", "orc")

    // read orc file using LOAD command
    sqlContext.load("foldername/filename", "orc").show
    // read orc file using READ command
    sqlContext.read.orc("foldername/filename").show

These command pairs do the same:
- save and write
- read and load
