# Reading Data
As part of this section we will primarily see how to read the JSON data from files using Pandas.

* Overview of Retail Data
* Adding Pandas to the project
* Reading Data using Pandas
* Previewing Data using Pandas
* Reading Data in Chunks
* Dynamically read files
* Create read program

## Overview of Retail Data
Here are the details related to retail data. It is important to understand the data to come up with a strategy to copy the data.
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
* If there are foreign key relationships to tables in the target database, then we need to populate root or parent tables first and then go down in the hierarchy. Another common way is to disable the constraints, populate data in tables in any order and then enable the constraints back.
* As our tables does not have constraints to enforce relationships we can load them in any order.

## Adding Pandas to the project
We will be using Pandas to read the data from JSON files. Let us see how we can install and quickly validate.
* Update **requirements.txt** with Pandas 1.3.2.
```
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
  * Filter the data for a given order_item_order_id

```python

# The file name is hardcoded and assigned to fp.
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

# Here is the piece of code to read the content of the file as reader.
json_reader = pd.read_json(fp, lines=True, chunksize=1000)

# Here is the piece of code to read each chunk as Dataframe.
for idx, df in enumerate(json_reader):
  print(f'Number of records in chunk with index {idx} is {df.shape[0]}')
```

## Dynamically read files
Let us understand how to read the files dynamically based on the table name which we are interested in. For now we will create variable for the table name and use it to dynamically load the files that belong to the table name.

```python
import os
BASE_DIR = '/Users/itversity/Projects/Internal/bootcamp/data-copier/data/retail_db_json'
table_name = 'order_items'

# Lists all the files in the folder. 
# All our folders contain only one file.
# Hence, we can access it by reading first element in the list as below.

file_name = os.listdir(f'{BASE_DIR}/{table_name}')[0]
fp = f'{BASE_DIR}/{table_name}/{file_name}'

# Here is the piece of code to read the content of the file as Pandas Dataframe.
import pandas as pd
df = pd.read_json(fp, lines=True)

df.shape
df.dtypes
```

## Create read program

```python

```
