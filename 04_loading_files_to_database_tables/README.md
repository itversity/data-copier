# Loading Data from files to tables

As part of this section let us get into the details about loading data from files into database tables using Pandas.

* Populating departments table
* Validate departments table
* Populating orders table
* Validate orders table in database
* Validate orders table using Pandas

## Populating departments table

Let us verify if the departments table exists and then populate the table.

```python
import pandas as pd

conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'
pd.read_sql('SELECT * FROM departments', conn)

BASE_DIR = '/Users/itversity/Projects/Internal/bootcamp/data-copier/data/retail_db_json'
table_name = 'departments'

import os
file_name = os.listdir(f'{BASE_DIR}/{table_name}')[0]
fp = f'{BASE_DIR}/{table_name}/{file_name}'

import pandas as pd
df = pd.read_json(fp, lines=True)

conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'
df.to_sql(table_name, conn, if_exists='append', index=False)
```

## Validate departments table

As the table is populated, let us see if the data is now available in the table.

```python
import pandas as pd
query = 'SELECT * FROM departments'
conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'
df = pd.read_sql(
    query,
    conn
)

df

df.count()

pd.read_sql(
	'SELECT count(1) FROM departments',
	conn
)
```


## Populating orders table

Let us populate orders table in chunks. Here are the steps which we are going to follow.
* Read data from files using `read_json` with chunksize set to 1000. It will create an object of type **JSON Reader**.
* Create connection string using SQL Alchemy Syntax.
* Iterate through JSON Reader object. In each iteration we will get a Dataframe of specified chunksize.
* Load Dataframe related to each chunk into the target table.

```python
BASE_DIR = '/Users/itversity/Projects/Internal/bootcamp/data-copier/data/retail_db_json'
table_name = 'orders'

import os
file_name = os.listdir(f'{BASE_DIR}/{table_name}')[0]
fp = f'{BASE_DIR}/{table_name}/{file_name}'

import pandas as pd
json_reader = pd.read_json(fp, lines=True, chunksize=1000)

conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'

for df in json_reader:
    min_key = df['order_id'].min()
    max_key = df['order_id'].max()
    df.to_sql(table_name, conn, if_exists='append', index=False)
    print(f'Processed {table_name} with in the range of {min_key} and {max_key}')
```

## Validate orders table in database

As data is loaded into the table using Pandas, let us connect to the database and validate to see if the data is populated into the right table as per our expectations.

* Connecting to the database.

```shell
docker exec -it retail_pg psql -U retail_user -d retail_db -W
```

* Running queries to validate data in the table.
```sql
\d orders

SELECT * FROM orders LIMIT 10;

SELECT count(*) FROM orders;

SELECT order_status, count(1) FROM orders GROUP BY order_status;
```

## Validate orders table using Pandas

Let us validate by querying data from orders table using Pandas.
* Create dataframe for `SELECT * FROM orders`.
* Create dataframe for order count by status.
* If you are interested, you can come up with your own scenarios and run the queries using `read_sql`.

```python
import pandas as pd
query = 'SELECT * FROM orders'
conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'
df = pd.read_sql(
    query,
    conn
)

df

df.count()

df.dtypes

pd.read_sql(
    'SELECT order_status, count(1) AS order_count FROM orders GROUP BY order_status',
    conn
)
```
