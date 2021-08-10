**Databases**
- RDS - Relational database Services
- runs on virtual machines
- you cannot login (ssh) into your rds instances
- updating RDS machines in amazon's responsibility
- it is not serverless - exception is Amazon Aurora which is serverless
- 6 OLTP dbs supported = mssql, oracle, mysql, postgres, aurora(amazon), mariadb
- 2 features
  - multi AZ - for DR - has auto failover
  - read replicas - for performance - no automatic failover
- non rdbms
  - collection -> table
  - document -> row
  - key values -> columns (fields) -> thi is flexible
- Amazon Redshift - Amazon's data warehousing service for running OLAP kind of processing
- Elasticache - scalable in-memory cache - supports memcached and redis
- when connecting an EC2 instance to RDS, make sure that the app server instance running on EC2 is able to access the DB port
- for this to work, add a new inbound rule in the DB security group to allow web server to connect to DB via the specified port - e.g for mysql allow port 3306 to be accessible
- sample user-data script to install php, wordpress on ec2 and connect to mysql db on RDS

```
#!/bin/bash
amazon-linux-extras install epel
amazon-linux-extras install php7.2
yum install httpd -y
cd /var/www/html
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp -r wordpress/* /var/www/html
rm -rf wordpress
rm -rf latest.tar.gz
chmod -R 755 wp-content
chown -R apache:apache wp-content
chkconfig httpd on
service httpd start
```

**Backups**
- automated backups
  - allows you to recover db to any point in time within a retention period
  - retention period is between 1 and 35 days
  - takes full daily snapshot and also stores transaction logs throughout the day
  - when you want to recover, you take the most recent snapshot and apply transaction logs for that day
  - so you can recover to a point in time to a second
  - enabled by default
  - back up is stored in S3 and storage is free equal to the size of your db
  - backups are done during a time window - during this window, storage I/O may be suspended and latency may be little high
  - backups are deleted when the original db is deleted
- db snapshots
  - snapshots are done manually - usually user initiated
  - snapshots are retained even if the original RDS instance is deleted
- restoring backups
  - when you restore either from automated or snapshot, it is restored as a new version of the db with a new DNS endpoint
- encryption
  - encryption at rest id support for all supported db  types
  - AWS KMS is used to encrypt
  - once RDS instance is encrypted, all data, backed up data, read replicas, snapshots are all encrypted
- multi-AZ
  - all changes are synchronously replicated to the alternated instance in another AZ
  - aws handles the replication
  - during outage or maintenance, RDS automatically fails over to the standby instance - DNS will remain the same
  - is used for DR only, not for performance improvement
  - available for mssql, oracle, mysql, postgresql, mariadb - (aurora by design is fault tolerant)
- read replicas
  - data is asynchronously replicated from main db to the read replicas
  - helps improve performance by using read replicas for all read operations
  - you can have replicas of read-replicas
  - generally used for read heavy database workloads
  - available for mssql, postgresql, mariadb, aurora
  - used for scaling, not for DR
  - must turn on automatic backs ups
  - can have upto 5 replicas
  - can be either aurora or mysql replicas
  - when you have read replicas of read replicas, latency may become an issue
  - each read replica will have a separate DNS endpoint
  - read replicas can be multi AZ - can also be in a different region
  - read replicas can be promoted to their own db - in this case, replication breaks

**Dynamo DB**
- amazon's nosql db
- single digit millisecond latency
- supports both key value and document data
- fit for mobile, web, gaming, iot etc
- stored on ssd storage
- spread across 3 geographically distinct data centres
- eventual consistent reads
  - this is the default mode
  -  data will be replicated to all copies usually more than a second
  - to reads of written data after a short time will return latest data
  - but data is not instantly available across all replicas
- strongly consistent reads
  - if you need latest data less than 1 sec after write then go for this approach

Redshift
- amazon's data warehousing service on the cloud
- used for business intelligence
- petabyte scale
- very less cost compared to other warehousing solutions
- used for OLAP processing
- configurations
  - single node - 160 GB
  - multi node
    - leader node - manages client connections and receives queries
    - compute node - performs queries and computations
    - up to 128 compute node under a leader node
- advanced compression
    - redshift does column compression instead of row
    - since column data has same datatype, compression can be more effective
    - compression ration is much more than traditional rdbms
    - redshift does not need indexes or materialized views - so less space usage
    - when loading data into an empty table, based on sample of data, redshift decides the best compression scheme
- massively parallel processing (NPP)
    - redshift automatically distributes data and queries across nodes
    - easy to add new nodes to the warehouse - scale out
    - so query performance remains fast even as the warehouse grows
- backups
    - enabled by default with 1 day retention period
    - max retention period is 35 days
    - maintains at least 3 copies of data - one original and one replica on compute node and one backup on S3
    - asynchronous replication of snapshot to s3 in another region for DR
- pricing
    - charged only for compute node , not leader node
    - billed for 1 unit per node per hour
    - so 3 node cluster running for 1 full month will be charged for 2160 (3 * 24 * 30) instance hours
    - also will be charged for backup
    - also will be charged for data transfer within the vpc
- security considerations
    - data in transit secured by SSL
    - data at rest secured using AES-256 encryption
    - redshift takes care of key management by default
    - but you can manage your own keys using HSM and/or AWS KMS
- availability
    - currently available only in 1 AZ
    - snapshots can be restored to a new AZ during outage

**Aurora**
- amazon's mysql compatible rdbms engine
- can work with postgres also
- cheaper than mysql, more performant than mysql
- 2 copies of data in each AZ with min of 3 AZs - total 6 copies
- transparently handles loss of up to 2 copies of data without affecting write availability
- transparently handles loss of up to 3 copies of data without affecting read availability
- self healing - discs are auto scanned for errors and auto corrected

- automated backups are enabled by default
- you can take snapshots and can be shared with other aws accounts
- to migrate from mysql to aurora - create a aurora read replica of the mysql instance - then promote one of the replicas (write) to become a master - this will have different DNS endpoint

**Elasticache**
- a web service that makes it easy to deploy, operate and scale in-memory cache in the cloud
- supports memcached and redis
- faster then disk based IO
- improves db and web app performance
