---
layout: post
title:  "DATABASE: PostgreSQL Commands"
categories: Database
---
# PostgreSQL Basic Commands
You can always check the [manual](https://www.postgresql.org/docs/12/index.html) and its useful [Appendixes](https://www.postgresql.org/docs/12/appendixes.html) section.
* ## versioning: [article](https://gorails.com/guides/upgrading-postgresql-version-on-ubuntu-server)
    ```bash
    # in Ubuntu: check currently installed versions:
    $ dpkg --get-selections | grep postgres
    ```
* ## [PostgreSQL packages for Debian and Ubuntu](https://wiki.postgresql.org/wiki/Apt)
    Quickstart and News, how to install the latest packages of your distribution
* ## prompt
    ```bash
    # on mac
    $ psql db_name
    db_name=>
    ```
* ## In Ubuntu, commands are a little bit different, [check here: PostgreSQL manual in Ubuntu](https://help.ubuntu.com/community/PostgreSQL)
    ```bash
    $ sudo -u postgres psql db_name
    db_name=#
    # 'sudo -u postgres' <-- means command on behalf of superuser 'postgres'

    # you can also switch to superuser like this:
    $ sudo su - postgres
    $ psql
    postgres=#
    ```
* ## quick command in Ubuntu
    ```bash
    $ sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'postgres';"
    $ sudo -u postgres psql -c "CREATE DATABASE testdb;"
    ```
* ## start and stop postgresql service
    ```bash
    $ sudo service postgresql start
    
    $ sudo service postgresql stop
    ```
* ## [createdb](https://www.postgresql.org/docs/12/app-createdb.html) and [dropdb](https://www.postgresql.org/docs/12/app-dropdb.html) 
    ```bash
    $ createdb db_name
    # if db_name is not specified, system user name will be used as the db_name

    $ dropdb mydb
    # a db_name has to be specified
    ```

* ## psql - [PostgreSQL interactive terminal commands](https://www.postgresql.org/docs/12/app-psql.html):
    ```sql
    mydb=> SELECT version();
    mydb=> SELECT current_date;
    mydb=> \h
    mydb=> \q
    ```
* ## create table columns (header)
    ```sql
    mydb=>
    CREATE TABLE weather (
        city            varchar(80),
        temp_lo         int,           -- low temperature
        temp_hi         int,           -- high temperature
        prcp            real,          -- precipitation
        date            date
    );
    ## -- comment, code after this won't be executed

    mydb=>
    CREATE TABLE cities (
        name            varchar(80),  -- this is a column
        location        point         -- another column
    );
    ```
* ## delete a table
    ```sql
    mydb=> DROP TABLE tablename;
    ```
* ## insert rows  
    after the columns are defined you can `INSERT` data in rows:
    ```sql
    mydb=>
    INSERT INTO weather VALUES ('San Francisco', 46, 50, 0.25, '1994-11-27');

    INSERT INTO cities VALUES ('San Francisco', '(-194.0, 53.0)')
    ```
    If you do not remember the order of the column:
    ```sql
    mydb=>
    INSERT INTO weather (city, temp_lo, temp_hi, prcp, date)
    VALUES ('San Francisco', 43, 57, 0.0, '1994-11-29');

    INSERT INTO weather (date, city, temp_hi, temp_lo)
    VALUES ('1994-11-29', 'Hayward', 54, 37);
    ```
* ## [COPY](https://www.postgresql.org/docs/12/sql-copy.html)  
    ```sql
    mydb=>
    COPY weather FROM '/home/user/weather.txt';
    ```
* ## [Querying a Table](https://www.postgresql.org/docs/12/tutorial-select.html)
    * ### to retrieve all the rows of table weather
    ```sql
    # option 1
    SELECT * FROM weather;
    # option 2
    SELECT city, temp_lo, temp_hi, prcp, date FROM weather;
    # exactly the same output
    ```
    | city      | temp_lo | temp_hi | prcp |    date|
    |------------|---------|---------|-------|-------------|
    |San Francisco |      46 |      50 | 0.25 | 1994-11-27|
    |San Francisco |      43 |      57 |    0 | 1994-11-29|
    |Hayward       |      37 |      54 |      | 1994-11-29|

    * ### flexible and editable
    ```sql
    SELECT city, (temp_hi+temp_lo)/2 AS temp_avg, date FROM weather;
       city        | temp_avg |    date
    ---------------+----------+------------
    San Francisco  |       48 | 1994-11-27
    San Francisco  |       50 | 1994-11-29
    Hayward        |       45 | 1994-11-29
    ```
    * ### be selective / filtering  
    Retrieve the weather of San Francisco on rainy days
    ```sql
    SELECT * FROM weather
        WHERE city = 'San Francisco' AND prcp > 0.0;
        # AND, OR, NOT can be used as Boolean operators
    ```
    * ### ordering
    ```sql
    SELECT * FROM weather
        ORDER BY city;
        

            city      | temp_lo | temp_hi | prcp |    date
        ---------------+---------+---------+------+------------
        Hayward       |      37 |      54 |      | 1994-11-29
        San Francisco |      43 |      57 |    0 | 1994-11-29
        San Francisco |      46 |      50 | 0.25 | 1994-11-27
    ```
    * ### be DISTINCT
    ```sql
    SELECT DISTINCT city
        FROM weather;

        city
    ---------------
    Hayward
    San Francisco
    (2 rows)
    ```
    * ### be DISTINCE in ORDER
    ```sql
    SELECT DISTINCT city
        FROM weather
        ORDER BY city;
    ```

* ## UPDATE  
    Suppose you discover the temperature readings are all off by 2 degrees after November 28. You can correct the data as follows:
    ```sql
    UPDATE weather
        SET temp_hi = temp_hi - 2, temp_lo = temp_lo - 2
        WHERE date > '1994-11-28';
    ```

* ## DELETE  
    Delete rows whoes data matches value preceded by WHERE:
    ```sql
    DELETE FROM weather WHERE city = 'Hayward';
    ```
    ### ***Note***: The system will not request confirmation before command like this:
    ```sql
    DELETE FROM tablename;
    ```
    All rows from the tabel will be removed, leaving an empty table.