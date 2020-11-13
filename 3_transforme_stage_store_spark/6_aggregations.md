# Aggregations

Objective is get for each order state get count. For example, "How many Closed" status we have"?
For this purpose we use *countByKey*.

    // Aggregations - using actions
    val orders = sc.textFile(""/foldername/table_1")

    // extract values from the column and count them by key
    val orderStatus = orders.map(order => (order.split(,)(3),"")).countByKey

    // print data
    orderStatus.foreach(println)

Get the total and the maximum of Revenue column

    // Read data
    val orderItems = sc.textFile("/foldername/table_2")

    // Get the values of Revenue column
    val orderItemsReveneue = orderItems.map(oi => oi.split(",")(4).toFloat)

    // get revenues total
    val totalRevenue = orderItemsReveneue.reduce((total, revenue) => total + revenue)

    // get the max revenue
    val orderItemsMaxRevenue = orderItemsReveneue.reduce((max, revenue) => {
      if (max < revenue) revenue else max
    })

# Combinations

Due the *groupByKey* not use combiners the *reduceByKey* and *aggregateByKey*
have better performance.

See the next example.

Get the total of a bunch of data using *groupByKey*
- it performance the sum taking one value and add the previous value
  - sum (1 to 1000) it will be performance like: 1 + 2 + 3 + 4 + ... + 1000

Get the total of a bunch of data using *reduceByKey*
- it performance the sum taking onve value and add the previous value
  - sum (1 to 1000) it will split values in a set of bunch of data and performance
  the summation in each bunch. At the end it sum each sum getting the total

        sum (sum(1, 250) sum(1, 250) sum(1, 250) sum(1, 250) )

# Demonstration of *groupByKey*

Use *groupByKey* only if is not possible to use the *reduceByKey* or *aggregateByKey*.

Get the revenue for each *order_id* and sort the order_item_subtotal for each order_id

    // Read data
    val orderItems = sc.textFile("/foldername/table_2")

    // construct (K,V) for groupByKey
    val orderItemsMap = orderItems.
      map(oi => (oi.split(",")(1).toInt, oi.spit(",")(4).toFloat))

    // performance the group by key. An array will be formed by values that belong
    // a same key. (2, [300.0, 438.0, 567.0]) (5, [213.0, 345.0, 65.0])
    val orderItemsGBK = orderItemsMap.groupByKey

    // Get Revenenue
    // Due the output of orderItemsGBK is a tuple of tuples we need to get the right
    // tuple that contains the values that we want and transform it into a List and sum.
    // The advice is always see the output of a command for implement the right pocedure
    val orderRevenue = orderItemsGBK.map( rec => rec._2.toList.sum)

    // print the result
    orderRevenue.take(10).foreach(println)

    // Get data in descending order by order_item_subtotal for each order_id
    / For this we need to use flatMap in order to get multiple results
    val ordersSortedByRevenue = orderItemsGBK.flatMap(rec => {
      // get the values list and order it by descend fashion
      // after that map by a key that is the descent order list and build
      // a tuple with order_item and the key
      rec._2.toList( o => -o).map( k => (rec._1, k))
    ) }

    // print the result
    ordersSortedByRevenue.take(10).foreach(println)

# reduceByKey(func,[numTasks])

It requires a function to transform the result for each key.

    // Read data
    val orderItems = sc.textFile("/foldername/table_2")

    // construct (K,V)
    val orderItemsMap = orderItems.
      map(oi => (oi.split(",")(1).toInt, oi.spit(",")(4).toFloat))

    // Total of revenue per order_id
    val revenuePerOrderId = orderItemsMap.
    reduceByKey((total, reveneue) => total + revenue)

    // Total of revenue per order_id
    val minrevenuePerOrderId = orderItemsMap.
    reduceByKey((min, reveneue) => if(min > revenue) revenue else min)

# aggregateByKey(zeroValue)(seqOp, combOp, [numTasks])    

It requires two functions. It uses intermediate values to compute a final value.

    // Read data
    val orderItems = sc.textFile("/foldername/table_2")

    // construct (K,V)
    val orderItemsMap = orderItems.
      map(oi => (oi.split(",")(1).toInt, oi.spit(",")(4).toFloat))

    // (order_id, order_item_subtotal)
    // (order_id, (order_revenue, max_order_item_subtotal))
    val revenueAndMaxPerProductId = orderItemsMap.
      aggregateByKey((0.0f,0.0f))(  // initialize the tuple
        (inter, subtotal) => (inter._1 + subtotal, if(subtotal > inter._2) subtotal else inter._2) ,
        (total, inter) => (total._1 + inter._1, if(total._2 > inter._2) total._2 else inter._2)
      )

    // to check the results compare these two lines
    orderItemsMap.sortByKey().take(10).foreach(println)
    revenueAndMaxPerProductId.sortByKey().take(10).foreach(println)

# sortByKey([ascending], [numTasks])

**Data Structure**
product_id, product_category_id, description, price, url_image_of_product

    // read data RDD[String]
    val products = sc.textFile("foldername/filename")

    // sort by category_id
    // use map to transform RDD into (K,V)
    val productsMap = products.
      map(product => (product.split(",")(1).toInt, product))

    // performance the sort  
    val productsSortedByCategoryId = productsMap.sortByKey()

    // for each product category ordered we want to see in descent order of product price
    val productsMap = products.
      map(product => (products.split(",")(1).toInt, products.split(",")(4).toFloat, product))

    // performance the sort  
    val productsSortedByCategoryId = productsMap.sortByKey()

    // to collect the values without keys
    val end_result = productsSortedByCategoryId.map(reac => rec._2)

# Ranking: takeOrdered(n, [ordering])

Ranking - Global (details of top 10 products)

  // read data RDD[String]
  val products = sc.textFile("foldername/filename")

  // making the price as key and the product is a value
  val productsMap = products.
    map(product => (product.split(",")(4).toFloat, product))

  val productsSortedByPrice = productsMap.sortByKey(false)

  val productsSortedByPriceTopTen = productsSortedByPrice.take(10)

An alternative

  val productsSortedByPrice = products.takeOrdered(10)(Ordering[Float].
    reverse.on(product => product.split(",")(4).toFloat))

# Ranking by key

Ranking: get top N priced products with in each product category

    // read data RDD[String]
    val products = sc.textFile("foldername/filename")

    // get the category id as key and product information as value
    val productsMap = products.map( product => (product.split(",")(1).toInt, product) )

    // group data by key
     val productsGroupByCategory = productsMap.groupByKey

     // get the collection of the category
     // (category_id, CompactBuffer(collection)
     // considering the first record of the RDD and get the collection
     val productsIterable = productsGroupByCategory.first._2

     def getTopNPricedProducts(productsIterable: Iterable[String], topN: Int): Iterable[String]= {
       // input: productsIterable that is the collection
       // input topN: the N top priced products
       // it is a iterable string because we want the name of those products  

       // extract the price from collection as float and then sort it and eliminate the duplicates

       // get the price and eliminate the duplicate using toSet
       val productPrices = productsIterable.map(p => p.split(",")(4).toFloat).toSet

       // to sort we need to convert the Set into a List
       // to get a descend order we only need to negate (-p)  
       val topNPrices = productPrices.toList.sortBy(p => -p).take(topN)

       // Get all the products in descending order by price
       val productsSorted= productsIterable.toList.sortBy(product => -product.split(",")(4).toFloat)

       // get the minimum price of the list
       val minOfTopNPrices = topNPrices.min

       // get the TopN products with high prices
       val topNPricedProducts = productsSorted.takeWhile(product =>.split(",")(4).toFloat >= minOfTopNPrices)

       // function output
       topNPricedProducts
     }

Get all the products in descending order by price
     val productsSorted= productsIterable.toList.sortBy(product => -product.split(",")(4).toFloat)

Having the TopN prices ordered we can compare with the product collection
that has the price too. If this prices is high than the minimum price that is
in the TopN list we save it.

    val minOfTopNPrices = topNPrices.min
    val topNPricedProducts = productsSorted.takeWhile(product =>.split(",")(4).toFloat >= minOfTopNPrices)

The function can be used like:

    getTopNPricedProducts(productsIterable, 5).foreach(println)

But we want this functionality for each product category.

    val top3PricedProductsPerCategory = productsGroupByCategory.flat( rec => getTopNPricedProducts(rec._2,3))
