# CLD
# LAB 02: APP SCALING ON AMAZON WEB SERVICES

# PEDAGOGICAL OBJECTIVES

Deploy a web application in a scalable way using a two-tier architecture

Use virtual machine images to clone a web application onto additional virtual machine instances

Use a load balancer that is provided as cloud service

Performance-test a load-balanced web application

# TASKS

In this lab you will perform a number of tasks and document your progress in a lab report. Each task specifies one or more deliverables to be produced. Collect all the deliverables in your lab report. Give the lab report a structure that mimics the structure of this document.

You should have from the previous lab a micro instance running Ubuntu Server 16.04 LTS with Drupal7 installed. In the following we will refer to it as the Drupal master instance.

You will improve the Drupal site to make it scalable. Your site will be able to absorb traffic increases from new users by adding virtual machines that process requests in parallel. Following a two-tier architecture the business logic and presentation layer will be separated from the database layer so that the former can be replicated in multiple virtual machines. The database moves into Amazon's Relational Database Service (RDS) which provides automatic backup, data replication and failover.

Note: Not all deliverables get you the same number of points. Deliverables that only verify that you performed some instructions get fewer points, deliverables that ask questions that test your understanding and require thinking get more points.

# TASK 1: CREATE A DATABASE USING THE RELATIONAL DATABASE SERVICE (RDS)
In this task you will create a new RDS database that will replace the MySQL database currently used by Drupal.

Please read the document What Is Amazon Relational Database Service (Amazon RDS)? for reference. Once you have read the document, please perform the following steps:

Open the RDS console. To create the database in the same region as the Drupal master instance, switch the console to the region in which you created the Drupal master instance.

Create a Security Group with a name of the form yourlastname-Drupal-DB and open the TCP port on 3306 (MySQL default port).

Launch a DB instance: Click Launch DB Instance and provide the following answers (leave any field not mentioned at its default value):

Select engine
Select MySQL
Use case - Do you plan to use this database for production purposes?
Select Dev/Test - MySQL
Specify DB details
DB Instance Class: db.t1.micro ou db.t2.micro
Multi-AZ Deployment: No
Storage type: General Purpose (SSD)
Allocated Storage: 20 GB
Estimated monthly costs: Copy the estimated costs into the report. Update 2018-03-06: If the user interface doesn't show you the estimated costs, use the Simple Monthly Calculator instead.
DB Instance Identifier: yourlastname-Drupal
Master Username: root
Master Password: Invent a password and write it down.
Configure advanced settings
Public accessibility: Yes
VPC Security Group(s): Choose existing VPC security groups: yourlastname-Drupal-DB
Database name: (leave empty)
After launching the DB instance return to the instances view and wait for the DB instance to be created.

In the RDS console select the newly created DB instance and write down the Endpoint address.

Test whether the database can be reached from the Drupal master instance.

Log into the Drupal master instance.

Using the database's endpoint address (without the port number) and the master password you wrote down run the command:

mysql --host=endpoint_address --user=root --password=master_password
You should see a welcome message and the MySQL command line prompt mysql>. Type quit to exit.

Optional: On your local machine download and install the MySQLWorkbench administration tool from http://www.mysql.com/products/workbench/ and use it to connect to the database.

# DELIVERABLE 1:
Copy the estimated costs that were shown in the launch wizard into the report.

Compare the costs of your RDS instance to a continuously running EC2 instance of the same size using the AWS calculator. (Don't forget to uncheck the Free Usage Tier checkbox at the top.)

In a two-tier architecture the web application and the database are kept separate and run on different hosts. Imagine that for the second tier instead of using RDS to store the data you would create a virtual machine in EC2 and install and run yourself a database on it. If you were the Head of IT of a medium-size business, how would you argue in favor of using a database as a service instead of running your own database on an EC2 instance? How would you argue against it?

Copy the endpoint address of the database into the report.

# TASK 2: CONFIGURE THE DRUPAL MASTER INSTANCE TO USE THE RDS DATABASE
In this task you will migrate the content of the local MySQL database to the newly created RDS database and change Drupal's database configuration to use the RDS database.

CHANGE DRUPAL'S DATABASE CONFIGURATION
Log into the Drupal master instance.

Stop the web server by typing:

sudo systemctl stop apache2
To change Drupal's configuration parameters to point to the RDS database and to create a new user drupal7 in the RDS database run the command sudo dpkg-reconfigure drupal7. Provide the following answers:

Reinstall database for drupal7? Yes
Database type to be used for drupal7: mysql
Connection method for MySQL database of drupal7: tcp/ip
Host name of a remote MySQL server: enter the endpoint address of the RDS database (without the port number)
Port number for the MySQL service: (leave empty)
Name of the database's administrative user: root
Password of the database's administrative user: the master password of the RDS database
Username for drupal7: drupal7
MySQL database name for drupal7: drupal7
The command should complete without error messages.

Open the file /etc/drupal/7/sites/default/dbconfig.php and write down the database password generated by the previous command for user drupal7.

To make the authentication in the RDS database less strict connect to the database, create an additional user 'drupal7'@'%' and give this user access rights to the drupal7 database. Perform the following steps. Launch the mysql command to connect to the RDS database (it's the same command as the verification step after creating the RDS database):

   mysql --host=endpoint_address --user=root --password=master_password
You should see a welcome message and the MySQL command line prompt mysql>.

On the mysql> command prompt run the following three commands where drupal7_password is the database password for user drupal7 you wrote down earlier:

CREATE USER 'drupal7'@'%' IDENTIFIED BY 'drupal7_password';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES ON drupal7.* TO 'drupal7'@'%' IDENTIFIED BY 'drupal7_password';
FLUSH PRIVILEGES;
Disconnect from the RDS database by typing quit and verify that user drupal7 can connect to the database by typing

   mysql --host=endpoint_address --user=drupal7 --password=drupal7_password
MIGRATE THE DATABASE CONTENT TO RDS
To migrate the data currently stored in the MySQL database of the Drupal master instance into the RDS database, perform the following steps.

Log into the Drupal master instance.

Type the following command to migrate the database content from the local MySQL database to the RDS database.

mysqldump --add-drop-table --user=drupal7 --password=drupal7_password drupal7 |
  mysql --host=endpoint_address --user=drupal7 --password=drupal7_password drupal7
The command should complete without errors.

Start the web server by typing:

sudo systemctl start apache2
Verify the database configuration by navigating with your browser to the Drupal home page at http://hostname/drupal7/.

If you receive an error message "The default settings file does not exist" or "The settings file is not writable" run the following commands:

cd /etc/drupal/7/sites/default
sudo cp -a settings.php default.settings.php
sudo chown www-data:www-data settings.php
# DELIVERABLE 2:
Copy the content of the generated file /etc/drupal/7/sites/default/dbconfig.php into the report.

# TASK 3: CREATE A CUSTOM VIRTUAL MACHINE IMAGE
Now that you have properly configured the Drupal master instance, you will save it into a virtual machine image. This image will be used later to create new instances with the exact same configuration.

In the EC2 console bring up the Instances panel and select the Drupal master instance.

Bring up the context menu and select Create Image. Provide the following answers (leave any field not mentioned at its default value):

Image Name: yourlastname Drupal.
Image Description: Drupal connected to RDS database.
Click Create Image. The instance will shut down temporarily and the image will be created.

In the console bring up the AMIs panel. Wait until the status of the AMI goes from pending to available.

# DELIVERABLE 3:
Copy a screenshot of the AWS console showing the AMI parameters into the report.

# TASK 4: CREATE A LOAD BALANCER
In this task you will create a load balancer in AWS that will receive the HTTP requests from clients and forward them to the Drupal instances.

In the EC2 console bring up the Load Balancers panel. Click on Create Load Balancer. Provide the following answers (leave any field not mentioned at its default value):

Choose : Classic Load Balancer
Define Load Balancer
Load Balancer Name: yourlastname-Drupal.
Configure Health Check
Health Check Interval: 10 seconds
Healthy Threshold: move down to 2.
Add EC2 Instances
Select the Drupal master instance.
In the EC2 console select the newly created load balancer. Write down its DNS Name (A Record).

In the EC2 console select the newly created load balancer. In the lower half of the panel, click on the Instances tab. Watch the status of the instance go from Out of Service to In Service.

Log into the Drupal master instance. Examine the Apache access log /var/log/apache2/access.log to see who is connecting to the web server**. **

# DELIVERABLE 4:
On your local machine resolve the DNS name of the load balancer into an IP address using the nslookup command (Linux or Windows). Write the DNS name and the resolved IP Address(es) into the report.

In the Apache access log identify the health check accesses from the load balancer and copy some samples into the report.

# TASK 5: LAUNCH A SECOND INSTANCE FROM THE CUSTOM IMAGE
In this task you will launch a second Drupal instance and connect it to the load balancer.

Using the custom virtual machine image you created earlier launch a second instance. Make sure that the instance launches in a different availability zone than the first instance.

Make sure that the instance works correctly by navigating with your browser to the Drupal home page of the new instance at http://hostname/drupal7/.

Using the AWS console connect the instance to the load balancer. Watch the status of the instance go from Out of Service to In Service.

# DELIVERABLE 5:
Draw a diagram of the setup you have created showing the components (instances, database, load balancer, client) and how they are connected. Include the security groups as well.

Using the Simple Monthly Calculator calculate the monthly cost of this setup. You can ignore traffic costs. (Make sure you don't forget to include a component in the calculation. Also don't forget to uncheck the Free Usage Tier checkbox at the top.)

# TASK 6: TEST THE DISTRIBUTED APPLICATION
In this task you will test the distributed application with a load generator and use the monitoring tools of the AWS console.

Download and install on your local machine the JMeter tool from http://jmeter.apache.org/.

Open two terminal windows side-by-side and, using SSH, log into each instance. Bring up a continuous display of the Apache access log by running the command sudo tail -f /var/log/apache2/access.log.

Using the AWS console, enable detailed (1-minute interval) monitoring of the two instances: Select an instance and click on the Monitoring tab. Click on Enable Detailed Monitoring.

Follow the instructions on http://fredpuls.com/site/softwaredevelopment/java/test/test_jmeter_quick_start.htm to create a simple test plan. Specify the load balancer as the target for the HTTP requests. Run a test.

Observe which of the instances gets the load. Increase the load and re-run the test. Observe response times and time-outs. Repeat until you see inacceptable response times and/or time-outs.

Immediately after having created a high load for the site, re-run the nslookup command to resolve the DNS name of the load balancer into IP addresses to see if there are any changes.

# DELIVERABLE 6:
Document your observations. Include screenshots of JMeter and the AWS console monitoring output.

When you resolve the DNS name of the load balancer into IP addresses while the load balancer is under high load what do you see? Explain.

Did this test really test the load balancing mechanism? What are the limitations of this simple test? What would be necessary to do realistic testing?

Note: In this task it is not important that you reproduce exactly an expected behavior of the load balancer. Your load generator (your local machine and your local network) may not behave always the same, the ELB may not always behave the same and you may get results different from your colleagues. What is important however is that you show that you understand the distributed system that you are testing and that you know how to observe the performance of its components with the monitoring functions provided by AWS. You should show that you are able to correlate the performance with different loads generated by JMeter and draw conclusions.

# TASK 7: RELEASE CLOUD RESOURCES
Once you have completed this lab release the cloud resources to avoid unnecessary charges:

Terminate the EC2 instances.
Delete the attached EBS volumes.
Delete the Elastic Load Balancer.
Delete the RDS instance.
Delete any leftover Elastic IP Addresses from the previous lab.
