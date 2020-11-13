# Object Oriented: Part 2

    object HelloWorld{
      def main(args: Array[String]): Unit = {
        println(hello world")
      }
    }

To invoke the object:

    HelloWorld.main(Array(" "))

Suppose you have a class Order already defined. If you declare an object with
the same name it is **the companion object**.

    class Order(var orderId: Int, var orderDate: String, var orderCustomerId: Int, var orderStatus: String){
      println("I am inside Order Constructor
      override def toString = "Order(" + orderid + "," + orderDate + "," + orderCustomerId + "," + orderStatus + ")"
    }

    object Order {
      # Special static method named apply
      def apply(orderId: Int, orderDate: String, orderCustomerId: Int, orderStatus: String): Order = {
        new Order(orderId, orderDate, orderCustomerId, orderStatus)
      }
    }

    val order = Order.apply(1, "203-10-01 00:00:00.000", 100, "COMPLETE")

Scala creates two types of objects. To see that type the follow two commands:

    :java -p Order
    :java -p Order$

With this methodology we can have a better control of what happens with the class.

We can play with this thing in sense that if you define in apply function the arguments as strings
that were defined as Integer in class we can transform those Strings to Integer:

    object Order {
      # Special static method named apply
      def apply(orderId: String, orderDate: String, orderCustomerId: String, orderStatus: String): Order = {
        new Order(orderId.toInt, orderDate, orderCustomerId.toInt, orderStatus)
      }
    }

    val order = Order.apply("1", "203-10-01 00:00:00.000", "100", "COMPLETE")
