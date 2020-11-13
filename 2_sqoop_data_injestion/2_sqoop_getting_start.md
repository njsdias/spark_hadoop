# Sqoop Getting Start

- connect string
- giving the password
- listing databases
- listing tables

# Connect string
- Go to Sqoop User Guide
- Inside of sqoop-import select Syntax
- on the table you see the option: --connect <jdbc-uri> : Specify JDBC connect string

The JAR file connector to MySQL RDBMS is located at

    usr/hdp/current/sqoop-client/lib

Doing

    ls -ltr
    mysql-conector-java-jar -> /usr/share/java/mysql-connector-java.jar

To connect to a Database Server, for instance:

    sqoop import --connect jdbc:mysql://database.example.com/employees

# Using sqoop help through CLI and see Database information

    // to see the commands available
    sqoop help

    // to see the help for list-databases command
    sqoop help list-databases

    // to list databases available
    sqoop list-databases\
      --connect jdbc:mysql://ms.itversity.com:3306 \
      --username retail_user \   
      --password-file name_file

Note: In this example, Mysql instance is running in 3306 port

    // to list the tables that are inside of databases
    sqoop list-databases\
      --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
      --username retail_user \
      --password-file name_file

# Run queries using ECAL sqoop command

We can use EVAL to run queries on remote databases. To see example how you can use this
command go to sqoop documentation and search for eval. For having more concise
information type CLI the command help:

    sqoop help eval

    // running SQL SELECT queries using EVAL sqoop command
    sqoop eval\
      --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
      --username retail_user \
      --password-file name_file \
      --query "SELECT * FROM orders LIMIT 10"

      // running SQL INSERT using EVAL sqoop command
      // the user need to have insert permission to be successful in this command
      sqoop eval\
        --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
        --username retail_user \
        --password-file name_file \
        --query "INSERT INTO orders VALUES (100000, \"20137-10-31 00:00:00.0\", 100000, \"DUMMY\")"

Note: We can use simple quotes:

        --query "INSERT INTO orders VALUES (100000, '20137-10-31 00:00:00.0', 100000, 'DUMMY

To create table and insert values:

      // running SQL CREATE TABLE query using EVAL sqoop command
      // the user need to have insert permission to be successful in this command
      sqoop eval\
        --connect jdbc:mysql://ms.itversity.com:3306/retail_export \
        --username retail_user \
        --password-file name_file \
        --query "CREATE TABLE dummy (i INT)"

      // running SQL INSERT VALUES query using EVAL sqoop command
      // the user need to have insert permission to be successful in this command
      sqoop eval\
        --connect jdbc:mysql://ms.itversity.com:3306/retail_export \
        --username retail_user \
        --password-file name_file \
        --query "INSERT INTO dummy VALUES (1)"
