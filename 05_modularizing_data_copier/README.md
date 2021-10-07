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
* Instead of hard coding such information we need to pass them as variables.
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

Let us validate the read logic that is developed as part of **read.py** program.
* We can validate either by using Pycharm Run option or using Python CLI.
* Here are the steps that are involved using Pycharm Run option.
  * Run the program and ignore any errors. It will create application under **Edit Configurations**.
  * Go to **Edit Configurations** and set required environment variables - `BASE_DIR` and `TABLE_NAME`.
  * Run the application and see the output.
* Here are the steps that are involved using Python CLI (under Terminal).
  * Launch Terminal within Pycharm.
  * Set environment variables. We can use `export` on Linux or Mac and `$env` on Windows to set the environment variables.
  * Run using `python app.py` and review the output.

## Write logic using Pandas

Let us develop the logic to write Dataframe into postgres database table as part of **write.py** program.
* Create program by name **write.py**.
* Here is the function to write the Dataframe to database table. It also contains required logic to test the functionality.

```python
def load_db_table(df, conn, table_name, key):
    min_key = df[key].min()
    max_key = df[key].max()
    df.to_sql(table_name, conn, if_exists='append', index=False)
    print(f'Loaded data for {table_name} with in the range of {min_key} and {max_key}')


if __name__ == '__main__':
    import pandas as pd
    import os

    data = [
        {'user_id': 1, 'user_first_name': 'Scott', 'user_last_name': 'Tiger'},
        {'user_id': 2, 'user_first_name': 'Donald', 'user_last_name': 'Duck'}
    ]
    df = pd.DataFrame(data)
    configs = dict(os.environ.items())
    conn = f'postgresql://{configs["DB_USER"]}:{configs["DB_PASS"]}@{configs["DB_HOST"]}:{configs["DB_PORT"]}/{configs["DB_NAME"]}'

    load_db_table(df, conn, 'users', 'user_id')
```

## Validate write logic

As we have developed the function to write Dataframe to Postgres database table along with test logic, it is time for us to validate.
* We can validate either by using Pycharm Run Configurations or Python command using CLI.
* Here are the steps using Pycharm Run Configurations.
  * Run the application to create configuration under run. Ignore any errors.
  * Set the required environment variables under run configuration of the application.
  * Run the application using Pycharm run option.
  * Review the output to confirm if the data is loaded successfully or not.
  * Connect to the database and validate data in the users table.
* Here are the steps using Python command as part of CLI.
  * Set required environment variables (on Windows we need to use `$env` and on Mac or Linux we need to use `export` command).
  * Run the program using `python write.py`.
  * Review the output to confirm if the data is loaded successfully or not.
  * Connect to the database and validate data in the users table. We should see duplicate records in the table as we loaded same data twice.

## Integrate read and write logic

As we are done with read and write logic, let us integrate both by using driver program.
* We can update **app.py** with the below logic.
```python
import os
from read import get_json_reader
from write import load_db_table


def process_table(BASE_DIR, conn, table_name):
    json_reader = get_json_reader(BASE_DIR, table_name)
    for df in json_reader:
        load_db_table(df, conn, table_name, df.columns[0])


def main():
    BASE_DIR = os.environ.get('BASE_DIR')
    table_name = os.environ.get('TABLE_NAME')

    configs = dict(os.environ.items())
    conn = f'postgresql://{configs["DB_USER"]}:{configs["DB_PASS"]}@{configs["DB_HOST"]}:{configs["DB_PORT"]}/{configs["DB_NAME"]}'
    process_table(BASE_DIR, conn, table_name)


if __name__ == '__main__':
    main()
```

## Validate Integration logic

As we are done with integration of read and write logic as part of driver program, let us go ahead and validate.
* As we have seen before you can either validate using Pycharm run with proper configurations or Python command using CLI.
* We just need to set all required environment variables and run the driver program.
* Also, we need to validate by running queries against the table by connecting to the database.
* Let us get into the validation using Pycharm Run using configurations.

## Develop logic for multiple tables

Let us go through the details of developing the logic to load the data from files into respective tables where we need to pass list of tables as argument.
* We will pass list of tables as part of comma separated string.
* For now we will pass the comma separated string as run time argument. It can be accessed using `sys` module.
* Once the list of tables is passed as comma separated string, we need to do the following:
  * Split the comma separated string into a list of tables.
  * Iterate through the list and invoke the logic to read from file and write to the table for each item in the list.
  * We will also modularize to make sure we have a function which will read and write for a table which is passed as argument.


## Validate logic for multiple tables

As our program to process multiple tables takes run time arguments, let us understand how to pass the run time arguments to validate.
* We can either use Pycharm run or Python command using CLI for the validation.
* Here is the process to pass the run time arguments using Pycharm.
  * Go to **Edit Configurations** and pass the argument as part of parameters.
  * Make sure all the environment variables defined.
  * Now we should be able to run the program which takes arguments using Pycharm.
  * Let us populate **departments** and **categories** using this approach and validate.
* Here is the process to pass the run time arguments using Python command as part of CLI.
  * Go to the Terminal or Powershell and make sure virtual environment is activated. We also need to make sure that we are in the working directory of the project.
  * If we use Pycharm integrated terminal, we will be in working directory as part of correct Python virtual environment.
  * Now we can pass the run time arguments to the program as part of `python` command after the program name.
  * Let us populate **products** using this approach and validate.