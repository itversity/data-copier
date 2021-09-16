# Writing Data to Database

* Creating departments table
* Populating departments table
* Validate departments table
* Creating orders table
* Populating orders table
* Validate orders table in database
* Validate by querying orders table

## Creating departments table

```shell
docker exec -it retail psql -U retail_user -d retail_db -W
```

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50)
);
```

## Populating departments table

```python
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
```

## Creating orders table

```shell
docker exec -it retail psql -U retail_user -d retail_db -W
```

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE,
    order_customer_id INT,
    order_status VARCHAR(30)
);
```

## Populating orders table

```python
BASE_DIR = '/Users/itversity/Projects/Internal/bootcamp/data-copier/data/retail_db_json'
table_name = 'orders'

import os
file_name = os.listdir(f'{BASE_DIR}/{table_name}')[0]
fp = f'{BASE_DIR}/{table_name}/{file_name}'

import pandas as pd
json_reader = pd.read_json(fp, lines=True, chunksize=10000)

conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'

for df in json_reader:
    min_key = df['order_id'].min()
    max_key = df['order_id'].max()
    df.to_sql(table_name, conn, if_exists='append', index=False)
    print(f'Processed {table_name} with in the range of {min_key} and {max_key}')
```

## Validate orders table in database

```shell
docker exec -it retail psql -U retail_user -d retail_db -W
```

```sql
\d orders

SELECT * FROM orders LIMIT 10;

SELECT count(*) FROM orders;
```
## Validate by querying orders table

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
```
