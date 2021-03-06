# Scala Development Application

We need to create some folders inside of our project named as *retail*:
- retail/src/
- retail/src/main/
- retail/src/main/scala/

Inside of project folder *retail* we need to have the sbt file name as *build.sbt*.
Here we add external dependencies. Go to website *spark programming guide* and choose *linking with spark*
to see how add dependencies.

    name := "retail"
    version:= "1.0"
    scalaVersion := "2.10.6"

    libraryDependencies += "org.apache.spark" % "spark-core_2.10" % "1.6.3"

To validate it type on linux command line the next command to download all
sbt dependencies and enter in scala console wit all sbt dependencies installed.

    sbt console

To verify if the installation occurred without problems, type in scala:

    import org.apache.spark.SparkContext

Copy the program line to empty file named as *DailyRevenue.scala*
located in folder *retail/src/main/scala/* :

    import org.apache.spark.{SparkContext, SparkConf}

    object DailyRevenue {
      def main(args: Array[String]) = {
        val conf = new SparkConf().setMaster("local").setAppName("Daily Revenue")
        val sc = new SparkContext(conf)

        // 1- Define folders
        val baseDir = "/local_system/foldername/"
        val productsPath = "/local_system/foldername/filename_p"
        val ouputPath = "/local_system/foldername/filename_out"

        // 2- Read from orders and orders_items
        val orders = sc.textFile(baseDir + "orders")  
        val ordersItems = sc.textFile(baseDir + "orders_items")

        // 3- Filter for COMPLETED or CLOSED orders
        val ordersFiltered = orders.
          filter(order => order.split(",")(3) == "COMPLETE" || order.split(",")(3) == "CLOSED")

        // 4- Convert both filtered orders and order_times to key value pairs
        val ordersMap = ordersFiltered.
          map(order => (order.split(",")(0).toInt,order.split(",")(1)))
        val orderItemsMap = ordersItems.
          map(oi => (oi.split(",")(1).toInt,(oi.split(",")(2).toInt,oi.split(",")(4).toFloat)))

        // 5- Join the two datasets using the key of the two datasets
        val ordersJoin = ordersMap.join(orderItemsMap)

        // 6- Get daily revenue per product id
        val ordersJoinMap = ordersJoin.map(rec => ((rec._2._1, rec._2._2._1), rec._2._2._2))
        val dailyRevenuePerProductId = ordersJoinMap.
          reduceByKey((revenue, order_item_subtotal) => revenue + order_item_subtotal)

        // 7- Load products from local file system and convert into RDD
        import scala.io.Source
        val productsRaw = Source.fromFile(productsPath).getLines.toList
        val products =sc.parallelize(productsRaw)

        // 8- Join daily revenue per product id with products to get daily revenue per product (by name)
        val productsMap = products.
          map(product => (product.split(",")(0).toInt, product.split(",")(2)))
        val dailyRevenuePerProductIdMap = dailyRevenuePerProductId.
          map(rec => (rec._1_2, (rec._1_1,rec._2)))
        val dailyRevenuePerProductJoin =  dailyRevenuePerProductIdMap.join(productsMap)

        // 9- Sort the data by date in ascending order and by daily revenue per product in descending order
        val dailyReveneuePerProductSorted = dailyRevenuePerProductJoin.
          map(rec => ((rec.2_.1._1, -rec.2_1._2), (rec.2_._1._1, rec._2._1._2, rec._2._2))).
          sortByKey()

        // 10- Get the data to desired format: order_date, daily_revenue_per_product, product_name
         val dailyRevenuePerProduct = dailyReveneuePerProductSorted.
          map(rec => rec._2._1 + "," + rec._2._2 + "," + rec._2._3)

        // 11- Save final output into HDFS in avro file format as well as text file format
        dailyRevenuePerProduct.saveAsTextFIle(outputPath)
    }
  }

The process is the follow, running the commands in linux command line:
- build the JAR file: *sbt package*
- run the code: *sbt "run-main DailyRevenue"*

# Run JAR application locally

Before that make sure you delete the files that will be generated by the code
to avoid problems.

spark-submit \
  --class DailyRevenue \
  /foldername/retail/target/scala-2.10/retail_2.10-1.0.jar
