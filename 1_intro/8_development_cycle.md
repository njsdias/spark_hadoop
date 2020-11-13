# Development Life Cycle

- Develop the application
- Build jar files
- Run as jar file

Find the [Dataset](https://github.com/dgadiraju/retail_db)

To develop an application put your file in the location:

    scalademo/retail/src/main/scala

- src: standard source code
- main: main module for the application
- scala: name of language

Inside of this folder create a file named as *orderRevenue.scala* with the code:

    import scala.io Source

    object OrderRevenue{

      // to make this object executable create a main function
      def main(args: Array[String]) = {

        val orderItems = Source.fromFile("path/filename").getLines

        val orderRevenue = orderItems.
          filter(oi => oi.split(",")(1).toInt == 2).
          map(oi=> oi.split(",")(4)).toFloat).
          reduce((t,v)=> t+v)
        println(orderRevenue)
        }
    }  

To avoid to compile every time the code that we developed we convert it to jar file,
that is a file that runs a compiled code every time that you run it.
To convert into a jar file we need to build it with SBT that is the most popular one.

To build the application with sbt we need to create a file: *build.sbt* inside of
*retail* folder (note write the comments that you see below)

    name := "retail"  // application name
    version := "1.0"   // application version
    scalaVersion := "2.10.6"  // scala version installed

To build the application to a jar file run the command:

    sbt package

It creates a folder *target/scala-2.10* and compile the code in two classes
and build a jar file *retail_2.10-1.0.jar* that is compounded by
*application_name_scala_version_application_version.jar*. To run the jar file
type the command:

    sbt run

However, it is more interesting to evaluate the Revenue for any item. In this case we
need to run our application with arguments. For this we need to change our previous code.

    import scala.io Source

    object OrderRevenue{

      // to make this object executable create a main function
      def main(args: Array[String]) = {

        val orderId = args(0).toInt

        val orderItems = Source.fromFile("path/filename").getLines

        val orderRevenue = orderItems.
          filter(oi => oi.split(",")(1).toInt == orderId).
          map(oi=> oi.split(",")(4)).toFloat).
          reduce((t,v)=> t+v)
        println(orderRevenue)
        }
    }  

After save it we need build again with the command

    sbt package

and run again the jar file with argument

    sbt "run-main OrderRevenue 5"
