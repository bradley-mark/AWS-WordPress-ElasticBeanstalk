# AWS-WordPress-Elastic-Beanstalk

Deploying a high-availability WordPress website with an external Amazon RDS database to Elastic Beanstalk

**Overview and High Level Design**

Use Default VPC

**Elastic Beanstalk created resources**
  
**EC2 instance** – An Amazon Elastic Compute Cloud (Amazon EC2) virtual machine configured to run web apps on the platform that you choose.

Each platform runs a specific set of software, configuration files, and scripts to support a specific language version, framework, web container, or combination of these. Most platforms use either Apache or NGINX as a reverse proxy that sits in front of your web app, forwards requests to it, serves static assets, and generates access and error logs.

**Instance security group** – An Amazon EC2 security group configured to allow inbound traffic on port 80. This resource lets HTTP traffic from the load balancer reach the EC2 instance running your web app. By default, traffic isn't allowed on other ports.

**Load balancer** – An Elastic Load Balancing load balancer configured to distribute requests to the instances running your application. A load balancer also eliminates the need to expose your instances directly to the internet.

**Load balancer security group** – An Amazon EC2 security group configured to allow inbound traffic on port 80. This resource lets HTTP traffic from the internet reach the load balancer. By default, traffic isn't allowed on other ports.

**Auto Scaling group** – An Auto Scaling group configured to replace an instance if it is terminated or becomes unavailable.

**Amazon S3 bucket** – A storage location for your source code, logs, and other artifacts that are created when you use Elastic Beanstalk.

**Amazon CloudWatch alarms** – Two CloudWatch alarms that monitor the load on the instances in your environment and that are triggered if the load is too high or too low. When an alarm is triggered, your Auto Scaling group scales up or down in response.

**AWS CloudFormation stack** – Elastic Beanstalk uses AWS CloudFormation to launch the resources in your environment and propagate configuration changes. The resources are defined in a template that you can view in the AWS CloudFormation console.

**Domain name** – A domain name that routes to your web app in the form *subdomain.region.elasticbeanstalk.com*.


# Launch a DB instance in Amazon RDS in default VPC

**Create DB Instance**

1. Open the RDS console **https://console.aws.amazon.com/rds/home**
2. In the navigation pane, choose **Databases**
3. Choose **Create database**
4. Choose **Standard Create**
5. Choose Engine options: **MySQL** - Engine Version **MySQL 8.0.28**
6. Choose Templates - **Dev/Test**
7. Under **Availablity & durability**, choose **Multi-AZ DB instance** 
8. Under **Settings** for **DB instance identifier**, type ebdb
9. Under **Credential Settings** enter the **Master username** ** and **Master password** ** - Note!
10. Under **Instance configuration** select **DB instance class** to **Burstable classes (includes t classes)** select **db.t3.micro**
11. Under **Connectivity**, **Database authentication**, **Monitoring** keep all defaults
12. Important click **Additional configuration** > **Database options** > **Initial database name** type ebdb
13. Choose **Create database**

**Create Linux Instance**

1. Open the EC2 console **https://us-east-1.console.aws.amazon.com/ec2/**
2. Choose **Launch instance**
3. Under **Name and tags**, type **Linux Server**
4. Under **Application and OS Images (Amazon Machine Image)** select, **Amazon Linux** - **Amazon Linux 2 Kernel 5.10 AMI 2.0.20221210.1 x86_64 HVM gp2 - 64-bit (x86)**
5. Under **Instance type** select, **t2.micro**
6. Under **Key pair (login)**, select key pair name
7. Keep all other defaults
8. Choose **Launch Instance**
9. Select **Instance** and **Connect**

**Download WordPress**

1. Download WordPress from wordpress.org

![image](https://user-images.githubusercontent.com/91480603/212995977-29f749a3-3aed-4d73-ba70-caba8d4d4e4e.png)

2. Download the configuration files from the sample repository

![image](https://user-images.githubusercontent.com/91480603/212995855-94e5d28f-fe8a-4895-835d-3c7f0fcbc09e.png)

3. Extract WordPress and change the name of the folder

![image](https://user-images.githubusercontent.com/91480603/212996449-a32d38bc-4c54-40c1-86bf-61d1607bbdc2.png)

![image](https://user-images.githubusercontent.com/91480603/212996631-15cdab40-3f98-4d50-be99-1dce9b9c3100.png)

4. Extract the configuration files over the WordPress installation

![image](https://user-images.githubusercontent.com/91480603/212997164-b6c12d10-7f9f-400e-abe9-cc241052083c.png)

**Launch an Elastic Beanstalk environment**

1. Open the Elastic Beanstalk console using this preconfigured link:
**https://console.aws.amazon.com/elasticbeanstalk/home#/newApplication?applicationName=tutorials&environmentType=LoadBalanced**
2. For **Platform**, select the platform and platform branch that match the language used by your application - **PHP 8.0 running on 64bit Amazon Linux 2** ** Platform version **3.5.3 (Recommended)**
3. For **Application code**, choose **Sample application**
4. Choose **Review and launch**
5. Review the available options. Choose the available option you want to use, and when you're ready, choose **Create app**

**Configure security groups and environment properties**

**Modify the inbound rules on the security group that's attached to your RDS instance**

1. Open the RDS console https://console.aws.amazon.com/rds/home
2. Choose **Databases** 
3. Choose the name of your DB instance to view its details
4. In the **Connectivity** section, make a note of the Subnets, Security groups, and Endpoint that are displayed on this page
5. Under **Security**, you can see the security group that's associated with the DB instance. Open the link to view the security group in the Amazon EC2 console
6. In the security group details, choose **Inbound rules**
7. Choose **Edit inbound rules**
8. Choose **Add Rule**
9. For **Type**, choose the DB engine that your application uses - Choose **MYSQL/Aurura**
10. For **Source**, type **sg-** to view a list of available security groups. Choose the security group that's associated with the Auto Scaling group that's used with your Elastic Beanstalk environment. This is so that Amazon EC2 instances in the environment can have access to the database
11. Choose **Save**

**Add the security group of your DB instance to your running environment**

1. Open the Elastic Beanstalk console **https://console.aws.amazon.com/elasticbeanstalk**
2. In the navigation pane, choose **Environments**, and then choose the name of your environment from the list
3. In the navigation pane, choose **Configuration**
4. In the **Instances** configuration category, choose **Edit**
5. Under **EC2 security groups**, choose the security group to attach to the instances (e.g **default sg-**) in addition to the instance security group that Elastic Beanstalk creates
6. Choose **Apply** at the bottom of the page
7. Read the warning, and then choose **Confirm**

**Configure environment properties for an Amazon RDS DB instance**

1. Open the Elastic Beanstalk console **https://console.aws.amazon.com/elasticbeanstalk**
2. In the navigation pane, choose **Environments**, and then choose the name of your environment from the list
3. In the navigation pane, choose **Configuration**
4. In the **Software** configuration category, choose **Edit**
5. In the **Environment properties** section, define the variables that your application reads to construct a connection string. 
6. Choose **Apply**

![image](https://user-images.githubusercontent.com/91480603/212999355-a8d5a37e-1a80-4b13-933a-36733cda8e93.png)

**Configure and deploy your application**

Login to Linux Instance with WordPress installed

**Install tree**

![image](https://user-images.githubusercontent.com/91480603/212999810-bd4f0c5b-05c0-4431-b4b1-15307d87d323.png)

Verify that the structure of your wordpress-beanstalk folder is correct, as shown

![image](https://user-images.githubusercontent.com/91480603/213000041-6ad9cc0f-a7a2-41e0-99e6-df7f22793b7e.png)

The customized wp-config.php file from the project repo uses the environment variables that you defined. 

The .ebextensions folder contains configuration files that create additional resources within your Elastic Beanstalk environment.

**To update configuration files and create a source bundle**

1. Modify the configuration files 

*.ebextensions/dev.config* – Restricts access to your environment to protect it during the WordPress installation process. Replace the placeholder IP address near the top of the file with the public IP address of the computer you'll use to access your environment's website to complete your WordPress installation.

![image](https://user-images.githubusercontent.com/91480603/213000910-f5bddacf-f421-48f4-a6b7-5081b9e5d546.png)

![image](https://user-images.githubusercontent.com/91480603/213015680-51902eca-9baf-4821-a5df-6bab57946365.png)

*.ebextensions/efs-create.config* – Creates an EFS file system and mount points in each Availability Zone/subnet in your VPC. Identify your default VPC and subnet IDs in the Amazon VPC console

![image](https://user-images.githubusercontent.com/91480603/213001033-f7cfdcdb-ae03-4786-8594-29943561e396.png)

![image](https://user-images.githubusercontent.com/91480603/213015714-ae17d2f6-0f97-435f-9c03-7907a5d75695.png)

2. Create a source bundle containing the files in your project folder. The following command creates a source bundle named *wordpress-beanstalk.zip*.

![image](https://user-images.githubusercontent.com/91480603/213001333-c22c1f6b-9aba-4430-9bd8-05b4650e14b2.png)

Download wordpress-beanstalk.zip from EC2 to local disk using WinSCP

**Deploy source bundle**

1. Open the Elastic Beanstalk console **https://console.aws.amazon.com/elasticbeanstalk**
2. In the navigation pane, choose **Environments**, and then choose the name of your environment from the list
3. On the environment overview page, choose **Upload and deploy**

![image](https://user-images.githubusercontent.com/91480603/213015839-72e47b99-ff59-4546-873d-589ba224e21b.png)

5. Use the on-screen dialog box to upload the source bundle
6. Choose **Deploy**

![image](https://user-images.githubusercontent.com/91480603/213015105-49c31353-af11-431d-8d0a-1c6bd3248834.png)

**Install WordPress**

1. Choose the **Go to environment** to open your website URL in a browser. You are redirected to a WordPress installation wizard
2. Perform a standard installation. 

![image](https://user-images.githubusercontent.com/91480603/213015351-1dc18808-7cb4-4641-9e91-11b03e0ebfa9.png)

**Additional Actions**

**Update keys and salts**

The WordPress configuration file wp-config.php also reads values for keys and salts from environment properties. 
Currently, these properties are all set to test by the wordpress.config file in the .ebextensions folder.

1. Open the Elastic Beanstalk console **https://console.aws.amazon.com/elasticbeanstalk**
2. Choose **Environments**, and then choose the name of your environment from the list
3. Choose **Configuration**
4. Under **Software**, choose **Edit**
5. In the **Environment properties**, modify the following properties:

![image](https://user-images.githubusercontent.com/91480603/213006525-9107ca9d-bb4f-4a0f-8704-d33b604e2845.png)

6. Choose **Apply**

**Remove access restrictions**

The sample project includes the configuration file loadbalancer-sg.config. It creates a security group and assigns it to the environment's load balancer, using the IP address that you configured in dev.config. It restricts HTTP access on port 80 to connections from your network. Otherwise, an outside party could potentially connect to your site before you have installed WordPress and configured your admin account.

**To remove the restriction and update your environment**
1. Delete the .ebextensions/loadbalancer-sg.config file from your project directory.

![image](https://user-images.githubusercontent.com/91480603/213013104-bbf0aeee-cdd3-4845-8f52-05991128f6ee.png)

2. Create a new source bundle

![image](https://user-images.githubusercontent.com/91480603/213013213-c98bf80d-49ec-4482-a13e-ee4e8c16c093.png)

3. **Upload and deploy** new source bundle - v2

**To configure your environment's Auto Scaling group for high availability**

1. Open the Elastic Beanstalk console **https://console.aws.amazon.com/elasticbeanstalk**
2. Choose **Environments**, and then choose the name of your environment from the list
3. Choose **Configuration**
4. Under **Capacity**, choose **Edit**
5. In the **Auto scaling group**, set **Min instances to 2**
6. Choose **Apply**

**Clean up**

Select and terminate both the Elastic Beanstalk environment and the RDS DB instance(s)


**Troubleshooting**

*PHP Compatibility and WordPress*
**https://make.wordpress.org/core/handbook/references/php-compatibility-and-wordpress-versions/**

![image](https://user-images.githubusercontent.com/91480603/213018103-c2fcbc8b-2cd6-43bc-88a2-f787b7d80325.png)

**Test check RDS DB connection using Linux server**

**Create testconnection.php file**

![image](https://user-images.githubusercontent.com/91480603/213017065-5e555282-ba47-4dbd-91ab-dff9c255f704.png)

Replace hostname, username, password

Copy/paste save file testconnection.php in /var/www/html

<?php
$link = mysqli_connect('hostname', 'username', 'password');
if (!$link) {
die('Could not connect: ' . mysqli_error());
}
echo 'Connected successfully';
mysqli_close($link);
?>

![image](https://user-images.githubusercontent.com/91480603/213016897-b68f25c7-28b4-4e3d-b350-71fc6bb9ad6e.png)

**Create phpinfo.php file**

![image](https://user-images.githubusercontent.com/91480603/213017725-7d3cc8d6-2856-47b8-b892-1a4f664ee351.png)

![image](https://user-images.githubusercontent.com/91480603/213017775-af2c41c3-1400-478e-bd33-8c77079f1a80.png)

**Check RDS DB Instance has DB name**

![image](https://user-images.githubusercontent.com/91480603/213017918-fee407af-edb4-4a1d-95df-1982d6c42036.png)



