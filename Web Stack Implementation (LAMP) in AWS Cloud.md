 # Deploying a LAMP stack website in AWS Cloud!

 In this project, i deployed a LAMP stack website in the AWS cloud. Below i describe all the steps i followed to complete the project:

 ## Requirements :

 1. AWS account
 2. Virtual Server with Ubuntu OS
 3. Basic Linux Understanding

 ### Step 1 : Launch a new EC2 instance and connect it via SSH.

 Launch a new EC2 machine, use Ubuntu OS  and SSH into EC2 instance using mobaxterm. We use use the SSH protocol to establish connectivity between computers, in this case between YOU (Client) and EC2 as shown in the picture below:

![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/99338e65-37a0-4354-afd5-301256484581)

Use this video for this step : https://youtu.be/hUoYVZlioRc?si=HgUPTV-TCLxLTyFE

Connect to the instance by copying and pasting this on mobaxterm :

```
ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>

```

### Step 2 : Installing apache and updating the firewall.

Apache HTTP Server is the most widely used web server software. We will use apache to host our website.

Install Apache using Ubuntu’s package manager ‘apt’:

```
#update a list of packages in package manager
sudo apt update

#run apache2 package installation
sudo apt install apache2

```
 
#### To verify that apache2 is running as a Service in our OS, use following command

```
sudo systemctl status apache2

```

If its green and running, the web serveer is launched successfully.

To test how our Apache HTTP server can respond to requests from the Internet, copy the Publi IP of your instance and paste it on your browsser.

### Step 3 : Installing  MySQL.

Now that we have a web server up and running, we need to install a Database to be able to store and manage data for our site, we install MySQL.

Use ‘apt’ to acquire and install this software:

```
$ sudo apt install mysql-server

```

When prompted, confirm installation by typing Y, and then ENTER.

When the installation is finished log in to the MySQL console by typing:

```
$ sudo mysql

```

You should see output like this:

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```

This means MySQL is installed and to exit it use :


```
mysql> exit

```

Now to secure your installation, run this and you wil be prompted to answer questions. (answer : n,y,y,y)

```
$ sudo mysql_secure_installation

```

### Step 4 : Installing  PHP.

Now we have Apache installed to serve our content and MySQL installed to store and manage the data. We need PHP to process the code to display dynamic content to the end users. 

To install PHP we install these three packages at once, run :

```
sudo apt install php libapache2-mod-php php-mysql

```

Once the installation is finished, you can run the following command to confirm your PHP version:

```
php -v

```

At this stage, our LAMP stack is installed and fully operational.

1.Linux (Ubuntu)
2.Apache HTTP Server
3.MySQL
4.PHP

### Step 5 : Installing  MySQL.
