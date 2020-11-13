# Joins

To JOIN you need a common key between two tables.
- (K, V) join (K,W) result (K,(V,W))

Table:1
- orderID, orderDate, orderCustomerID, orderStatus

Table2:
- ID(PK), orderID(FK), productID, quantity, subtotal, productPrice

      // read data
      val orders = sc.textFile(""/foldername/table_1")
      val orderItems = sc.textFile("/foldername/table_2")

      // create (K,V)
      val ordersMap = orders.map(order => {
        (order.split(",")(0).toInt, order.split(",")(1).substring(0,10))
        })
        // create (K,W)
        val odersItemsMap = ordersItems.map(orderItem => {
          val oi = order.split(",")
          (oi(1).toInt, oi(4).toFloat)
          })

      // process the Join between two tables
      val ordersJoin= ordersMap.join(orderItemsMap)

      // check results
      ordersJoin.take(10).foreach(println)

      // count results
      ordersJoin.count

# Left JOIN

Get all the orders which do not have corresponding entries in the order items.

    // read data
    val orders = sc.textFile(""/foldername/table_1")
    val orderItems = sc.textFile("/foldername/table_2")

    // create (K,V)
    val ordersMap = orders.map(order => {
      (order.split(",")(0).toInt, order)
      })
    // create (K,W)
      val odersItemsMap = ordersItems.map(orderItem => {
        val oi = order.split(",")
        (oi(1).toInt, orderItem)
      })

      // process the LeftOuterJoin between two tables
      val ordersLeftOuterJoin= ordersMap.leftOuterJoin(orderItemsMap)

      // Get only the entries without any corresponding cases (None values)
      val ordersLeftOuterJoinFilter = ordersLeftOuterJoin.filter(order => order._2._2 == 'None')

      // check results
      ordersLeftOuterJoinFilter.take(10).foreach(println)

      // count results
      ordersLeftOuterJoinFilter.count

      // Extract values from orders with no order item
      val ordersWithNoOderItem = ordersLeftOuterJoinFilter.map(order => order._2._1)

      // check results
      ordersWithNoOderItem.take(10).foreach(println)


**Important Note**:
Explanation of tuple notion
Our data is a tuple with this structure: (Int, (String, Option[String])).

- t._1: access to Int value
- t._2: access to (String, Option[String]) values
- t._ 2._1: access to String value
- t._ 2._2: access to Option[String] value

# Right JOIN

We can solve the same problem with Right Outer Join transformation.

    val ordersRightOuterJoin = orderItemsMap.rightOuterJoin(ordersMap)

    val ordersWithNoOrderItem = ordersRightOuterJoin.
      filter(order._2._1 == None).
      map(order => order._2._2)

      // check results
      ordersWithNoOrderItem.take(10).foreach(println)
