# Getting Started
As part of this module we will set up the development environment to develop data integration application to copy data from a source database (MySQL) to a target database (Postgres).

* Setup Docker
* Quick Overview of Docker
* Setup MySQL Database
* Overview of MySQL
* Setup Postgres Database
* Overview of Postgres
* Setup Project using PyCharm
* Add required dependencies
* Create GitHub Repository

## Setup Docker
Let us understand different options to setup Docker using different environments. We will also see how to set up Docker on Ubuntu 18.04 using GCP.
* If your Mac or PC have 16 GB RAM and Quad Core, I would recommend to setup Docker Desktop. Just Google and Set it up.
* For Windows, there might be some restrictions on Windows 10 Home and older versions.
* If you do not want to install locally, you can get credits from GCP and get a VM to setup Docker based environment.
  * Sign up for GCP and get $300 credits
  * Set up gcloud for logging in using Mac Terminal or Windows Powershell
  * Provision Ubuntu 18.04 VM
  * Install Docker by using article from Digital Ocean.
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
## Setup MySQL Database
Let us setup source database using MySQL as part of Docker Container.
* Clone **retail_db** repository from GitHub. We will use this repository to setup tables and load data as part of the database.
```
git clone https://www.github.com/dgadiraju/retail_db.git
```
* Pull image `docker pull mysql`
* Create and start container using `docker run`
```
docker run \
  --name mysql_retail_db \
  -e MYSQL_ROOT_PASSWORD=itversity \
  -d \
  -v /home/dgadiraju/retail_db:/retail_db \
  -p 3306:3306 \
  mysql
```
* We can review the logs by using `docker logs -f mysql_retail_db` command.
* Make sure retail_db is either mounted or copied on to the Docker Container.
* Connect to MySQL database using `docker exec`
```
docker exec \
  -it mysql_retail_db \
  mysql -u root -p
```
* Create Database and User as part of MySQL running in Docker
```
CREATE DATABASE retail_db;
CREATE USER retail_user IDENTIFIED BY 'itversity';
GRANT ALL ON retail_db.* TO retail_user;
FLUSH PRIVILEGES;
```
* Run **/retail_db/create_db.sql** to create tables and load data using MySQL CLI.
```
USE retail_db;
SOURCE /retail_db/create_db.sql
```
## Overview of MySQL
Let us get a quick overview of MySQL.
* MySQL is multi tenant database server. It means there can be multiple databases per server.
* We typically create databases and users then grant different types of permissions for different users.
* Here are the details about our database:
  * Database Name: **retail_db**
  * Database User: **retail_user**
  * Permissions: **ALL** (DDL, DML, Queries)
* Let us create additional table with 2 fields.
```
CREATE TABLE t (
  i INT,
  s STRING
);
```
* Let us insert data into the table.
  * Inserting one row at a time.
```
INSERT INTO t VALUES (1, 'Hello');
INSERT INTO t VALUES (2, 'World');
```
  * Inserting multiple rows at a time (bulk insert or batch insert)
* Let us update data in the table.
* Let us delete data from the table.
## Setup Postgres Database
## Overview of Postgres
## Setup Project using PyCharm
## Add required dependencies
## Create GitHub Repository
