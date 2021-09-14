# Writing Data to Database

* Setup Postgres Database
* Create retail_db
* Create users table
* Populating sample data
* Reading data from table using Pandas
* Truncate users table
* Writing Pandas Dataframe to table
* Validating users data
* Dropping users table

## Setup Postgres Database

```shell
docker pull postgres
docker network create retail_nw

docker create \
  --name retail \
  --network retail_nw \
  -p 5452:5432 \
  -e POSTGRES_PASSWORD=itversity \
  postgres
  
docker start retail

docker exec -it retail psql -U postgres
```

## Create retail_db

```shell
docker exec -it retail psql -U postgres
```

```sql
CREATE DATABASE retail_db;
CREATE USER retail_user WITH ENCRYPTED PASSWORD 'itversity';
GRANT ALL ON DATABASE retail_db TO retail_user;
\q
```

## Create users table

```shell
docker exec -it retail psql -U retail_user -d retail_db
```

```sql
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    user_first_name VARCHAR(30),
    user_last_name VARCHAR(30)
);
```

## Populating Sample Data

```shell
docker exec -it retail psql -U retail_user -d retail_db
```

```sql
INSERT INTO users (user_first_name, user_last_name)
    VALUES ('Scott', 'Tiger'),
    ('Donald', 'Duck');

SELECT * FROM users;
```

## Reading data from table using Pandas

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

```shell
docker exec -it retail psql -U retail_user -d retail_db
```

```sql
TRUNCATE TABLE users;
\q
```

## Writing Pandas Dataframe to table

```python
users_list = [
    {'user_first_name': 'Scott', 'user_last_name': 'Tiger'},
    {'user_first_name': 'Donald', 'user_last_name': 'Duck'}
]

import pandas as pd
df = pd.DataFrame(users_list)

conn = 'postgresql://retail_user:itversity@localhost:5452/retail_db'
df.to_sql('users', conn, if_exists='append', index=False)
```

## Validating users data

```shell
docker exec -it retail psql -U retail_user -d retail_db
```

```sql
SELECT * FROM users;
\q
```

## Dropping users table

```shell
docker exec -it retail psql -U retail_user -d retail_db
```

```sql
DROP TABLE users;
\q
```
