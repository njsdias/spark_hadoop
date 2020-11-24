# Create Database and Tables - Text File Format

Google it
- Hive Language Manual and search for DDL Statements
to see the hive available commands.

Enter in the cluster and run spark-sql inside of it. For instance:

    spark-sql --master yarn --conf spark.ui.port=12867

But we can use spark-shell, pyspark or spark-sql. Inside of spark shell we can
run SQL commands.

    show databases;

For test our SQL queries we can use HIVE that is the SQL for Hadoop ecosystem.
When we use Hive or Spark-SQL the query will be comply in two some execution frameworks
- Hive: it will be compile into map-reduce framework
- spark: uses spark framework

To be comply it needs to read metadata from somewhere.

   hive metastore

Learning SQL using Hive allow us to transfer this knowledge to other Big Data technologies:
- spark-SQL
- presto
- impala

Assuming you are using hive context. Create, enter in database and show the tables that database has.

    create database nameOfDatabase;
    use nameOfDataBase;
    show tables;

Inside of Hive context we can run HDFS commands using the *dfs* command. For instance,
to list the databases created in warehouse we can run the next command inside of hive context:

    dfs -ls /apps/hive/warehouse/

## Create Tables

*Attention*: In Hive we don't have primary key and foreign key and indexes.

    create table orders(
      order_id int,
      order_data string,
      order_customer_id int,
      order_status string
    ) row format delimited fields terminated by ","
    stored as textfile;

## Load data to table

Google it
- Hive Language Manual and search for DML Statements
to see the load command specifications.

Here is showing the case when data is stored in local. But if you have data in
HDFS you need to change *local* and the use the appropriated location where is
the data inside of HDFS.

      load data local inpath "/foldername/orders" into table orders;

To see the first items records.

      select * from orders limit 10;

To see how the data is stored inside of database

      dfs -ls /apps/hive/warehouse/nameOfDatabase/orders;

To create the orders_item table

    create table order_items(
      order_item_id int,
      order_item_order_id int,
      order_item_product_id int,
      order_item_quantity int,
      order_item_subtotal float,
      order_item_product_price float
    ) row format delimited fields terminated by ","
    stored as textfile;

    load data local inpath "/foldername/order_items" into table orders_items;

    select * from order_items limit 10;

# Create Database and tables in ORC File Format

    create databse database_name;
    use database_name;

To get the sintaxe we can search in LanguageManual DLL and search for Create Table.
In case to create in ORC foramt we don't need specify row delimiter fields.


    create table orders(
      order_id int,
      order_data string,
      order_customer_id int,
      order_status string
    ) stored as orc;




    create table order_items(
      order_item_id int,
      order_item_order_id int,
      order_item_product_id int,
      order_item_quantity int,
      order_item_subtotal float,
      order_item_product_price float
    ) stored as orc;

    describe orders;

    //to see more details
    describe formatted orders;

To see the files related with databases. In this case it will be empty because we didn't yet load any data to tables.

    dfs -ls hdfs://cluster_address//database_name/orders;

To insert values we can use data stored in the table that is in  nameOfDatabase_txt database and the insert command will be transform this data in ORC format

    insert into table orders select * from nameOfDatabase_txt.orders;
    insert into table order_items select * from nameOfDatabase_txt.order_items;

    // to see information
    select * from orders limit 10;
    select * from order_items limit 10;
