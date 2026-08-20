# cloud-lab-5-assignment-1
Cloud Computing Lab 5 – Assignment 1
AWS RDS MySQL Integration with WordPress on EC2
Objective
The objective of this assignment is to deploy an Amazon RDS MySQL database, connect the existing Lab 4 WordPress application running on an Amazon EC2 instance to the RDS database, and demonstrate all four CRUD operations from the running EC2 application.
---
Architecture
```text
                    Internet
                       |
                       v
              +------------------+
              |   EC2 Instance   |
              |     Ubuntu       |
              |      Nginx       |
              |    PHP 8.5-FPM   |
              |    WordPress     |
              +--------+---------+
                       |
                       | MySQL / TCP 3306
                       |
                       v
              +------------------+
              |    Amazon RDS    |
              |      MySQL       |
              |     8.4.9        |
              +--------+---------+
                       |
                       v
              +------------------+
              | wordpress DB     |
              | 12 WordPress     |
              | tables            |
              +------------------+
```
---
AWS Services Used
Amazon EC2
Amazon RDS for MySQL
Amazon VPC
Amazon EC2 Security Groups
---
EC2 Application
The existing Lab 4 WordPress application was reused for this assignment.
Configuration	Value
Operating System	Ubuntu
Web Server	Nginx
PHP	8.5-FPM
Application	WordPress
EC2 Public IP	`23.22.209.180`
Application URL	http://23.22.209.180
> **Note:** The EC2 public IP may change if the instance is stopped and started without an Elastic IP.
---
RDS Configuration
An Amazon RDS MySQL instance was deployed for the WordPress application.
Configuration	Value
Database Engine	MySQL
Engine Version	8.4.9
DB Instance Identifier	`lab5-rds`
Instance Class	`db.t3.micro`
Storage	20 GiB
Deployment	Single-AZ
Database Name	`wordpress`
Port	`3306`
Public Access	No
VPC	`vpc-08446e7e5207dc9a6`
Security Group	`lab5-rds-sg`
---
Database Schema
The existing Lab 4 WordPress database was migrated to Amazon RDS.
The RDS database contains 12 WordPress tables:
`wp_commentmeta`
`wp_comments`
`wp_links`
`wp_options`
`wp_postmeta`
`wp_posts`
`wp_term_relationships`
`wp_term_taxonomy`
`wp_termmeta`
`wp_terms`
`wp_usermeta`
`wp_users`
The database was verified using:
```sql
SHOW TABLES;
```
The `wp_posts` table was also verified to contain existing Lab 4 application data.
---
Database Migration
The existing Lab 4 WordPress database was available as a SQL backup on the EC2 instance:
```text
/var/backups/wordpress/wordpress-db.sql
```
The backup was imported into the RDS MySQL database named `wordpress`.
The imported database was verified by connecting to RDS and executing:
```sql
SHOW TABLES;
```
The verification returned 12 WordPress tables.
---
EC2 to RDS Connection
The WordPress application was originally configured to use a local database host:
```text
DB_HOST = localhost
```
After deploying RDS and importing the database, the WordPress configuration was updated to use the RDS endpoint.
The relevant configuration is:
```text
DB_NAME = wordpress
DB_USER = wpuser
DB_HOST = <RDS Endpoint>
DB_PORT = 3306
```
The actual database password is intentionally not included in this repository.
The connection from EC2 to RDS was tested using the MySQL client:
```bash
mysql -h <RDS-ENDPOINT> -P 3306 -u wpuser -p wordpress
```
The connection was successful, and the WordPress tables were accessible from the EC2 instance.
---
Security Configuration
The RDS instance uses the security group:
```text
lab5-rds-sg
```
Inbound database access was restricted to the EC2 Security Group.
The configured inbound rule is:
Type	Protocol	Port	Source
MySQL/Aurora	TCP	3306	EC2 Security Group
No `0.0.0.0/0` rule was used for RDS database access.
RDS public access was also disabled.
This ensures that the database is accessible by the EC2 application without exposing the MySQL port publicly.
---
WordPress URL Configuration
During migration, the existing WordPress database contained the previous application URL.
The `home` and `siteurl` values in the `wp_options` table were updated to the current EC2 public address:
```text
http://23.22.209.180
```
This allowed the WordPress application and administration interface to correctly use the current EC2 public address.
---
CRUD Demonstration
All four CRUD operations were demonstrated from the running WordPress application on EC2.
1. CREATE
A test post named:
```text
Lab 5 CRUD Test
```
was created through the WordPress application.
2. READ
The newly created post was opened and displayed through the running WordPress application.
3. UPDATE
The content of `Lab 5 CRUD Test` was modified and the updated content was verified through the application.
4. DELETE
The test post was moved to the WordPress Trash and permanently deleted.
Therefore:
```text
CREATE  ✓
READ    ✓
UPDATE  ✓
DELETE  ✓
```
---
Evidence
The following evidence should be included with the submission:
EC2 instance/application running
RDS instance deployed and available
RDS security group showing MySQL port 3306 restricted to the EC2 Security Group
Successful EC2-to-RDS MySQL connection
WordPress database tables present in RDS
WordPress application running after the RDS migration
CREATE operation
READ operation
UPDATE operation
DELETE operation
Screenshots can be stored in a `screenshots/` directory in this repository.
Recommended structure:
```text
screenshots/
├── 01-ec2-running.png
├── 02-rds-available.png
├── 03-security-group.png
├── 04-rds-connection.png
├── 05-database-tables.png
├── 06-wordpress-running.png
├── 07-create.png
├── 08-read.png
├── 09-update.png
└── 10-delete.png
```
---
Application URL
EC2 WordPress Application:
http://23.22.209.180
WordPress Admin:
http://23.22.209.180/wp-admin
---
Security Note
No AWS access keys, secret keys, SSH private keys, database passwords, or other sensitive credentials are included in this repository.
The database password is configured directly on the EC2/RDS environment and is intentionally excluded from the README.
---
Result
The existing Lab 4 WordPress application running on Amazon EC2 was successfully connected to an Amazon RDS MySQL database.
The existing WordPress database was migrated to RDS, the database contained 12 WordPress tables, EC2-to-RDS connectivity was verified, and all four CRUD operations were successfully demonstrated through the running EC2 application.
The RDS security group was configured to allow MySQL access only from the EC2 Security Group.
