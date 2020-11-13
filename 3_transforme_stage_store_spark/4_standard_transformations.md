# Standard Transformations

- Filtering
- String Manipulation (Scala)
- Row Level Transformations
- Joins
- Aggregation
- Sorting
- Ranking
- Set Operations

To get more information we can go to Spark documentation. The transformations
are part of RDD.

Some examples:

- To aggregate: reduceByKey , aggregateByKey
- To sort: sortByKey
- for ranking: groupByKey
- to join datasets: use join

# String Manipulation (Scala)

    // Create RDD
    val orders = sc.textFile("foldername/filename")
    val productRaw = scala.io.Source.fromFile("/foldername/filename").getLines.toList
    val products = sc.parallelize(productsRaw)

    // verify the output of a RDD is a String
    val str = orders.first

    // split by a delimiter
    val a = str.split(",")

    // elements access of a string
    a(0)
    a(1)

    // converto to other types: tpInt, toFLoat, toDouble, toString
    // convert to integer
    val orderID = a(0).toInt

    // verify if a string has a set of characters
    a(1).contains("2013")

    val orderDate = a(1)
    // extract a substring from  the 1rst position to the 10th position
    orderDate.substring(0, 10)
    // extract a substring from  the 11th position to the end
    orderDate.substring(11)

    // replace characters
    orderData.replace('-', '/')
    orderDate.replace("07", "July

    // check the index of a character inside of a String
    ofrderDate.indexOf("2")

    // check the 2nd ocurrence of a character
    orderDate.indexOf("2", 2)

    // length of a String
    orderDate.length

We have more options. The function most used is Split function

# Row Level Transformations

## Map
The map is the most transformation function used to transform data.

    // Create RDD
    val orders = sc.textFile("foldername/filename")

The objective performance the next transformation:
-  1,2013-07-25 00:00:00.0.11599,CLOSED -> 20130725 as Int

        // get the first record of data
        val str = orders.first
        // get the date, replace  "-" by "" and convert it to integer
        str.split(",")(1).substring(0,10).replace("-","").toInt

        // do the same for all data
        val orderDates = orders.map((str: String) => str.split(",")(1).substring(0,10).replace("-","").toInt )

        // check the result
        orderDates.take(10).foreach(println)

If we want to Join or aggregateBykey  it requires a RDD of type (Key,Value). Each element
of RDD needs to be a tuple. We need to convert the next in to (K,V) pairs that can be Join.

- 1,2013-07-25 00:00:00.0.11599,CLOSED

Thus the orderID will be the Key and the Date will be the Value.

    val ordersPairedRDD = orders.map(order => {
      // split the RDD by delimiter ","
      val o = order.split(",")   
      // Key o(0): take the first element and convert it to Integer   
      // Value o(1):  get the date, replace  "-" by "" and convert it to integer
      (o(0).toInt. o(1).substring(0,10).replace("-","").toInt)  
      })

    // to check the result
    ordersPairedRDD.take(10).foreach(println)

## FlatMap: groupByKey

    val = l= List("Hello", "How are you doing", "Let us perform word count",
    "As part of the word count program", "!"we will see how many times each word repeat")

    val l_rdd = sc.parallelize(1)

    // in each element of list split it by space to get each word that is inside of those elements.
    val l_FlatMap = l_rdd.flatMapp(ele => ele.split(" ")

    // print words
    l_FlatMap.take(10).foreach(println)

    // procedd the word count
    val wordcount = l_FlatMap.map(word =>  (word, "")).

# Filtering (horizontal, vertical)

- Vertical: we use *map* transformation

        // read data
        val orders = sc.textFile("foldername/filename")

        // Filtering Data
        ordersFilter = orders.filter(order => order.split(",")(3) == "COMPLETE")

        // print the result
        ordersFilter.take(10).foreach(println)

        // amount of records got by filter transformation
        ordersFilter.count

- Horizontal

        // check if we have COMPLETE string in the record
        val s = orders.first

        // using contains
        s.contains("COMPLETE") || s.contains("CLOSED")

        // using split
        s.split(",")(3) == "COMPLETE" ||   s.split(",")(3) == "CLOSED"

        // objective get all the orders from 2013-09 which are in CLOSED or COMPLETED

        // 1- get the unique values of orders status: complete,closed, pending, ect)
        orders.map(order => order.split(",")(3)).distinct
        // 2- print the result
        orders.map(order => order.split(",")(3)).distinct.collect.foreach(println)

        // achieve the objective
        val ordersFiltered= orders.filter( order => {
          val o = order.split(",")
          (o(3) == "COMPLETE" || o(3) == "CLOSED") && (o(1).contains("2013-09"))
          })  

        // print result
        ordersFiltered.take(10).foreach(println)
        
        // amouht of records
        ordersFiltered.count
