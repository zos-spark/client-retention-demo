#Client Retention Demo

## Purpose
This project serves as an end-to end demonstration and integration verification test (IVT) for Spark on z/OS [reference architecture](https://ibm.box.com/shared/static/xm05xl372hkbmmj4eu9fhoq0kplytzp3.png).

## Contents

* z System Data Package
	* Sample Client Profile Data (VSAM)
	* Sample Client Transaction History (DB2)
	* Preload scripts
* Notebooks
	* Sample Scala Notebook that performs data munging on DB2 and VSAM data and writes results to MongoDB.
	* Sample Python Notebooks that analyzes data in MongoDB.
	* Sample Python Notebook that uses [Dato](https://dato.com) to provide a churn analysis on the data in MongoDB.


## Dependencies
The client retention demo requires the following:

* Docker Toolbox
* z/OS Host (z/System)
 * [IBM z/OS Platform for Apache Spark](http://www-03.ibm.com/systems/z/os/zos/apache-spark.html) with APAR
 * VSAM
 * DB2
* Interactive-Insights-Workbench (I2W) (Docker)
* MongoDB (Docker)
* Scala-Workbench (Docker)

## Setup

### Setup VSAM and DB2 on z/OS
Go into the ```data/zos/``` directory and follow the README in that directory.

### Setup Interactive-Insights-Workbench (I2W) Environment
Download [I2W](https://github.com/zos-spark/interactive-insights-workbench) and following the [Quickstart](https://github.com/zos-spark/interactive-insights-workbench#quickstart) instructions.


### Setup MongoDB
If you haven't already, Download [I2W](https://github.com/zos-spark/interactive-insights-workbench) and follow the [Sample Database](https://github.com/zos-spark/interactive-insights-workbench#sample-database) instructions.


### Setup Scala-Workbench Environment
Download the [Scala-Workbench](https://github.com/zos-spark/scala-workbench) and follow the setup instructions.  In the [Setup Environment Variables](https://github.com/zos-spark/scala-workbench#setup-environment-variables-optional) section, there is an example ```template/docker-compose.yml.template``` file.  In this file, make sure to fill in the following fields:

* JDBC_USER - User able to query VSAM and DB2
* JDBC_PASS - Password for user able to query VSAM and DB2
* JDBC_HOST - Host system of VSAM and DB2

If you are using a username/password with MongoDB, also fill in the following fields:

* MONGO_USER - User able to access the MongoDB database
* MONGO_PASS - Password for the user abel to access the MongoDB database
* MONGO_HOST - Host system of the MongoDB instance

## Verification Test
Once the setup steps listed above have been completed, you can verify the setup using the following scripts:

1. On the Scala-Workbench run the ```client_retention_demo.ipynb``` notebook.
2. On I2W run the ```client_explore.ipynb``` and ```churn_business_value.ipynb``` notebooks.

###client\_retention\_demo.ipynb
The ```client_retention_demo.ipynb``` will use [IBM z/OS Platform for Apache Spark](http://www-03.ibm.com/systems/z/os/zos/apache-spark.html) to access data stored in a DB2 table and in a VSAM data set.  It will then calculate some aggregate statistics, then offload the results to MongoDB.

###client\_explore.ipynb
The ```client_explore.ipynb``` will read from MongoDB, and create several interactive exploritory widgets.

###client\_retention\_demo.ipynb
The ```churn_business_value.ipynb``` will read from MongoDB, and create several interactive widgets that show business value of target groups.