# AWS-WordPress-Elastic-Beanstalk

Deploying a high-availability WordPress website with an external Amazon RDS database to Elastic Beanstalk

**Overview and High Level Design**

Use Default VPC

# Launch a DB instance in Amazon RDS in default VPC

1. Open the RDS console **https://console.aws.amazon.com/rds/home**
2. In the navigation pane, choose **Databases**
3. Choose **Create database**
4. Choose **Standard Create**
5. Choose Engine options: **Amazon Aurora** - edition **Amazon Aurura MySQL-Compatible Edition** **Aurora (MySQL 5.7) 2.10.2**
6. Choose Templates - **Dev/Test**
8. Under **Settings** for **DB cluster identifier**, type ebdb
9. Under **Credential Settings** enter the **Master username** and **Master password** - Note!
10. Under **Instance configuration** select **DB instance class** to **Burstable classes (includes t classes) select **db.t3.small**
11. Under **Availablity & durability**, **Multi-AZ deployment** - For HA high availablity, set this to **Create an Aurura Replica or Reader Node in a different AZ**
12. Under **Connectivity**, **Database authentication**, **Monitoring** keep all defaults
13. Choose **Create database**

**modify the inbound rules on the security group that's attached to your RDS instance**

1. Choose the name of your DB instance to view its details
2. In the **Connectivity** section, make a note of the Subnets, Security groups, and Endpoint that are displayed on this page
3. 
