# CLD 
## LAB 02: APP SCALING ON AMAZON WEB SERVICES

#### By Ali Miladi & Dany Tchente

## TASK 1: SET UP

#### 1- Copy the estimated costs that were shown in the launch wizard into the report.
**Picture 1:** 

![MacDown Screenshot](/Users/mac/Desktop/semestre_2/CLD/Laboratoire/Lab02/Screen\ Shot\ 2018-03-06\ at\ 11.57.06.png)

**Picture 2:** 

![MacDown Screenshot](/Users/mac/Desktop/semestre_2/CLD/Laboratoire/Lab02/Screen\ Shot\ 2018-03-06\ at\ 11.57.34.png)

#### 2- Compare the costs of your RDS instance to a continuously running EC2 instance of the same size using the AWS calculator. (Don't forget to uncheck the Free Usage Tier checkbox at the top.)

**Picture 3:** 

![MacDown Screenshot](/Users/mac/Desktop/semestre_2/CLD/Laboratoire/Lab02/Screen\ Shot\ 2018-03-06\ at\ 11.55.34.png)

**Picture 4:** 

![MacDown Screenshot](/Users/mac/Desktop/semestre_2/CLD/Laboratoire/Lab02/Screen\ Shot\ 2018-03-06\ at\ 11.55.52.png)

bla bla bla

#### 3- In a two-tier architecture the web application and the database are kept separate and run on different hosts. Imagine that for the second tier instead of using RDS to store the data you would create a virtual machine in EC2 and install and run yourself a database on it. If you were the Head of IT of a medium-size business, how would you argue in favor of using a database as a service instead of running your own database on an EC2 instance? How would you argue against it?

#### 4- Copy the endpoint address of the database into the report.

**Picture 5:** 

![MacDown Screenshot](/Users/mac/Desktop/semestre_2/CLD/Laboratoire/Lab02/Screen\ Shot\ 2018-03-06\ at\ 12.14.09.png)

##TASK 2: CONFIGURE THE DRUPAL MASTER INSTANCE TO USE THE RDS DATABASE

####1- What is the smallest and the biggest instance type (in terms of virtual CPUs and memory) that you can choose from when creating an instance? 

The smallest instance type: ***t2.nano***

The Biggest instance type: ***i3.16xlarge***

####2- How long did it take for the new instance to get into the running state? 
few than one minute.

####3- The public DNS name of the instance:
ec2-35-158-156-35.eu-central-1.compute.amazonaws.com

####4- The result **hostname** and **uname -a** commands:

***hostname :*** ip-172-31-43-164

***uname -a:*** Linux ip-172-31-43-164 4.4.0-1049-aws #58-Ubuntu SMP Fri Jan 12 23:17:09 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux

**Picture 1:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 04.14.18.png) 

####5- Try to ping the instance from your local machine. 

***5.1-*** What do you see? 

***The ping does not pass*** because the rules in the firewall accept only connexion type SSH from TCP protocol on port 22.





**Picture 2:** ![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 04.03.18.png ) 

***5.2-*** Change the configuration to make it work: 

We add a new rule to allow ping ***(All ICMP-IPV4)***

**Picture 3:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 04.11.59.png) 

***5.3-*** Ping the instance, record 5 round-trip times:

**Picture 4:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 04.57.54.png)

***5.4-*** Determine the IP address seen by the operating system in the EC2 instance by running the ifconfig command

**Picture 5:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 05.03.01.png)

What type of address is it (172.31.43.164)? ***It is a private address.***

Or this address (35.158.156.35) is ***a public address*** that can be reach from outside.

How do you explain that you can successfully communicate with the machine? 
***The reason is the address translation by NAT (Network Address Translation) who match the private IP address from the host to the public IP address.***

##TASK 3: INSTALL A WEB APPLICATION

####1- Screenshot of the page we created in Drupal

**Picture 6:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 06.12.16.png)

####2- The Elastic IP Address we created

IP Address: ***18.196.177.152***

**Picture 7:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 06.13.27.png)

####3- Why is it a good idea to create an Elastic IP Address for a web site (our web application)? Why is it not sufficient to hand out as URL for the web site the public DNS name of the instance?

-> When we want to mask a failure of an instance by rapidly remapping the address to another instance in our account.

-> When we want to enable communication with the internet (reachable from the internet).




##TASK 4: CREATE AN ADDITIONAL EBS VOLUME AND USE SNAPSHOTS

####1- What is the size of the EBS Volume?

The size is ***8GiB***.

####2- What capacity does the operating system see and how much space is left?

What capacity does the operating system see? ***7.7GiB***

how much space is left? ***6.3GiB***


**Picture 8:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 06.43.46.png)


####3- Copy the Availability Zone of your Instance and Volume into the report.
Availability Zone and Volume: ***eu-central-1b***

####4- Copy the available space after formatting and mounting into the report.

Available space: ***908M***

**Picture 9:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-21\ at\ 07.28.18.png)

##TASK 5: PERFORMANCE ANALYSIS OF YOUR INSTANCE (OPTIONAL)

####1- Provide the URLs of the Geekbench results for the EC2 instance and your local machine.
EC2 Instance : ***http://browser.primatelabs.com/geekbench3/8561964***

Local machine : ***http://browser.primatelabs.com/geekbench3/8561983***

####2- Provide system information about the EC2 instance.

**Picture 10:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 12.40.44.png)

####3- Provide the single-core and multi-core performance scores for overall, integer, floating-point and memory performance of the EC2 instance.

***3.1-*** single-core and multi-core performance scores for overall

**Picture 11:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 12.50.13.png)

***3.2-*** integer


**Picture 12:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 12.43.55.png)

***3.3-*** floating-point


**Picture 13:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 12.44.15.png)

***3.4-*** memory performance


**Picture 14:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 12.44.25.png)

####4- Provide system information about your local machine.

**Picture 15:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 13.06.48.png)

####5- Provide the single-core and multi-core performance scores for overall, integer, floating-point and memory performance of your local machine.

***5.1-*** single-core and multi-core performance scores for overall

**Picture 16:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 13.06.40.png)

***5.2-*** integer, floating-point and memory performance

**Picture 17:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 13.06.56.png)

**Picture 18:** 
![MacDown Screenshot](/Users/mac/Desktop/Screen\ Shot\ 2018-02-24\ at\ 13.07.07.png)

####6- Compare the overall scores of the two machines.

we observe that the overall scores on our local machine is upper than the scores on our EC2 instances.

##TASK 6: RESOURCE CONSUMPTION AND PRICING

####1- How much does your instance (including disk) cost per hour? What was its cost for this lab?

***1.1-*** Instance (including disk) cost per hour : ***$0.0134 per hour in addition to $0.0013 per hour for 8GiB***

***Total : $0.0147 per hour***

***1.2-*** Cost for this lab : ***Monthly
Cost $0.90 for 8 hours and 8GiB EBS volumes***

####2- Change the parameters to an instance that runs continuously during the whole month. Note the total cost.

Total cost : ***$10.39***

####3- If you buy a 1 TB SSD disk at Digitec it currently costs CHF 289. How much does a 1 TB ESB Volume cost for a month?

1 TB ESB Volume cost for a month : ***$121.86***

##TASK 7: CLEANUP

***Done***
