# Problem Statement

- Use retail_db dataset

- problem Statement
  - get daily revenue by product considering completed and closed orders
  - data need to be stored by ascending order by date and then descending order
  by revenue computed for each product for each day
  - data should be delimited by "," in this order: order_date, daily_revenue_per_product, product_name

- Data for orders and order_items is available in HDFS

- Data fro products is available locally

- Final output need to be stores under
  - HDFS location - avro fromat
    - /user/userid/daily_revenue_avro_scala
  - HDFS location - text formats
    - user/userid/daily_revenue_txt_scala
  - Local location
    - home/userid/daily_revenue_scala
  - Solution need to be stored under
    - home/userid/daily_revenue_scala_txt

# Solution Steps
- Launch Spark Shell
- read orders and orders_items
- Filter for completed or closed orders
- convert both filtered orders and orders_items to key valu pairs
- join the two data sets
- get daily revenue per product id
- load products from local file system and convert into RDD
- join daily revenue per product id with products to get daily revenue per product (by name)
- sort the data bu date in ascending order and by daily revenue per product in descending order
- get data to desired format: order_date, daily_revenue_per_product, product_name
- save final output into HDFS in avro file format as well as text file formats
  - user/userid/daily_revenue_avro_scala
  - user/userid/daily_revenue_txt_scala
- copy both from HDFS to local file system
    - home/userid/

#

Depend on the amount of data that you try to process  you need to check:
- number of executors that are defined knowing:
  - resources that are in the cluster that are available to process the data
  - size of files that we try to process

**Size of data**

    hadoop fs -ls -h /fodlername/filename

Suppose we are trying to process 10GB. The number of executors that we need to use depends on the capacity of the cluster. To see the cluster resources:
- access to /etc/hadoop/conf/yarn-site.xml and search for *yarn.resource.webapp.address*.

It allows you to get the address to enter in YARN cluster configuration copying and past it in your web browser.
Look for *Memory Total* and *VCores Total*. Check for *Minimum Allocation* and *Maximum Allocation* parameters.
The values are in MB (Minimum 1024 MB = 1GB ; vCores:1, Maximum 4096 MB = 4GB ; vCores:6).

The number of executors CAN NOT exceed the Vcores Total x Memory Total and
we CAN NOT exceed the *Maximum Allocation*.

VCores Total is the number of executors. We can say to use 3GB (not exceeding the maximum allocation)
 for each executor.

# Solution commands    

    // 1- Launch Spark Shell
    spark-shell --master yarn \
      --num-executors 1 \
      --executor-memory 512M \
      -- conf spark.ui.port=12673

    // *-*-*-*-*-*-*-*-*-*-*-*-*
    // 2- Read from orders and orders_items
    val orders = sc.textFile("foldername/orders")  
    val ordersItems = sc.textFile("foldername/orders_items")

    // a) display data
    orders.first
    orderItems.first

    orders.take(10).foreach(println)
    ordersItems.take(10).foreach(println)

    // *-*-*-*-*-*-*-*-*-*-*-*-*
    // 3- Filter for COMPLETED or CLOSED orders
    // a) to understand the data
    orders.map(order => order.split(",")(3)).distinct.collect.foreach(println)

    // b) filtering the data a requires
    val ordersFiltered = orders.
      filter(order => order.split(",")(3) == "COMPLETE" || order.split(",")(3) == "CLOSED")

    // c) check the result
    ordersFiltered.take(10).foreach(println)

To JOIN the data we need to convert the data as (K,V) pairs.

    // *-*-*-*-*-*-*-*-*-*-*-*-*
    // 4- Convert both filtered orders and order_times to key value pairs
    // a) Convert the order_id into key and date into value pairs
    val ordersMap = ordersFiltered.
      map(order => (order.split(",")(0).toInt,order.split(",")(1)))

    // b) Convert the order_item_id to key and the
    // tuple(order_item_product_id, order_item_subtotal) as value pairs
    val orderItemsMap = ordersItems.
      map(oi => (oi.split(",")(1).toInt,(oi.split(",")(2).toInt,oi.split(",")(4).toFloat)))

    // c) check the result
    ordersMap.take(10).foreach(println)
    orderItemsMap.take(10).foreach(println)

    // *-*-*-*-*-*-*-*-*-*-*-*-*
    // 5- Join the two datasets using the key of the two datasets

    // a) using the regular JOIN (it is different from leftOuterJoin and rightOuterJoin)
    val ordersJoin = ordersMap.join(orderItemsMap)

    // b) check the result
    ordersJoin.take(10).foreach(println)

    // *-*-*-*-*-*-*-*-*-*-*-*-*
    // 6- Get daily revenue per product id

    // ordersJoin gives data as:(order_id, (order_date, (order_item_product_id, order_item_subtotal)))

    // a)convert to
    // key: order_date, order_item_product_id
    // value: order_item_subtotal
    val ordersJoinMap = ordersJoin.map(rec => ((rec._2._1, rec._2._2._1), rec._2._2._2))

    ordersJoinMap.take(10).foreach(println)

    // b) get daily revenue
    // key: order_date, order_item_product_id
    // value: daily_revenue_per_product_id
    val dailyRevenuePerProductId = ordersJoinMap.
      reduceByKey((revenue, order_item_subtotal) => revenue + order_item_subtotal)

    dailyRevenuePerProductId.take(10).foreach(println)

The business statement requires product_name instead of product_id. The product_name
information is in other table that is in local file system. Thus we need to
read the data from local system and join it with the previous output.

    // 7- Load products from local file system and convert into RDD
    // getLines make Iteratable and toList convert into a Sequence that allow us
    // to parallelize and convert it to a RDD
    import scala.io.Source
    val productsRaw = Source.fromFile("/foldername/filename").getLines.toList

    // convert list to a RDD
    val products =sc.parallelize(productsRaw)

    // *-*-*-*-*-*-*-*-*-*-*-*-*

    // 8- Join daily revenue per product id with products to get daily revenue per product (by name)

    // a) to proceed a JOIN we need to build a (K,V) pairs
    // K: product_id ; V: product_name
    val productsMap = products.
      map(product => (product.split(",")(0).toInt, product.split(",")(2)))

    // b) check the result
    productsMap.take(10).foreach(println)
    productsMap.count

To be possible to JOIN the two datasets is necessary to have the same id.
For this reason we need to add an additional transformation in dailyRevenuePerProductId
that has the data like: (order_date, order_product_id), daily_revenue_per_product_id)

    // c) (order_product_id,(order_date,daily_revenue_per_product_id))
    val dailyRevenuePerProductIdMap = dailyRevenuePerProductId.
      map(rec => (rec._1_2, (rec._1_1,rec._2)))

    // d) proceed with Join
    val dailyRevenuePerProductJoin =  dailyRevenuePerProductIdMap.join(productsMap)
    dailyRevenuePerProductJoin.take(10).foreach(println)

    // *-*-*-*-*-*-*-*-*-*-*-*-*

After JOIN we have:

(order_product_id,((order_date,daily_revenue_per_product_id), product_name))

    // 9- Sort the data by date in ascending order and by daily revenue per product in descending order
    // order_date, daily_revenue_per_product, product_name.

    // a) We want to obtain data as (K;V) pairs with in follow format:
    //  (order_date_asc, daily_revenue_per_product_desc), (order_date, daily_revenue_per_product, product_name))
    val dailyReveneuePerProductSorted = dailyRevenuePerProductJoin.
      map(rec => ((rec.2_.1._1, -rec.2_1._2), (rec.2_._1._1, rec._2._1._2, rec._2._2))).
      sortByKey()
    dailyReveneuePerProductSorted.take(100).foreach(println)

    // 10- Get the data to desired format: order_date, daily_revenue_per_product, product_name
     val dailyRevenuePerProduct = dailyReveneuePerProductSorted.
      map(rec => rec._2._1 + "," + rec._2._2 + "," + rec._2._3)
    dailyRevenuePerProduct.take(10).foreach(println)

Save the result in files

    // 11- Save final output into HDFS in avro file format as well as text file format
    dailyRevenuePerProduct.saveAsTextFIle("/foldername/filename")
    dailyRevenuePerProduct.save("/foldername/filename", "avro")

    // a) preview the data
    sc.textFile("/foldername/filename").take(10).foreach(println)

In cluster    :

  hadoop fs -tail /foldername/filename

Copy both from HDFS to local file system:
- this copy the directory inside of foldername
    - hadoop fs -get /hadoop_system/foldername /local_system/foldername/.
- this copy the files inside of foldername
    - hadoop fs -get /hadoop_system/foldername /local_system/foldername
