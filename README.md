# AWS Refactor Project using Elastic Beanstalk

## Overview

This project involves refactoring an existing application infrastructure to leverage cloud-native services provided by Amazon Web Services (AWS). The refactored architecture utilizes Elastic Beanstalk to automate the deployment and management of the application, along with other AWS services such as RDS (Relational Database Service), Amazon ElastiCache (Memcache), and Amazon MQ (Rabbit MQ). The goal is to enhance scalability, reliability, and maintainability while optimizing costs.

## Components

### Elastic Beanstalk Web Tier Environment

- **Platform**: Elastic Beanstalk is configured to use Tomcat 9 and AWS Corretto 11 as the platform for hosting the web application.
- **Deployment Method**: The Elastic Beanstalk environment is set up using the Public and Private VPC deployment method. Instances provisioned by Elastic Beanstalk are deployed in the private subnet, while the Load Balancer provisioned by Elastic Beanstalk is deployed in the Public Subnet.

### Cloud-Native Services

- **RDS (Relational Database Service)**: Amazon RDS is used to host the database for the application.
- **Amazon ElastiCache (Memcache)**: ElastiCache is utilized to provide in-memory caching for improved performance.
- **Amazon MQ (Rabbit MQ)**: Amazon MQ is employed as a message broker for communication between application components.

### Security Configuration

- **Security Groups**: Elastic Beanstalk creates a security group for the instances, allowing access to backend services (RDS, ElastiCache, Amazon MQ) on their respective ports. This ensures secure communication between components.
- **SSL Certificate**: The application is accessed securely over HTTPS. A certificate is generated through AWS ACM (Amazon Certificate Manager) and associated with the Elastic Beanstalk environment.

### DNS Configuration

- **CNAME**: A CNAME record is created to map the Elastic Beanstalk endpoint to a public DNS. This provides a user-friendly domain name for accessing the application.

## Implementation Steps

1. **Environment Setup**: Configure Elastic Beanstalk environment with Tomcat 9 and AWS Corretto 11 platform.
   
2. **AWS Service Configuration**: Set up RDS, ElastiCache, and Amazon MQ with appropriate configurations for the application.

3. **VPC Configuration**: Configure public and private subnets in the VPC for Elastic Beanstalk deployment.

4. **Security Group Configuration**: Configure security groups to allow communication between Elastic Beanstalk instances and backend services.

5. **SSL Certificate Configuration**: Generate a certificate through AWS ACM and associate it with the Elastic Beanstalk environment for HTTPS access.

6. **DNS Configuration**: Create a CNAME record to map the Elastic Beanstalk endpoint to a public DNS.

7. **Deployment**: Deploy the application to Elastic Beanstalk and ensure proper communication with backend services.

8. **Testing and Optimization**: Test the application functionality and optimize the infrastructure for performance and cost-efficiency.

## Conclusion

By following the steps outlined above, the application infrastructure can be successfully refactored using Elastic Beanstalk and other AWS services. This approach offers scalability, reliability, and security benefits, allowing for efficient management and operation of the application in the cloud.

# Database
Here,we used Mysql DB 
sql dump file:

git clone -b aws-refactor-beanstalk https://github.com/seunayolu/devopsproject.git

- db_backup.sql file is a mysql dump file we have to import this dump to mysql db server

- Setup an Ubuntu EC2 instance in the public subnet to serve as a bastion host to connect the the RDS instance. SSH into the EC2 and run the command below to install mysql client and import the database dump into RDS.

```
sudo apt update && apt install mysql-client
cd devopsproject
mysql -h <database_endpoint> -u <user_name> -p<password> <database_name> < src/main/resources/db_backup.sql
```

