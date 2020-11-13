# Basic Map Reduce

Convert range to List

    val l = (1 to 100).toList

Now, we want to *get the sum of squares of all even values of the list*. We can filter the even
values and sum them up. The function filter (an internal Scala functions)
takes a function as parameter that receives integer and returns Boolean:

    def filter(p: Int => Boolean): List[Int]

Thus we can do it like this:

    val f = l.filter(ele => ele % 2 == 0)

We can see that, filter is applied to list "l" .

Now we can evaluate the square of each even value using map. Map takes a function
and returns the transformations

    def map[B](f. Int => B): scala.collection.TranversableOnce[B]

    val m = f.map(rec => rec* rec)

We can see that, map is applied to filter "f".

Now we are applying reduce to map and get the sum.

    val r = m.reduce((total, element) => total + element)

It do a recursive sum. Suppose we have this list of values:

    List(4, 16, 36, 64)

The sum is processed like this:

    (total,element) => total + element
    4 + 16 = 20
    20 + 36 = 56
    56 + 64 = 120
