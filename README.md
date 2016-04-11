# Client Retention Demo

## Purpose
This project serves as an end-to-end demonstration and integration verification test (IVT) for the Spark on z/OS [reference architecture](https://ibm.box.com/shared/static/xm05xl372hkbmmj4eu9fhoq0kplytzp3.png).

The project uses imitation financial data for a retail bank scenario. The retail bank is assumed to have two data sources for their system of records:

* Client Profile Data (VSAM)
* Client Transaction History (DB2)

The bank would use the Scala Workbench to distill these data sources into a desired data set for use by data explorers who would use the Interactive Insights Workbench to perform downstream analytics.

## Overview

To perform an IVT of a Spark on z/OS deployment one would do the following:

1. Install and configure the [IBM z/OS Platform for Apache Spark](http://www-03.ibm.com/systems/z/os/zos/apache-spark.html). 
2. Prime the z/OS data sources with sample demo data.
3. Install the Scala Workbench
4. Install the Interactive Insights Workbench (I2W) and MongoDB
5. Run a Scala Notebook to prime the MongoDB
6. Run a I2W Notebook to visualize downstream analytics.

## Contents

* z System Data Package
	* Sample Client Profile Data (VSAM)
	* Sample Client Transaction History (DB2)
	* Preload scripts
* Notebooks
	* Sample Scala Notebook that performs data munging on DB2 and VSAM data and writes results to MongoDB.
	* Sample Python Notebooks that analyzes data in MongoDB.
	* Sample Python Notebook that uses [Dato](https://dato.com) to provide a churn analysis on the data in MongoDB. <font color="red">*Pending contribution from Dato*</font>.

## Dependencies
The client retention demo requires the following:

* Docker Toolbox
* z/OS Host (z/System)
 * [IBM z/OS Platform for Apache Spark](http://www-03.ibm.com/systems/z/os/zos/apache-spark.html) with <font color="blue">PTF UI36538</font>
 * VSAM
 * DB2
 * [IBM JAVA 8 SR2-FP10 sdk for 64-bit System z](http://www.ibm.com/developerworks/java/jdk/linux/download.html)
* Interactive Insights Workbench (Docker)
* MongoDB (Docker)
* Scala Workbench (Docker)

## Demo Setup

### z/OS Data
Prepare VSAM and DB2 data sources with sample demo data. Go into the ```data/zos/``` directory and follow the README in that directory.

#### Setup Interactive-Insights-Workbench (I2W) Environment
Download [I2W](https://github.com/zos-spark/interactive-insights-workbench) and following the [Quickstart](https://github.com/zos-spark/interactive-insights-workbench#quickstart) instructions.

#### Setup MongoDB
If you haven't already, Download [I2W](https://github.com/zos-spark/interactive-insights-workbench) and follow the [Sample Database](https://github.com/zos-spark/interactive-insights-workbench#sample-database) instructions.

#### Setup Scala-Workbench Environment
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

#### client\_retention\_demo.ipynb
The ```client_retention_demo.ipynb``` will use [IBM z/OS Platform for Apache Spark](http://www-03.ibm.com/systems/z/os/zos/apache-spark.html) to access data stored in a DB2 table and in a VSAM data set.  It will then calculate some aggregate statistics, then offload the results to MongoDB.

#### client\_explore.ipynb
The ```client_explore.ipynb``` will read from MongoDB, and create several interactive exploritory widgets.

#### churn\_business\_value.ipynb
The ```churn_business_value.ipynb``` will read from MongoDB, and create several interactive widgets that show business value of target groups.

#### client\_churn\_analysis.ipynb
The ```client_churn_analysis.ipynb``` will read from MongoDB, and apply machine learning techniques against the data using technology from [Dato](https://dato.com). <font color="red">*Pending contribution from Dato*</font>
