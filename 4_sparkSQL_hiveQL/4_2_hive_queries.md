# Spark SQL Commands

Launch spark-shell in YARN Cluster for instance:

  spark-shell --master yarn --conf spark.ui.port=12345

Check for Spark Context and sql

    // you need to define these two commands
    sc
    sqlContext

    // sqlContext.sql("Write Here Any SQL Command")
    // for instance
    sqlContext.sql("select * from orders limit 10").show

# Functions
To see functions connect to Hive and run:

    show functions;

To see the syntax that is used by a function run the command:

    // describe function FUNCTION;
    describe function length;

    // apply it to a column
    // display the records of a column and the total of characters of each word
    select order_status, length(order_status) from orders limit 100;

# String Functions

First we create a table only to have data to test the string function:

    create table customers (
      customer_id int,
      customer_fname varchar(45),
      customer_lname varchar(45),
      customer_email varchar(45),
      customer_password varchar(45),
      customer_street varchar(45),
      customer_city varchar(45),
      customer_state varchar(45),
      customer_zipcode varchar(45)
      ) row format delimiter fields terminated by ','
      stored as textfile;

Functions usually used with string data:

- substr or substring
- instr
- like
- rlike : regular expressions
- length
- lcase or lower: to put the word with lowercase letters
- ucase or upper: to put the word with uppercase letters
- trim, ltrim (left), rtrim (right)
- lpad, rpad
- initcap
- cast

## substr or substring

    describe function substr;

    // getting only 'How are you'
    select substr('Hello World, How are you', 14);

    // getting only 'World'
    select substr('Hello World, How are you', 7,5);

    // getting the last three characters : 'you'
    select substr('Hello World, How are you', -3);

    // getting the last seven characters  plus three: 'are'
    select substr('Hello World, How are you', -7, 3);

## instr

To see the first occurrence of a space

    // the result is 6
    select instr('Hello World, How are you', ' ');

To see the first occurrence of the word 'World':

    // the result is 7
    select instr('Hello World, How are you', 'World');

## like
Use % symbol to pass the correct word to _like_ command

    select "Hello World, How are yow" like 'Hello%';
    select "Hello World, How are yow" like '%World%';

## lpad
Put in the left side of the string a character that we specify.

    // string, number of total characters, character to complete
    // gives: 02
    select lpad(2, 2, '0')

    // gives: 000000000002
    select lpad(2, 12, '0')

## cast

Transform the data in other format  

    select cast("12" as int)

Our timestamp data is in format: yyyy-mm-dd hh:mm:ss.ms.
We want to extract the mouth and transform it in an integer.

    select cast(substr(order_date,6,2) as int) from orders

## split

Convert a string in a list.

    // ["Hello", "World,", "How", "are, "you"]
    select split("Hello World, How are you", " ");

    // get the first element of the list
    // Hello
    select index(split("Hello World, How are you", " "), 0);

## Timestamp manipulation Functions

These functions are usually used with timestamps.

- current_date
- current_time_stamp
- date_add
- date_format
- date_diff
- display
- dayofmonth
- to_date
- to_unix_timestamp
- to_utc_timestamp
- from_unixtime
- from_utc_timestamp
- minute
- month
- month_between
- next_day

      // get the year from the date
      select data_format(current_date, 'y');

      // get the day from the date
      select data_format(current_date, 'd');

      // get the day position on the year
      select data_format(current_date, 'D');

For more details search on Hive documentation for Data Retrieval Queries and
Operators and User Defined Functions (UDFs).

      // get the day of a timestamp
      select day('2017-10-09');

      // extract the date from a timestamp
      select to_date(current_timestamp);

      // convert to unix timestamp
      select to_unix_timestamp(current_date);

      // convert from unix timestamp to normal
      select from_unixtimestamp(unix_timestamp);

      // select date from a timestamp column
      select to_date(order_date) from orders limit 10;

      // select days in future
      select date_add(order_date, 10) from orders limit 10;


# Aggregation

      // get the number of records in a table
      select count(1) from orders;

This type of aggregations has limitations because when you are aggregate some
column value is makes groups using this column and don't allows you to use
other columns. The next will gives an error

      // gives an error
      select count(1) order_status from orders;

However, we can pass multiple aggregation functions. The next example is value because
both of them are similar aggregation functions and both of them have similar groups of records
and in this example takes counts the distinct values in all data.

      // get the number of orders and the distinct number of orders
      select count(1), count(distinct order_status) from orders;

# Case

    if(condition) 'x' else if (condition) 'y' else 'z'

But in SQL we don't have the _if_ condition. For this we have CASE that
do the same.

    // get the syntax of case command
    describe function case

    //get the distinct values for order_status column (categories)
    select distinct order_status from orders;

Imagine the business says that wants some actions for the cases that
we have CLOSED or COMPLETE order_status. In this situation we can use _case_ command.

    // do something based on business requirements
    select order_status,
           case order_status
                when 'CLOSED' then 'No Action'
                when 'COMPLETE' then 'No Action'
                when 'ON_HOLD' then 'Pending Action'
                when 'PAYMENT REVIEW' then 'Pending Action'
                when 'PENDING' then 'Pending Action'
                when 'PROCESSING' then 'Pending Action'
                when 'PENDING_PAYMENT' then 'Pending Action'
                else 'Risky'
          end from orders limit 10;

We can decrease the number of lines using boolean operators, _in_, _like_, etc.

    // do something based on business requirements
    select order_status,
       case
            when order_status IN('CLOSED', 'COMPLETE') then 'No Action'
            when order_status IN('ON_HOLD', 'PAYMENT REVIEW', 'PENDING', 'PROCESSING',  'PENDING_PAYMENT') then 'Pending Action'
            else 'Risky'
      end from orders limit 10;

To substitute the NULL values in a column we can use CASE or NVL command. The latter
allow us to do a more concise substitution.

    // using NVL to change NULL value for what you want
    select nvl(order_status, 'Status Missing') from orders limit 100;

    // using CASE to change NULL value for what you want
    select case when order_status is null then 'Status Missing' else order_status
        end from orders limit 100;

## Row level transformations

For data standardization. For instance we want to group our records by month.
Our timestamp column is in format yyyy-mm-dd hh:mm:ss:ms. We can solve this in several ways.
One way is to select the yyyy and  mm and concatenate them.
At the end we can transform in integer.

    // concatenate year with month
    select cast(concat(substr(order_date, 1,4),substr(order_date, 6,2))as int)
        from orders limit 10;

    // concatenate year with month
    select cast(date_format(order_date, 'YYYYMM') as int)
            from orders limit 10;

# Joins

Join orders and customers tables using order_customer_id and customer_id(PK).
Remember one customer can have many orders.

    // this is not a good idea
    select o.*, c.* from orders o, customers c,
        where o.order_customer_id == c.order_customer_id
        limit 10;

    // the appropriate way to join two tables
    select o.*, c.* from orders o join customers c
      on o.order_customer_id = c.customer_id
      limit 10;

    // the same using INNER JOIN
    select o.*, c.* from orders o inner join customers c
        on o.order_customer_id = c.customer_id
        limit 10;

**Left join** allow us to get all rows from the left table
and rows from right table that have the same key. The records that
don't have the same key they appears as NULL.

In this example we will select all rows from Customers table and the rows from
Orders table that share the same key with Customers table. In this case we can have
customers without orders. These records will appeared as NULL.

    // left join
    select o.*, c.* from orders c left outer join customers o
        on o.order_customer_id = c.customer_id
        limit 10;

We want the data that is in customers but that is not in orders. In this situation
we know that the NULL values corresponds to the cases that are only in Customers.

    // left join
    select * from orders c left outer join customers o
        on o.order_customer_id = c.customer_id
        where o.order_customer_id is null
        limit 10;

# Aggregations

The basic is count records of a table.

    // get the total of rows of a table
    select count(1) from orders;

    // get the number of records for each category
    select order_status, count(1) as total from orders group by order_status;

We want to get the revenue for each order

    //
    select o.order_id, o.order_date, o.order_status, sum(oi.order_item_subtotal) order_revenue
      from orders o join order_item oi
      on o.order_id = oi.order_item_order_id
    group by o.order_id, o.order_date, o.order_status;

    // revenue with more than 1000 for COMPLETE and CLOSE order_status
    select o.order_id, o.order_date, o.order_status, sum(oi.order_item_subtotal) order_revenue
      from orders o join order_item oi
      on o.order_id = oi.order_item_order_id
    where o.order_status in ('COMPLETE, 'CLOSED)
    group by o.order_id, o.order_date, o.order_status
    having sum(oi.order_item_subtotal) >= 1000;

    // revenue per day using round to get the numbers in a better format
    select o.order_date, round(sum(oi.order_item_subtotal), 2) order_revenue
    from orders o join order_item oi
    on o.order_id = oi.order_item_order_id
    where o.order_status in ('COMPLETE, 'CLOSED)
    group by o.order_date;

# Sorting: ORDER BY

Ordering records in ascendant or decedent order.

    // revenue with more than 1000 for COMPLETE and CLOSE order_status
    // ordering in order_date and order_revenue
    select o.order_id, o.order_date, o.order_status, round(sum(oi.order_item_subtotal), 2) order_revenue
      from orders o join order_item oi
      on o.order_id = oi.order_item_order_id
    where o.order_status in ('COMPLETE, 'CLOSED)
    group by o.order_id, o.order_date, o.order_status
    having sum(oi.order_item_subtotal) >= 1000
    order by o.order_date, order_revenue desc;

Using we use distribute the data is not order globally any more.
It allow us to get better performance because here we are getting revenue order
for each date and not globally. One thing is order by date and the other is
get that order in date.

    // revenue with more than 1000 for COMPLETE and CLOSE order_status
    // ordering in order_date and order_revenue
    // now the date is not order globally
    // it allow us to get better performance
    select o.order_id, o.order_date, o.order_status, round(sum(oi.order_item_subtotal), 2) order_revenue
      from orders o join order_item oi
      on o.order_id = oi.order_item_order_id
    where o.order_status in ('COMPLETE, 'CLOSED)
    group by o.order_id, o.order_date, o.order_status
    having sum(oi.order_item_subtotal) >= 1000
    distribute o.order_date order by o.order_date, order_revenue desc;

# Set Operations

Joins are typical done between two tables in common key.

Set operations are done in two dataset that has similar nature (structure).

- Union: eliminate duplicates and we get the distinct values.
- Union all: we add all records even if they are duplicates

      // get the union between the queries
      select 1, "Hello";
      union
      select 2, "World";
      union
      select 1, "Hello";
      union
      Select 1, "World";

# Analytical Functions (AF)

To find AF documentation follow the next steps
- google for: Hive Language Manual
- Data Retrieve Queries
- Windowing and Analytics

The next query allow us to obtain the percentage of revenue per order_id an the average revenue
per order_id. For This purpose we need to use  _over (partition by order_id)_ to inform the calculus made for each order_id.

    select * from (
      select o.order_id, o.order_date, o.order_status, oi.order_item_subtotal,  
        round(sum(oi.order_item_subtotal) over (partition by o.order_id), 2) order_revenue,
        oi.order_item_subtotal / round(sum(oi.order_item_subtotal) over (partition by o.order_id), 2) pct_revenue,
        round(avg(oi.order_item_subtotal) over (partition by o.order_id), 2) avg revenue
      from orders o join order_item oi
      on o.order_id = oi.order_item_order_id
      where o.order_status in ('COMPLETE, 'CLOSED)) q
    where order_revenue >= 1000
    order by o.order_date, order_revenue desc;

# Analytical Functions: Ranking

The objective is ranking the results by _order_item_subtotal_ . In the next query we can see the difference
between different raking functions.

    select * from (
      select o.order_id, o.order_date, o.order_status, oi.order_item_subtotal,  
        round(sum(oi.order_item_subtotal) over (partition by o.order_id), 2) order_revenue,
        oi.order_item_subtotal / round(sum(oi.order_item_subtotal) over (partition by o.order_id), 2) pct_revenue,
        round(avg(oi.order_item_subtotal) over (partition by o.order_id), 2) avg revenue
        rank() over (partition by o.order_id order by oi.order_item_subtotal desc) rnk_revenue,
        dense_rank() over (partition by o.order_id order by oi.order_item_subtotal desc) dense_rnk_revenue,
        percent_rank() over (partition by o.order_id order by oi.order_item_subtotal desc) pct_rnk_revenue,
        row_number() over (partition by o.order_id order by oi.order_item_subtotal desc) rn_orderby_revenue,
        row_number() over (partition by o.order_id) rn_revenue,        
      from orders o join order_item oi
      on o.order_id = oi.order_item_order_id
      where o.order_status in ('COMPLETE, 'CLOSED)) q
    where order_revenue >= 1000
    order by o.order_date, order_revenue desc, rnk_revenue;

# Windowing Functions

It is used for time-series data analysis.

- LEAD
- LAG

      select * from (
        select o.order_id, o.order_date, o.order_status, oi.order_item_subtotal,  
          round(sum(oi.order_item_subtotal) over (partition by o.order_id), 2) order_revenue,
          oi.order_item_subtotal / round(sum(oi.order_item_subtotal) over (partition by o.order_id), 2) pct_revenue,
          round(avg(oi.order_item_subtotal) over (partition by o.order_id), 2) avg revenue
          rank() over (partition by o.order_id order by oi.order_item_subtotal desc) rnk_revenue,
          dense_rank() over (partition by o.order_id order by oi.order_item_subtotal desc) dense_rnk_revenue,
          percent_rank() over (partition by o.order_id order by oi.order_item_subtotal desc) pct_rnk_revenue,
          row_number() over (partition by o.order_id order by oi.order_item_subtotal desc) rn_orderby_revenue,
          row_number() over (partition by o.order_id) rn_revenue,    
          lead(oi.order_item_subtotal) over(partition by o.order_id by oi.order_item_subtotal desc) lead_order_item_subtotal,  
          lag(oi.order_item_subtotal) over(partition by o.order_id by oi.order_item_subtotal desc) lag_order_item_subtotal,   
          first_value(oi.order_item_subtotal) over(partition by o.order_id by oi.order_item_subtotal desc) first_order_item_subtotal,   
          last_value(oi.order_item_subtotal) over(partition by o.order_id by oi.order_item_subtotal desc) last_order_item_subtotal    
        from orders o join order_item oi
        on o.order_id = oi.order_item_order_id
        where o.order_status in ('COMPLETE, 'CLOSED)) q
      where order_revenue >= 1000
      order by o.order_date, order_revenue desc, rnk_revenue;
