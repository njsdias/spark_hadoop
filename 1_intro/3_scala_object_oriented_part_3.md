# Object Oriented: Part 3

## Case Classes
  - Serializable
  - Product
  - some important methods


Case class by default needs to have class variables. We cannot build
a case class with constructor arguments. It is not possible.

    case class Order(orderId: Int, orderDate: String, orderCustomerId: Int, orderStatus: String){
      println("I am inside Order Constructor
      override def toString = "Order(" + orderid + "," + orderDate + "," + orderCustomerId + "," + orderStatus + ")"
    }

By default a case calss considers all variables as immutable variables

    case class Order(orderId: Int, orderDate: String, orderCustomerId: Int, orderStatus: String){
      println("I am inside Order Constructor
      override def toString = "Order(" + orderid + "," + orderDate + "," + orderCustomerId + "," + orderStatus + ")"
    }

To confirm it type *:javap -p Order*

It automatically creates a class as

- scala.Produt
- scala.Serializable

It also provides the toString function even if you don't use it in case class.
It allow us to use other elements related with scala.Product:

- productPrefix(), productElement(), productIterator()

We have the Order$ object in automatic way too.



But you can change the immutable to mutable telling explicitly that you want variables as mutable variables:

    case class Order(var orderId: Int, var orderDate: String, var orderCustomerId: Int, var orderStatus: String){
      println("I am inside Order Constructor
      override def toString = "Order(" + orderid + "," + orderDate + "," + orderCustomerId + "," + orderStatus + ")"
    }

To access of elements write:

    order.productElement(0) -> it is the 1rst element

A rule is when you want to see the options that you have available type
the variable name with dot and next press tab. To do this procedure
all times that you want until you see the end of options:

    order.productIterator.foreach(println)
