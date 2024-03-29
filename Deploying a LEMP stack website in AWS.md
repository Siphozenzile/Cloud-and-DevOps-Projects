# LEMP Stack Implementation in AWS.

Deployment of a LAMP stack website in the AWS cloud. Below i describe all the steps i followed to complete this project:

## Requirements :

1. AWS account
2. Virtual Server with Ubuntu OS
3. Basic Linux Understanding

## Step 1 : Launch a new EC2 instance and connect it via SSH.
Launch a new EC2 machine, use Ubuntu OS and SSH into EC2 instance using mobaxterm. We use use the SSH protocol to establish connectivity between computers. in this case between YOU (Client) and EC2 as shown in the second picture below:

![Screenshot (52)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/42da871f-9136-4ed8-930e-29186af5b640)

Connect to the instance by copying and pasting this on mobaxterm :

```
ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>
```
You want to achieve the this connectivity as shown below :

![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/1b735b52-7e67-4c6f-8537-63293f985fd9)

Use this video for this step : https://youtu.be/hUoYVZlioRc?si=HgUPTV-TCLxLTyFE

## Step 2 : Installing Nginx web server 

In order to display our website to site visitors, we are going to employ Nginx web server. We’ll use the apt package manager to install this package.

```
sudo apt update
sudo apt install nginx
```
To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

```
sudo systemctl status nginx
```
Copy your Public Ip and paste on yoour browser, you should get the following page :

![Screenshot (36)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/4f4704c6-bc65-45f7-98e4-7b232974dae4)

## Step 3 : Installing MySQL

Now that we have a web server up and running, we need to install a Database Management System (DBMS) to be able to store and manage data. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

Use ‘apt’ to acquire and install this software:

```
$ sudo apt install mysql-server
```

When the installation is finished, log in to the MySQL console by typing:

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
To secure installation, run this :

```
$ sudo mysql_secure_installation
```
When you’re finished, test if you’re able to log in to the MySQL console by typing:

```
$ sudo mysql -p
```
To exit the MySQL console, type:

```
mysql > exit
```
Now, MySQL is installed and secured. Next, we will install PHP, the final component in the LEMP stack.

## Step 4 : Installing PHP

We have Nginx installed to serve our content and MySQL installed to store and manage the data. Now we will install PHP to process code and generate dynamic content for the web server.

To install, we install these 2 packages at once:
```
sudo apt install php-fpm php-mysql
```

When prompted, type Y and press ENTER to confirm installation.

We now have PHP components installed. Next, we will configure Nginx to use them.

## Step 5 : Configuring Nginx to use PHP processor

When using the Nginx web server, we can create server blocks to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use projectLEMP as an example domain name

Create the root web directory for your_domain as follows:

```
sudo mkdir /var/www/projectLEMP
```

Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:
```
sudo nano /etc/nginx/sites-available/projectLEMP
```
Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano:
```
sudo nano /etc/nginx/sites-available/projectLEMP
```

This will create a new blank file. Paste in the following bare-bones configuration:
```
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```
Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:
```
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:
```
sudo unlink /etc/nginx/sites-enabled/default
```
When you are ready, reload Nginx to apply the changes
```
sudo systemctl reload nginx
```
Your new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:
```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP'
$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
Now to your browser and paste IP address,the following shoould be the output.

![Screenshot (53)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/09ed4836-26b4-4de9-a1c1-d5d216c14239)

## Step 6 : Configuring Nginx to use PHP processor

In this step we want to test if Nginx can correctly hand .php files off to our PHP processor.

We do this by creating a test PHP file in document root. Open a new file called info.php within your document root in your text editor:

```
sudo nano /var/www/projectLEMP/info.php
```
Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:

```
<?php
phpinfo();
```
We can now access this page in our web browser by visiting the domain name or public IP address that we’ve set up in Nginx configuration file, followed by /info.php: 

Output :

![Screenshot (54)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/3a9e26be-1577-4e40-8587-04bc50c46483)


