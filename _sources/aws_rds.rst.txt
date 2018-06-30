AWS Relational Database Service - RDS
=====================================

.. toctree::
   :maxdepth: 2
   
   aws_rds_multi-az
   aws_rds_storage

Relation Database Service – RDS Overview
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Amazon Relational Database Service (RDS) is a web service that makes it easier to set up, operate, and scale a relational database in the cloud.

* RDS provides cost-efficient, resizable capacity for an industry-standard relational database and manages common database administration tasks.

*RDS features & benefits

	* CPU, memory, storage, and IOPS can be scaled independently.

	* manages backups, software patching, automatic failure detection, and recovery.
	
	* automated backups can be performed as needed, or manual backups can be triggered as well. Backups can be used to restore a database, and the RDS restore process works reliably and efficiently.

	* provides high availability with a primary instance and a synchronous secondary instance that can be failovered to seamlessly when a problem occurs.

	* provides elasticity & scalability by enabling MySQL, MariaDB, or PostgreSQL Read Replicas to increase read scaling.

	* supports MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server, and the new, MySQL-compatible Amazon Aurora DB engine

	* in addition to the security in the database package, IAM users and permissions can help to control who has access to the RDS databases

	* databases can be further protected by putting them in a VPC, using SSL for data in transit and encryption for data in rest
	
	*However, **as it is a managed service, shell (root ssh) access to DB instances is not provided**, and this restricts access to certain system procedures and tables that require advanced privileges.

RDS Components
^^^^^^^^^^^^^^

* **DB Instance**

	* is a basic building block of RDS

	* is an isolated database environment in the cloud

	* each DB instance runs a DB engine. AWS currently supports MySQL, MariaDB, PostgreSQL, Oracle, and Microsoft SQL Server & Aurora DB engines
	
	* can be accessed from Amazon AWS command line tools, Amazon RDS, APIs, or the AWS Management RDS Console.

	* computation and memory capacity of an DB instance is determined by its DB instance class, which can be selected as per the needs

	* for each DB instance, 5 GB to 6 TB of associated storage capacity can be selected

	* storage comes in three types: Magnetic, General Purpose (SSD), and Provisioned IOPS (SSD), which differ in performance characteristics and price
	
	* each DB instance has a DB instance identifier, which is customer-supplied name and must be unique for that customer in an AWS region. It uniquely identifies the DB instance when interacting with the Amazon RDS API and AWS CLI commands.

	* each DB instance can host multiple databases, or a single Oracle database with multiple schemas.

	* can be hosted in an AWS VPC environment for better control

* **Regions and Availability Zones**

	* AWS resources are housed in highly available data center facilities in different areas of world, these data centers are called regions which further contain multiple distinct locations called Availability Zones
	
	* Each AZ is engineered to be isolated from failures in other AZs, and to provide inexpensive, low-latency network connectivity to other AZs in the same region

	* DB instances can be hosted in several AZs, an option called a Multi-AZ deployment.

		* Amazon automatically provisions and maintains a synchronous standby replica of the DB instance in a different AZ.

		* Primary DB instance is synchronously replicated across AZs to the standby replica

		* Provides data redundancy, failover support, eliminate I/O freezes, and minimize latency spikes during system backups.

* **Security Groups**

	* security group controls the access to a DB instance, by allowing access to the specified IP address ranges or EC2 instances

* **DB Parameter Groups**

	* A DB parameter group contains engine configuration values that can be applied to one or more DB instances of the same instance type

* **DB Option Groups**

	* Some DB engines offer tools that simplify managing the databases and making the best use of data.

	* Amazon RDS makes such tools available through option groups for e.g. Oracle Application Express (APEX), SQL Server Transparent Data Encryption, and MySQL memcached support.
	
RDS Interfaces
^^^^^^^^^^^^^^

* RDS can be interacted with multiple interfaces

	* AWS RDS Management console

	* Command Line Interface

	* Programmatic Interfaces which include SDKs, libraries in different languages, and RDS API
	
RDS Pricing
^^^^^^^^^^^

* Instance class

	* Pricing is based on the class (e.g., micro, small, large, xlarge) of the DB instance consumed.
	
* Running time

	* Billed by the instance-hour, which is equivalent to a single instance running for an hour for e.g., a single instance running for two hours = two instances running for one hour, both consume 2 instance-hours.

	* if a DB instance runs for only part of an hour, full instance-hour is charged

* Storage

	* Storage capacity provisioned for the DB instance is billed per GB per month.

	* If the provisioned storage capacity is scaled within the month, the bill will be pro-rated.

* I/O requests per month

	* Total number of storage I/O requests made in a billing cycle.

* Backup storage

	* Automated backups & any active database snapshots consume storage

	* Increasing backup retention period or taking additional database snapshots increases the backup storage consumed by the database.

	* RDS provides backup storage up to 100% of the provisioned database storage at no additional charge for e.g., if you have 10 GB-months of provisioned database storage, RDS provides up to 10 GB-months of backup storage at no additional charge.

	* Most databases require less raw storage for a backup than for the primary dataset, so if multiple backups are not maintained, you will never pay for backup storage.

	* Backup storage is free only for active DB instances.

* Data transfer
	
	* Internet data transfer in and out of your DB instance.

* Reserved Instance

	* In addition to regular RDS pricing, reserved DB instances can be purchased
