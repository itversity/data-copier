# Database Programming using Pandas
As part of this section, we will see how to write data to Postgres Database Tables using Pandas. Here are the topics which we are going to cover.

* Validate Postgres Setup using Docker
* Add required dependencies
* Create users table in retail_db Database
* Populate sample data into users table
* Reading data from table using Pandas
* Truncate users table
* Writing Pandas Dataframe to table
* Validating users data
* Dropping users table

## Validate Postgres Setup using Docker

Let us make sure Postgres database is setup.

```shell
docker ps -a # to list all the containers. We can check status to see if Postgres container is running.

# Run the below command to start the postgres container if it is stopped
docker start retail_pg

# Validate connectivity to the database in Postgres Server
docker exec -it retail_pg psql -U retail_user -d retail_db -W
```

We can also run below commands to validate the tables in the database.

```sql
\d

\q
```

## Add required dependencies

Let us add required dependencies to connect to Postgres database and then perform read as well as write operations against Postgres database tables using Pandas.
* `psycopg2` - Database driver to connect to Postgres Database.
* `SQLAlchemy` - ORM Wrapper on top of `psycopg2` and other database specific drivers.
* Pandas Database related functions uses SQL Alchemy and hence we need to install SQL Alchemy as well.

Here is the content of **requirements.txt** file.

```text
psycopg2-binary==2.8.5
SQLAlchemy==1.3.17
pandas==1.3.2
```

## Create users table in retail_db Database

Let us go ahead and create a new table in retail_db database. We will use this new table to understand how to use Pandas to read the data from database tables as well as to write data to database tables.

```shell
docker exec -it retail_pg psql -U retail_user -d retail_db -W
```

```sql
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    user_first_name VARCHAR(30),
    user_last_name VARCHAR(30)
);
```

## Populating Sample Data into users table

Let us connect to the postgres database retail_db and populate the table using `INSERT` command. We will insert multiple records using one `INSERT` statement.

* Connect to the database

```shell
docker exec -it retail_pg psql -U retail_user -d retail_db -W
```

* Insert data into the table

```sql
INSERT INTO users (user_first_name, user_last_name)
    VALUES ('Scott', 'Tiger'),
    ('Donald', 'Duck');

SELECT * FROM users;
```

## Reading data from table using Pandas

As we have inserted couple of records into the table let us understand how to read data from Postgres Database table using Pandas.

* We can use Pandas `read_sql` to read the data from database table by passing query string and connection string as arguments.
* It will result in a Dataframe.
```python
import pandas as pd
query = 'SELECT * FROM users'
conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'
df = pd.read_sql(
    query,
    conn
)

df

df.count()
```

## Truncate users table

As we would like to populate the table using Python Pandas let's truncate the data which is currently populated in the table. We can use `TRUNCATE` statement to clean up the table completely.

* Connect to the database.

```shell
docker exec -it retail psql -U retail_user -d retail_db
```

* Truncate the table using `TRUNCATE` Statement. `TRUNCATE` is DDL Statement.

```sql
TRUNCATE TABLE users;
\q
```

## Writing Pandas Dataframe to table

As our table is empty, now let's populate the table using Pandas. We will validate using Pandas in this lecture and we will also validate by running SQL by connecting to the database in next lecture.

Here are the steps we need to follow to populate a Dataframe into a table.
* Create list of dicts
* Create Dataframe using list of dicts
* Create a string type object which contains valid SQL Alchemy string to connect to Postgres Database.
* Write the Dataframe into the table usint `to_sql` by passing table name and connection string as arguments. If the table is pre-created we need to use key word argument `if_exists`. Also, as we would like to populate **user_id** using sequence, we need to disable index of Pandas Dataframe.

```python
users_list = [
    {'user_first_name': 'Scott', 'user_last_name': 'Tiger'},
    {'user_first_name': 'Donald', 'user_last_name': 'Duck'}
]

import pandas as pd
df = pd.DataFrame(users_list)

conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'
df.to_sql('users', conn, if_exists='append', index=False)

pd.read_sql('SELECT * FROM users', conn)
```

## Validating users data

Let us also validate by running query by connecting to the database to confirm data is populated in the correct database table.

```shell
docker exec -it retail_pg psql -U retail_user -d retail_db -W
```

```sql
SELECT * FROM users;
\q
```

## Dropping users table

As we have gone through the steps related to reading from database table as well as writing to database table using Pandas, let us clean up the table by dropping it.

```shell
docker exec -it retail_pg psql -U retail_user -d retail_db -W
```

```sql
DROP TABLE users;
\q
```
