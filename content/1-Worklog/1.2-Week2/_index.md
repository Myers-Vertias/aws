---

title: "Week 2 Worklog"

date: 2026-09-16

weight: 1

chapter: false

pre: " <b> 1.2. </b> "

---

### Week 2 Objectives:

* Strengthen practical knowledge of AWS services and cloud resource management through hands-on workshops.

* Understand how applications securely access AWS services using IAM Roles instead of hard-coded access keys.

* Become familiar with AWS Cloud9 as a browser-based development environment and practice basic AWS CLI operations.

* Learn Amazon S3 for object storage, static website hosting, access control, versioning, and content distribution.

* Understand Amazon RDS as a managed relational database service and practice connecting an application running on EC2 to an RDS database.

* Gain practical experience in deploying, testing, monitoring, backing up, and cleaning up AWS resources.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Wednesday | **IAM Roles & AWS Cloud9**<br>• Learn how applications access AWS services using Access Keys and Secret Access Keys.<br>• Understand the security risks of embedding long-term credentials in applications.<br>• Learn IAM Roles for EC2 and how temporary permissions can be provided to applications through an instance role.<br>• **Practice:** Create an EC2 instance, create an IAM Role, attach the required permissions, and associate the role with EC2.<br>• Learn AWS Cloud9 as a browser-based IDE.<br>• Practice basic command-line operations, file management, and AWS CLI commands in Cloud9. | 09/16/2026 | 09/16/2026 | [AWS Cloud Journey](https://cloudjourney.awsstudygroup.com/vi/) |
| Friday | **Amazon S3 & Amazon RDS**<br>• Learn Amazon S3 concepts: buckets, objects, access control, and storage management.<br>• **Practice:** Create an S3 bucket, upload objects, configure static website hosting, review Block Public Access and public object settings, and explore versioning.<br>• Learn how CloudFront can be used to distribute S3 static website content.<br>• Learn Amazon RDS as a managed relational database service and understand supported database engines, connectivity, security, backups, and Multi-AZ concepts.<br>• **Practice:** Create a VPC, Security Group, DB Subnet Group, and MySQL RDS instance.<br>• Create an EC2 instance and connect to it using SSH.<br>• Deploy a Node.js application on EC2 and configure the application to connect to the RDS database.<br>• Practice checking RDS connectivity, application deployment, database backup/restore, and resource cleanup. | 09/18/2026 | 09/18/2026 | [AWS Cloud Journey](https://cloudjourney.awsstudygroup.com/vi/) |

### Week 2 Achievements:

* Strengthened practical knowledge of AWS service management through hands-on workshops.

* Learned how IAM Roles can be used to provide applications running on EC2 with permissions to access AWS services without storing long-term credentials in the application.

* Understood the difference between access keys and IAM Roles and the importance of using secure credential management practices.

* Practiced creating and attaching IAM Roles to EC2 instances.

* Became familiar with AWS Cloud9 as a browser-based development environment.

* Practiced using the Cloud9 terminal and basic file management operations.

* Practiced basic AWS CLI operations from a cloud-based development environment.

* Learned the fundamentals of Amazon S3, including:

  * Buckets
  * Objects
  * Object storage
  * Access control
  * Block Public Access

* Practiced creating S3 buckets and uploading objects.

* Learned how Amazon S3 can be used for static website hosting.

* Explored S3 security configuration and public access settings.

* Learned S3 Versioning and its role in protecting object data and maintaining previous versions.

* Learned the basic relationship between Amazon S3 and CloudFront for content distribution.

* Gained a foundational understanding of Amazon RDS as a managed relational database service.

* Learned important RDS concepts, including:

  * Database engines
  * DB instances
  * Endpoints and ports
  * DB Subnet Groups
  * Security Groups
  * Multi-AZ
  * Read Replicas
  * Automated Backups
  * DB Snapshots

* Practiced creating a MySQL RDS database instance in a custom VPC environment.

* Practiced configuring networking components required for RDS, including VPCs, subnets, DB Subnet Groups, and Security Groups.

* Practiced creating an EC2 instance and connecting to it using SSH.

* Successfully deployed a Node.js application on EC2 and configured the application to communicate with the RDS MySQL database.

* Practiced troubleshooting application and database connectivity issues, including:

  * Incorrect database host configuration
  * RDS connectivity problems
  * Security Group configuration issues
  * Application process failures
  * Port 3306 database connection errors

* Learned how to monitor RDS resources and review database backup and restore options.

* Gained practical experience managing AWS resources from initial deployment through testing, troubleshooting, backup, and cleanup.

* Developed a better understanding of how IAM, compute, storage, development environments, and managed databases work together to support cloud applications.