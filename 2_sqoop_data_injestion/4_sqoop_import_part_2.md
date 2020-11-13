# Sqoop: Importing Data

VIDEO 39

- Number of mappers
- Splitting using custom columns
- Splitting using non numeric column


# Number of mappers

The default number is four.

If the table is not have a primary key it needs to pick one of the keys to
evaluate the table size using MIN and MAX sql functions.
