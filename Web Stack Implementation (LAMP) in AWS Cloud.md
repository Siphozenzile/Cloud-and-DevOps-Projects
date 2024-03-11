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

### Step 5 : Creating a virtual host for our website using Apache.

We will set up a domain called projectlamp.

Create the directory for projectlamp using ‘mkdir’ command as follows:

```
sudo mkdir /var/www/projectlamp

```

Next, assign ownership of the directory with your current system user:

```
sudo chown -R $USER:$USER /var/www/projectlamp

```

Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):

```
sudo vi /etc/apache2/sites-available/projectlamp.conf

```
This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

```
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

```
You can now use a2ensite command to enable the new virtual host:

```
sudo a2ensite projectlamp

```
We want to disable the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. To disable Apache’s default website use a2dissite command , type:


```
sudo a2dissite 000-default

```
Finally, reload Apache so these changes take effect:

```
sudo systemctl reload apache2
```
Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:

```
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' 
$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```

## Step 6 : Enable PHP on the Website.

To enbable PHP on the website, we will need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

```
sudo vim /etc/apache2/mods-enabled/dir.conf

```
```
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>

```
After saving and closing the file, you will need to reload Apache so the changes take effect:

```
sudo systemctl reload apache2

```

Now that we have a custom location to host our website’s files and folders, we’ll create a PHP test script to confirm that Apache is able to handle and process requests for PHP files.

Create a new file named index.php inside your custom web root folder:

```
vim /var/www/projectlamp/index.php

```
This will open a blank file. Add the following text, which is valid PHP code, inside the file:

```
<?php
phpinfo();

```
When finished, save and close the file, refresh the page and you will see a page similar to this:
![Screenshot (29)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/412de712-4040-46bc-a157-9510de0663fd)





