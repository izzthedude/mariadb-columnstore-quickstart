# MariaDB ColumnStore Quick Start

[MariaDB ColumnStore](https://mariadb.com/docs/features/mariadb-columnstore/) is a columnar storage engine that utilizes a massively parallel distributed data architecture. ColumnStore is designed for big data scaling to process petabytes of data, linear scalability and exceptional performance with real-time response to analytical queries. 

**This README provides steps to get you up and running with MariaDB ColumnStore using a [Docker](https://www.docker.com/) container**

## Prerequisites 

Before getting started with this walkthrough you will need:

* [Docker Desktop](https://www.docker.com/get-started)
* [git](https://git-scm.com/) (optional)

## Overview

This walkthrough will step you through the process of installing, accessing and configuring single-node MariaDB ColumnStore instance, made available through MariaDB Community Server within a Docker container. 

**Note:** To use this download, import and use this data on an existing, non-container MariaDB Server instance (including [MariaDB SkySQL](https://mariadb.com/skyview)) jump to step #4.

### 1. Using a Docker image to create a Docker container

Pull down the [MariaDB Community Server (with ColumnStore) image](https://hub.docker.com/r/mariadb/columnstore) and create a new container by executing the following command in a terminal window:

```bash
docker run \
  --name mcs_container \
  -p 3306:3306 \
  -d \
  mariadb/columnstore
```

### 2. Enter the newly created container

```bash
docker exec -it mcs_container bash
```

### 3. Install `git` and `wget`

```bash
yum install git wget
```

### 4. Clone this repository

```bash
git clone https://github.com/izzthedude/mariadb-columnstore-quickstart.git

cd mariadb-columnstore-quickstart
```

### 5. Download flight data 

The sample data used in this example comes from the United States Department of Transportation, which provides millions of records of flight data spanning many years.

Use the following command to execute a script that will download US domestic flight data between a `start` and `end` year. 

```bash 
./get_flight_data.sh
```

**Note:** Keep in mind that there are millions of flight records that can take up gigabytes of storage space.

### 6. Create mariadb user and grant privileges
```bash
# Enter MariaDB CLI
mariadb

# Commands from here onwards are from within the MariaDB CLI.
CREATE USER 'app_user'@'%' IDENTIFIED BY 'CHANGE_PASSWORD_HERE';
GRANT ALL ON travel.* TO 'app_user'@'%';
```

### 7. Create schemas and load data

This repository includes the following schema:

* travel (`database`)
    * **airlines** (`ColumnStore table`) - airlines providing flights within the United States
    * **airports** (`ColumnStore table`) - airports within the United States
    * **flights** (`ColumnStore table`) - US domestic flight records 

In this sample, the [create_and_load.sh](create_and_load.sh) script will be used to create the schemas (via [schema.sql](schema.sql)) and load the following tables:

* travel.airlines - using [data/airlines.csv](data/airlines.csv)
* travel.airports - using [data/airports.csv](data/airports.csv)
* travel.flights - using data that is downloaded with the [get_flight_data.sh](get_flight_data.sh) script

Execute the following command to execute a script to create the schema and load data.

```bash
./create_and_load.sh 127.0.0.1 3306 app_user CHANGE_PASSWORD_HERE
```

## Support and Contribution <a name="support-contribution"></a>

This is a fork strictly for personal/academic use. This fork is **NOT** officially affiliated or endorsed by the original developers. As such, any issues from my changes **MUST NOT** be reported to them.

## License <a name="license"></a>
[![License](https://img.shields.io/badge/License-MIT-blue.svg?style=plastic)](https://opensource.org/licenses/MIT)
