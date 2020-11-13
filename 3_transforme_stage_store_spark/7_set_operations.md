# Set Operations

**Union, Intersection, Distinct**

Intersection eliminates the duplicates in automatic way.

Union includes duplicates. We need to use distinct.

    // read dataset
    // oder_id(0), order_date(1), order_customer_id(2), order_status(3)
    val orders = sc.textFile("foldername/filename")

    // get clients with orders in 2013/08
    val customers_201308 = orders.
      filter(order => order.split(",")(1).contains("2013-08")).
      map(order => order.split(",")(2).toInt)

    // get clients with orders in 2013/09
    val customers_201309 = orders.
      filter(order => order.split(",")(1).contains("2013-09")).
      map(order => order.split(",")(2).toInt)

    // Get all the customers who placed orders in 2013/08 AND 2013/09
    val customer_201308_and_201309 = customers_201308.intersection(customers_201309)

    // Get all unique customers who placed orders in 2013/08 OR 2013/09
    val customer_201308_or_201309 = customers_201308.union(customers_201309).distinct

Get all customers who placed orders in 2013/08 but nor in 2013/09
LeftOuterJoin gives us all records from left table and
for those records that are in right table which are not in left table
they are identified as None.  Filter allow us to collect the orders with date 2013/08.

    val customer_201308_minus:201309 = customers_201308.map(c => (c,1)).
      leftOuterJoin(customers_201309.map(c => (c,1))).
      filter(rec => rec._2._2 == None).
      map(rec => rec._1).  // to get only the customer_id
      distinct
