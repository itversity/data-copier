# Reading Data

* Setup Retail Data
* Overview of Retail Data
* Adding Pandas to the project
* Reading Data using Pandas
* Previewing Data using Pandas
* Reading Data in Chunks
* Dynamically read files
* Create read program

## Setup Retail Data
Let us go ahead and setup Retail Data Set for the project.
* Create a folder by name **data** using `mkdir -p data` command.
* Go to **data** folder by running `cd data`.
* Clone the **retail** data set repository from GitHub by running the below command.

```
git clone https://github.com/dgadiraju/retail_db_json.git
```

* It will create a folder by name **retail_db_json**. The folder should contain 6 folders related to 6 tables.
* Each folder contain a **JSON** File.
* **JSON** is nothing but specialized text file format. Hence, we will be able to preview the data using any standard editor.
* Each line have one record and each attribute have it's name and value with in each record.

## Overview of Retail Data
Here sre the details related to retail data. It is important to understand the data to come up with a strategy to copy the data.
* The data is sourced from RDBMS database.
* In the source the tables are related.
* There are 6 tables, out of which 3 tables are related to product catalog.
  * **departments**
  * **categories**
  * **products**
  * **departments** is the root table, and it has **one to many** relationship with **categories** table.
  * **categories** is the parent table to **products**. We have product level details in **products** table. 
* One of the table is to store customer data. It is **customers**
* **orders** and **order_items** are the transactional tables.
  * **orders** contain order level details such as **order_date**, **order_customer_id**, **order_status**, etc.
  * Each order can have multiple items. All item level details are part of **order_items** table.
  * There is a one-to-many relationship between **orders** and **order_items**.
* For each table, we have a folder and the folder contains one file. The file is of type json.

## Adding Pandas to the project
We will be using Pandas to read the data from CSV files. Let us see how we can install and quickly validate.
* Update **requirements.txt** with Pandas 1.3.2.
```
psycopg2-binary==2.8.5
SQLAlchemy==1.3.17
pandas==1.3.2
```
* Run `pip install -r requirements.txt` to ensure Pandas is installed as part of our project.
* We can quickly validate by running this piece of code. This code will convert list of dicts to Pandas Data Frame.

```python
import pandas as pd
users = [
    {'user_id': 1, 'user_name': 'Scott, Tiger'},
    {'user_id': 2, 'user_name': 'Donald, Duck'}
]
users_df = pd.DataFrame(users)
users_df
```

## Reading Data using Pandas
Let us understand how to read our Retail data using Pandas. The files which contain data related to retail are of type **JSON**.
* **Pandas** provides functions to read the data in JSON Files.
* We can use `read_json` by passing path to it to convert the JSON data in files into **Pandas Dataframe**.
* Here is the sample code to read **order_items** data into Pandas Dataframe.

```python
# Make sure fp is pointing to the file name which you are trying to read
fp = '/Users/itversity/Projects/Internal/bootcamp/data-copier/data/retail_db_json/order_items/part-r-00000-6b83977e-3f20-404b-9b5f-29376ab1419e'

import pandas as pd
df = pd.read_json(fp, lines=True)
df.shape()
```
* You can also try reading data related to other tables as an exercise.

## Previewing Data using Pandas
Let us go through how we can use **Pandas** APIs to preview the data in Dataframe.
* We will perform following tasks.
  * Get not null count of each attribute
  * Get the names and data types of each attribute
  * Project some of the fields in the Dataframe
  * Filter the data for a give order_item_order_id
  * Convert dataframe to list of tuples

```python
fp = '/Users/itversity/Projects/Internal/bootcamp/data-copier/data/retail_db_json/order_items/part-r-00000-6b83977e-3f20-404b-9b5f-29376ab1419e'

import pandas as pd
df = pd.read_json(fp, lines=True)

df.count()
df.describe()

df.columns
df.dtypes

df[['order_item_order_id', 'order_item_subtotal']]

df[df['order_item_order_id'] == 2]


```

## Reading Data in Chunks using Pandas
When size of data is huge, it is recommended to read the data in chunks, then process and then to write to the target. Let us understand how to read the data from JSON files using Pandas in chunks.

```python
fp = '/Users/itversity/Projects/Internal/bootcamp/data-copier/data/retail_db_json/order_items/part-r-00000-6b83977e-3f20-404b-9b5f-29376ab1419e'

import pandas as pd

json_reader = pd.read_json(fp, lines=True, chunksize=1000)

for idx, df in enumerate(json_reader):
  print(f'Number of records in chunk with index {idx} is {df.shape[0]}')
```

## Dynamically read files

```python
import os
BASE_DIR = '/Users/itversity/Projects/Internal/bootcamp/data-copier/data/retail_db_json'
table_name = 'order_items'
file_name = os.listdir(f'{BASE_DIR}/{table_name}')[0]
fp = f'{BASE_DIR}/{table_name}/{file_name}'

import pandas as pd
df = pd.read_json(fp, lines=True)

df.shape
df.dtypes
```

## Create read program

```python

```
