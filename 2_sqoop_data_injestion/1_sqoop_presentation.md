# Sqoop: Data Injestion

The skills to transfer data between external systems and your cluster. This include the following:

- Import data from a MySQL database into HDFS using Sqoop
- Export data to a MySQL database from HDFS using Sqoop
- Change the delimiter and file format of data during import using Sqoop
- Ingest real-time and near-real-time streaming data into HDFS
- process streaming data as it is loaded onto the cluster
- Load data into and out of HDFS using the Hadoop File System commands

# Sqoop: Documentation

Google for: sqoop user guide 1.4.6. Keywords:

- sqoop-import
- sqoop-export
- sqoop-eval
- sqoop-list-databases
- sqoop-list-tables
- sqoop-help

Log to cluster that is the sqoop installed. To get help for sqoop-eval:

    sqoop help eval


Search for additional content to setup the environment in

    www.youtube.com/itversityin

Search for "Hadoop Certification - HDPCD- Setup retail_db database"
to access the database.

Enter into cluster (**ssh host_name**). Inside of cluster to see the sqoop version:
    sqoop version

To connect mysql database:

    // -u: to use the database ; -h: to specify the hostname; -p: to ask the password
    mysql -u retail_user -h hostname -p

After that we can run every sql commands:

    show databases;
    use retail_db;
    show tables;
    select * from orders limit 10;
    exit
