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

**Modify the inbound rules on the security group that's attached to your RDS instance**

1. Choose the name of your DB instance to view its details
2. In the **Connectivity** section, make a note of the Subnets, Security groups, and Endpoint that are displayed on this page
3. Under **Security**, you can see the security group that's associated with the DB instance. Open the link to view the security group in the Amazon EC2 console
4. In the security group details, choose **Inbound rules**
5. Choose **Edit inbound rules**
6. Choose **Add Rule**
7. For **Type**, choose the DB engine that your application uses
8. For **Source**, type **sg-** to view a list of available security groups. Choose the security group that's associated with the Auto Scaling group that's used with your Elastic Beanstalk environment. This is so that Amazon EC2 instances in the environment can have access to the database
9. Choose **Save**

**Download WordPress**

1. Open the EC2 console **https://us-east-1.console.aws.amazon.com/ec2/**
2. Choose **Launch instance**
3. Under **Name and tags**, type **Linux Server**
4. Under **Application and OS Images (Amazon Machine Image)** select, **Amazon Linux** - **Amazon Linux 2 Kernel 5.10 AMI 2.0.20221210.1 x86_64 HVM gp2 - 64-bit (x86)**
5. Under **Instance type** select, **t2.micro**
6. Under **Key pair (login**, select key pair
7. Keep all other defaults
8. Choose **Launch Instance**
9. Select **Instance** and **Connect**
10. Download WordPress from wordpress.org
![image](https://user-images.githubusercontent.com/91480603/212140151-84b9a480-af9d-496e-97e4-bcdbb8025531.png)
11. Download the configuration files from the sample repository
![image](https://user-images.githubusercontent.com/91480603/212140220-0c6157ad-3c89-4015-8585-67ce0426f5c1.png)
12. Extract WordPress and change the name of the folder
![image](https://user-images.githubusercontent.com/91480603/212140276-4775efcb-21a9-442f-9dcd-f21cec47334d.png)
13. Extract the configuration files over the WordPress installation
![image](https://user-images.githubusercontent.com/91480603/212140354-879fe60e-0e65-4f1b-a266-3d9898b5a44e.png)

**Launch an Elastic Beanstalk environment**

1. 




