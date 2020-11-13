# Scala

- Spark is developed in Scala
- For building applications, better install IntelliJ IDE or Eclipse with Scala plugin
- For certification preparation, practice using REPL (Read, Evaluate,Print and Loop)

# Basic Programming Constructs
Using REPL, type scala and the command line interface for Scala
is named as REPL. Nothing more than that!!

For execute multi-line code type:

    :paste

and start to write your code ended by a Enter when you finish a line. To execute all lines do: Ctrl-D.

# val & var

- val z=0: it means that variable z can't receive other value. It is immutable

- var z=0: it means that variable z can receive other value. It is mutable.

# string functions

Imagine you type:

    s = "hello world"

To see that functions it is available to use for this string variable type s. and press tab to display a list of possible functions. To see the sintaxe of a functions press Tab after the
name of the function: s.length (press now Tab to see the sintaxe of length function).

# for loop with range

Simple type:

    for (i <- (1 to 100)){
      println(i)
    }

# Develop Functions

    def sum(lb: Int, ub: Int) = {
      var total = 0
      for(element <- lb to ub){
        total <- element
      }
      total
    }

## Pass a function as an argument

    def sum(func: Int => Int, lb:Int, ub:Int)={
      var total = 0
      for (element <- lb to ub){
        total += func(element)
      }
      total
    }

    def cube(i: Int) = i * i * i

    sum(cube, 1, 10)

    sum(i => i * i * i, 1, 10)

  Thus it is clear that func can be any function that gives an integer.  
