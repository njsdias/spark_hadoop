# Object Oriented: Part 1

- Classes
- Constructor Arguments
- Class Variables

# Classes

    class Order(orderId: Int, orderDate: String, orderCustomerId: Int, orderStatus: String){
      println("I am inside Order Constructor
      def func1(...){...}
      def func2(...){...}  
    }

It creates a new Order.

    val order = new Order(1, "203-10-01 00:00:00.000", 100, "COMPLETE")

When you execute it it gives an object reference: Order=Order@4c623b03 .

A better way is:

    class Order(orderId: Int, orderDate: String, orderCustomerId: Int, orderStatus: String){
      println("I am inside Order Constructor
      override def toString = "Order(" + orderid + "," + orderDate + "," + orderCustomerId + "," + orderStatus + ")"
    }

This gives the output:

    Order(1, "203-10-01 00:00:00.000", 100, "COMPLETE

When you do

    :javap -p Order

you will see the arguments are defined as private. To access them you need to
define the argument as val

    class Order(val orderId: Int, val orderDate: String, val orderCustomerId: Int, val orderStatus: String){
        ....
    }

Now we can access to the values, but pay attention they were defined as immutable:

      order.orderId

If you want to modify them you need to change from val to var. Now it is possible to modify
them, for instance:

      order.orderId = 2

And you can check it type:

      order

You have other option to modify this value:

      order.orderId_ = (3)

This is possible because when you do: ":javap -p Order" you see:

      public: void orderId_$eq(java.lang.String)

pay attention that you have: $_eq
