# Getting Started
As part of this module we will set up the development environment to develop an application to copy data from JSON Files to a target database (Postgres).

* Problem Statement - Data Copier
* Setup Docker
* Quick Overview of Docker
* Prepare Dataset
* Setup Postgres Database
* Overview of Postgres
* Setup Project using PyCharm
* Managing Dependencies
* Create GitHub Repository

## Problem Statement - Data Copier
Let us go through the problem statement for our project. We would like to develop the code to copy data which are in files using JSON Format to Postgres Database.

Data is available in another repository called as [retail_db_json](https://github.com/itversity/retail_db_json). We will see how to setup the repository later. But we will review the data now.

## Setup Docker
Let us understand different options to setup Docker using different environments. We will also see how to set up Docker on Cloud9
* If your Mac or PC have 16 GB RAM and Quad Core, I would recommend to setup Docker Desktop. Just Google and Set it up.
* For Windows, there might be some restrictions on Windows 10 Home and older versions.
* If you do not want to install locally, you can setup the whole development environment using AWS Cloud9. Make sure to choose Ubuntu as OS while setting up AWS Cloud9.

## Quick Overview of Docker
Docker is one of the key technology to learn. Let us quickly review some of the key concepts related to Docker.
* Overview of Docker Images
* Managing Docker Images
* Overview of Docker Containers
* Starting Containers using Images
* Managing Docker Containers
* Usage and Characteristics of Docker Containers
  * Images are reusable
  * Containers are ephemeral (stateless)
  * Production Databases should not be running on Docker Containers
  * Production Applications are typically deployed using Docker Containers

## Prepare Dataset
Let us prepare dataset to play around. Dataset is part of GitHub Repository.
* The data set is called as **retail_db**. It is a hypothetical data set provided by Cloudera as part of Cloudera QuickStart VM.
* Clone **retail_db** repository from GitHub. We will use this repository to setup tables and load data as part of the database.
```shell script
git clone https://www.github.com/itversity/retail_db_json.git
```
* It has some scripts as well as json separated files as well.
* As part of this usecase we will use scripts which will create tables as well as load data sets.
* **create_db_tables_pg.sql** is the script which will facilitate us to create tables alone. It will not load data into the tables.

## Setup Postgres Database
Let us setup a database using Postgres as part of Docker Container.
* Pull image `docker pull postgres`
* Create folder for Postgres Database
```shell script
mkdir retail_pg
```
* Create and start container using `docker run`
```shell script
docker run \
  --name retail_pg \
  -e POSTGRES_PASSWORD=itversity \
  -d \
  -v `pwd`/data/retail_db_json:/data/retail_db_json \
  -v `pwd`/retail_pg:/var/lib/postgresql/data \
  -p 5452:5432 \
  postgres

# Windows using Powershell
docker run --name retail_pg -e POSTGRES_PASSWORD=itversity -d -v C:\Users\dgadiraju\Projects\data-copier\retail_pg:/var/lib/postgresql/data -v C:\Users\dgadiraju\Projects\retail_db_json:/retail_db_json -p 5452:5432 postgres
```
* We can review the logs by using `docker logs -f pg_retail_db` command.
* Make sure retail_db is either mounted or copied on to the Docker Container.
* Connect to Postgres database using `docker exec`
```shell script
docker exec \
  -it retail_pg \
  psql -U postgres

# On Windows
docker exec -it retail_pg psql -U postgres
```
* Create Database and User as part of Postgres running in Docker
```sql
CREATE DATABASE retail_db;
CREATE USER retail_user WITH ENCRYPTED PASSWORD 'itversity';
GRANT ALL PRIVILEGES ON DATABASE retail_db TO retail_user;
```
* Run **/retail_db_json/create_db_tables_pg.sql** to create tables using Postgres CLI.
```shell script
docker exec \
  -it retail_pg \
  psql -U retail_user \
  -d retail_db  \
  -f /data/retail_db/create_db_tables_pg.sql

# On Windows
docker exec -it retail_pg psql -U retail_user -d retail_db -f /retail_db_json/create_db_tables_pg.sql
```
## Overview of Postgres
Let us get a quick overview of Postgres Database.
* Postgres is multi tenant database server. It means there can be multiple databases per server.
* We typically create databases and users then grant different types of permissions for different users.
* Here are the details about our database:
  * Database Name: **retail_db**
  * Database User: **retail_user**
  * Permissions: **ALL** (DDL, DML, Queries)
* Login to the system or Docker container where Postgres is running. In my case I am connecting to Docker container.
```shell script
docker exec \
  -it retail_pg \
  bash
```
* Login to Postgres Database
```shell script
psql -U retail_user \
  -d retail_db  \
  -W
```
* Let us create additional table with 2 fields.
```sql
CREATE TABLE t (
  i INT,
  s VARCHAR(10)
);
```
* CRUD Operations (DML)
  * C - Create -> INSERT
  * R - Read -> Querying using SELECT
  * U - Update -> UPDATE
  * D - Delete -> DELETE
  * CRUD Operations are achieved using Data Manipulation Language (DML).
  * Syntax with respect DML Statements is same with most of the RDBMS Databases.
* Let us insert data into the table.
  * Inserting one row at a time.
```sql
INSERT INTO t VALUES (1, 'Hello');
INSERT INTO t VALUES (2, 'World');
SELECT * FROM t;
```
  * Inserting multiple rows at a time (bulk insert or batch insert)
```sql
INSERT INTO t VALUES 
    (1, 'Hello'),
    (2, 'World');
SELECT * FROM t;
```
* Let us update data in the table.
```sql
UPDATE t SET s = lower(s);
SELECT * FROM t;
UPDATE t SET s = 'Hello' WHERE s = 'hello';
SELECT * FROM t;
```
* Let us delete data from the table.
```sql
DELETE FROM t WHERE s = 'Hello';
SELECT * FROM t;
DELETE FROM t; -- Deletes all the data from a given table.
SELECT * FROM t;
```
* We can also clean up the whole table using DDL Statement. `TRUNCATE` is faster to clean up the data compared to `DELETE` with out conditions.
```sql
TRUNCATE TABLE t;
SELECT * FROM t;
```
* We can drop the table using `DROP` Command.
```sql
DROP TABLE t;
```
* SQL Commands starts with `CREATE`, `ALTER`, `TRUNCATE`, `DROP` etc are called as Data Definition Language or DDL Commands.

## Setup Project using PyCharm
Let us setup project using PyCharm. I will be using PyCharm Enterprise Edition. However, you can use Community Edition as well.
* Create New Project by name **data-copier**.
* Make sure virtual environment is created with name **dc-venv**. It will make sure we are in appropriate virtual environment related to our project.
* Create a program by name **app.py**.
* Add below code to it and validate using PyCharm.
```python
def main():
    print("Hello World!")


if __name__ == "__main__":
    main()
```

## Managing Dependencies
Let us see how we can manage dependencies for Python Projects.
* We use Pip to manage dependencies for Python based Projects.
* Here is the typical life cycle.
  * Search for Python libraries using [https://pypi.org](https://pypi.org).
  * One can install directly using `pip install` Command. For example `pip install configparser`.
  * To overcome compatibility issues, we need to decide on versions.
  * We can also pass version of the library to `pip install` command. For example `pip install configparser==5.0.0`.
* For projects we keep track of all the external libraries using a text file - e.g. **requirements.txt**.
* We can add libraries to our project using **requirements.txt**. For example, here we are adding Pandas to our project.
```text
pandas==1.3.2
```
* We will take care of other libraries later.
* Once libraries are defined as part of **requirements.txt**, we can run `pip install -r requirements.txt`.
* We can also uninstall all the libraries using relevant command.
 
## Create GitHub Repository
As we have skeleton of the project, let us setup GitHub repository to streamline future code changes.
* GitHub can be used to version our application.
* We can initialize GitHub repository by saying `git init`. It will create **.git** directory to keep track of changes.
* GitHub should only contain source code related to our application. Hence, we should ignore non source code files and folders such as following:
  * **.idea** - a folder created by PyCharm to keep track of IDE preferences and settings.
  * **__pycache__** - a file which contain bytecode of the program which can be interpreted by Python interpreter.
  * **data-copier-env** - a directory which is created when virtual environment is added to our project.
  * We might add other files in future.
```text
dc-venv
__pycache__
.idea
```
* Add the source code files to keep track of changes and then commit.
```shell script
git add . 
# Adds all the files recursively from the directory in which command is executed
# We can also add specific files or directories
git commit
# Review all the files and make sure only source code files are included along with README.md
# Add commit message and save. If you are not comfortable with command line you can use Pycharm Git Plugin.
# You can also give commit message with git commit itself
git commit -m "Initial Commit"
```
* Now go to [GitHub Website](https://www.github.com) and add a repository as demonstrated. Make sure to sign up if you do not have account.
* Follow the instructions provided after repository is created to push the existing repository.
