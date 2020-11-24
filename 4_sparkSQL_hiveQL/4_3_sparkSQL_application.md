# Spark SQL application: Hive context

**Problem Statements**
Get daily revenue by product considering completed and closed orders.
  - PRODUCTS data have to be read from local file system. DataFrame need to be created
  - Join ORDERS, ORDER_ITEMS
  - Filter on ORDER_STATUS

Data need to be sorted by ascending order by date and then descending order by
revenue computed for each product for each day.
  - Sort data by order_date in ascending order and then daily revenue per product
  in descending order

The code presented here is write in spark shell. For this prupose we need to check
if you have spark context and sqlContext.

We need to create dataframe from RDD. A RDD can be created reading a text file and
 create a RDD in top of it.

    // read orders: creating RDD
    val ordersRDD = sc.textFile("/public/reatail_db/orders")

    // see the first ten lines:
    // each element inside of RDD is string
    ordersRDD.take(10).foreach(println)

    // create a dataframe in top of RDD.
    // extract each line and use cast to change the type of data
    val ordersDF = ordersRDD.map(orders => {
      (order.split(",")(0).toInt,
      order.split(",")(1),
      order.split(",")(2).toInt,
      order.split(",")(3))
      }).toDF("order_id", "order_date", "order_customer_id", "order_status")

    // to see the dataframe schema
    ordersDF.printSchema

    // to see data
    ordersDF.show()


# Run Queries using a DataFrame

    // dataframe.registerTable("table_name")
    ordersDF.registerTempTable("orders")

    // queries
    sqlContext.sql("select * from orders").show()

    // show how many records are in each order_status
    sqlContext.sql("select order_status, count(1) count_by_status from orders group by order_status * from orders").show()

    // read data from locally
    val productsRaw = scala.io.Source.fromFile("/data/retail_db/products/part-0000").getLines()

    // read data from locally
    val productsRaw = scala.io.Source.fromFile("/data/retail_db/products/part-0000").getLines().toList

    // convert to RDD
    val productsRDD = sc.parallelize(productsRaw)

    // convert RDD to dataframe and pick only the interest columns
    val productsDF = productsRDD.map(product => {
      (product.split(",")(0).toInt,
      product.split(",")(2).toInt,
      }).toDF("product_id", "product_name")

    // to see the products data
    productsDF.show()

    // create a table using the products dataframe that allows
    // run queries
    productsDF.registerTempTable("products")

    // show tables available
    sqlContext.sql("show tables").show()  

    // run sql Queries
    sqlCOntext.sql("select * from products").show()

# Solve Problem Statements

To write sql queries in multiple lines use " commands +" to pull a line.

    // to control the number of tasks used to do Queries
    // we can limit to use only few threads use a few threads
    sqlContext.setConf("spark.sql.shuffle.partitions", "2")

    // Solution
    sqlContext.sql("SELECT o.order_date, p.product_name, sum(oi.order_item_subtotal) daily_revenue_per_product " +
    "FROM orders o JOIN order_items oi " +
    "ON o.order_id = oi.order_item_order_id " +
    "JOIN products p ON p.product_id = oi.order_item_product_id " +
    "WHERE o.order_status IN ('COMPLETE, 'CLOSED') " +
    "GROUP BY o.order_date, p.product_name " +
    "ORDER BY o.order_date, daily_revenue_per_product desc").show

# Save the output in Database

    // create a database
    sql.Context.sql("CREATE DATABASE db_name_")

    // create the table
    sql.Context.sql("CREATE TABLE db_name.daily_revenue" +
    "(order_date string, product_name string, daily_revenue_per_product float)" +
    "STORED AS orc")

    // store the solution in a variable. The output is a dataframe
    val daily_revenue_per_product = sqlContext.sql("SELECT o.order_date, p.product_name, sum(oi.order_item_subtotal) daily_revenue_per_product " +
    "FROM orders o JOIN order_items oi " +
    "ON o.order_id = oi.order_item_order_id " +
    "JOIN products p ON p.product_id = oi.order_item_product_id " +
    "WHERE o.order_status IN ('COMPLETE, 'CLOSED') " +
    "GROUP BY o.order_date, p.product_name " +
    "ORDER BY o.order_date, daily_revenue_per_product desc").show

    // insert data into table
    daily_revenue_per_product.insertInto("db_name.daily_revenue")

    // do a query using the table created
    sqlContext.sql("SELECT * from db_name.daily_revenue").show

Dataframe
- insertInto: if False it is do an append. If it is True it replace the exiting the data.
- saveAsTable: if even the table is not created we can used this command
- write: we can save data into a file chose different data formats: orc, parquet, json, text


# Dataframe operations

Only new operations
- show, select, filter, join and more

      // show 100 records
      daily_revenue_per_product.show(100)

      // options to save dataframe in a file
      // option 1
      daily_revenue_per_product.save("/path/filename_1", "json")
      //option 2
      daily_revenue_per_product.write.json("path/filename_2")

      // inside of hadoop cluster to check the file was saved
      hadoop fs -tail /path/filename_1", "json")
      hadoop fs -tail /path/filename_2", "json")

      // convert dataframe into RDD
      daily_revenue_per_product.rdd

      // see first 10 rows
      daily_revenue_per_product.rdd.take(10).foreach(println)

      // show data provide the fields name
      daily_revenue_per_product("order_data", "daily_revenue_per_product").show

      // number of records
      daily_revenue_per_product("order_data", "daily_revenue_per_product").count

      // filter data
      daily_revenue_per_product.filter(daily_revenue_per_product("order_date") === "2013-07-25 00:00:00.0")


It is **recommend to use SQL queries** instead of dataframe operations because **SQL we can have more flexibility**.
