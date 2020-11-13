# Basic I/O Operations

Access Data [here](https://github.com/dgadiraju)

To do a git clone of repository with name *data* and in this example we are using *retail_db*:

- oderItem
- orderItemAlready
- ProductID
- quantityBought
- sellsMoney
- productPrice

Read files from scala

    // Import IO Library
    import scala.io.Source

    // Read the file line by lines
    val orderItems = Source.fromFile("path/filename").getLines

    // Print the first ten lines
    orderItems.take(10).foreach(println)

    // to see the size of the dataset
    orderItems.toList.size

    // To see the first element of the list
    val orderItems = Source.fromFile("path/filename").getLines.toList
    orderItems(0)

    // Extract the sum of all sellsMoney where orderItemAlredy equal two
    val orderItemsFiler = orderItems.filter(orderItem => orderItem.split(",")(1).toInt == 2)



    val orderItemsMap = orderItemsFilter.map(orderItem => orderItem.split(",")(4).toFloat)
    val orderItemsMap.reduce((total, orderItemSubTotal) => total + orderItemSubtotal)

    // this other way to right the last functions using inplace (_)
    val orderItemsFiler = orderItems.filter(_.split(",")(1).toInt == 2)
    val orderItemsMap = orderItemsFilter.map(_.split(",")(4).toFloat)
    val orderItemsMap.reduce(_ + _)
