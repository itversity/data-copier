# Modularizing Data Copier

As part of this section we will see how to modularize the code so that it is easily manageable.

* Overview of main function
* Overview of Environment Variables
* Using os module for Environment Variables
* Passing Environment Variables using Pycharm
* Read logic using Pandas
* Validate read logic
* Write logic using Pandas
* Validate write logic
* Create Driver Program
* Validate Driver Program
* Validate Application using Pycharm
* Validate Application using Terminal
* Passing Table List as argument
* Passing Table List using file

## Overview of main function

Let us get an overview of main function in Python.
* If you develop code in Python script without making it part of any function, then such code will execute whenever we try to import the program to access functions in it.
* The code which is not part of any function will also run when we try to run directly.
* Create the program by name **read.py** and paste the below code.

```python
print('Hello from read')


def dummy():
    print('Hello from dummy of read')
```

* Create the program by name **app.py** and paste the below code.

```python
from read import dummy


def main():
    print('Hello World from itversity')
    dummy()
    

if __name__ == "__main__":
    main()
```

* Now run both **read.py** and **app.py**. When we run **app.py**, we see output of both the print statements.
* Generally we want to see the results of first print statement only when we run **read.py** directly, but not when we import in other programs.
* Also, Python have a special variable by name `__name__`. When we run any Python program, the value of `__name__` is set to `__main__`.
* We can leverage it to make sure that the certain code can run when we run the program, but not when we import it as part of another program.
* Now update **read.py** with below code.

```python
def dummy():
    print('Hello from dummy of read')


if __name__ == '__main__':
    print('Hello from read')
```

* Now if you run **read.py** you will see the results of print statement in main and when you run **app.py**, that piece of code will not be executed.

## Overview of Environment Variables

Let us get an overview of environment variables. 
* We typically develop applications using Pycharm on PC or Mac.
* Once the development is done we will deploy them in QA/UAT and then in Production.
* There will be several components such as databases which we need to connect our application to.
* For each of the environments such as Dev, QA/UAT, Production, etc the connectivity information will differ. However, the core logic remain same.
* Instead of hard coding such information we need to pass them as environment variables.
* One of the way to pass the such information is to pass them as environment variables.
* In Mac or Linux, we can pass environment variables using `export` command. For example, `export DB_NAME=retail_db`.
* On Windows, we can pass environment variables using `$env:DB_NAME = "retail_db"`.

## Using os module for Environment Variables

Let us understand how we can leverage `os` module to access environment variables.

* Make sure to set the environment variable.

```shell
# On Mac or Linux
export DB_NAME=retail_db

# On Windows
$env:DB_NAME = "retail_db"
```
* Once environment variables are set, we should be able to launch Python CLI and import `os` module.
* We can get the value of **DB_NAME** by passing it to `os.environ.get`.
* We can also access all the environment variables by saying `os.environ.items()`. We can convert the returned object to dict and then access the value by passing the name of the environment variable.

```python
import os
db_name = os.environ.get('DB_NAME')

configs = dict(os.environ.items())
configs['DB_NAME']
```

## Passing Environment Variables using Pycharm

Let us understand how we can pass environment variables using Pycharm. We will use below logic as part of `app.py` to understand how to access environment variables using Pycharm

```python
import os


def main():
    DB_NAME = os.environ.get('DB_NAME')
    print(f'Hello World from {DB_NAME}')
```

* Now we can run the application and then go to **Run -> "Edit Configurations"**.
* We can set the environment variables as part of the popup as demonstrated.
* We can also validate using Terminal by setting the environment variable and then by running the program using `python` command.

## Read logic using Pandas

Let us add read logic using pandas to our application.
* We will create a program by name **read.py** with the below logic.

```python
import os
import pandas as pd


def get_json_reader(BASE_DIR, table_name, chunksize=1000):
    file_name = os.listdir(f'{BASE_DIR}/{table_name}')[0]
    fp = f'{BASE_DIR}/{table_name}/{file_name}'
    return pd.read_json(fp, lines=True, chunksize=chunksize)


if __name__ == '__main__':
    # This code will only run when we run the program.
    # When we import, the program will not run.
    BASE_DIR = os.environ.get('BASE_DIR')
    table_name = os.environ.get('TABLE_NAME')
    json_reader = get_json_reader(BASE_DIR, table_name)
    for idx, df in enumerate(json_reader):
        print(f'Number of records in chunk with index {idx} is {df.shape[0]}')
```

## Validate read logic


## Write logic using Pandas


## Validate write logic
## Create Driver Program
## Validate Driver Program
## Passing Environment Variables using Pycharm
## Validate Application using Pycharm
## Validate Application using Terminal
## Passing Table List as argument
## Passing Table List using file
