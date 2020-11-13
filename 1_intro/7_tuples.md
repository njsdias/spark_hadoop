# Tuples

- It is generic object type where there are no names associated with it
- It represents a record with different attributes and data types
- Elements can be accessed using positional notion

    // Read the file line by lines
    val orderItems = Source.fromFile("path/filename").getLines

To create an object by names use tupples:

    val t = (1,1,957,1,299.98,299.98)

A tuple is not more than an object. To see the options available to use with a
tuple type t. and press Tab key in your keyboard. We can see to access the
tuple elements we use them position To element four use:

    t._4
