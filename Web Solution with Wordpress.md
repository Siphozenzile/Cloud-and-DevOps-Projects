# Configure Linux Storage Subsystem and Deploy a Web Solution using Wordpress

In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

## Three-tier Architecture 

Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.

![image](https://github.com/user-attachments/assets/5c811dce-1ae0-4f93-ba2e-28bcbbb2a843)

1. Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
2. Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
3. Data Access or Management Layer (DAL): This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server

## Requirements(3-Tier Setup) :

1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

## Step 1 : Prepare a Web Server. 

1. Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB. Attach the EBS Volumes to your EC2 instance.

 ![Screenshot (461)](https://github.com/user-attachments/assets/8541026a-1a99-4c51-a6c1-5f45718a5f83)

 ![Screenshot (449)](https://github.com/user-attachments/assets/543cac5e-0f58-4116-97c6-fb4b8bb89842)

2. Open up the Linux terminal to begin configuration ( ssh into EC2 instance via putty or git bash)
3. Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdd, xvde, xvdf.

  ![Screenshot (450)](https://github.com/user-attachments/assets/d720e551-67b2-4f90-bb20-7fb3737c0618)

4.  Use df -h command to see all mounts and free space on your server.
5.  Use gdisk utility to create a single partition on each of the 3 disks.

``` sudo gdisk /dev/xvdd, sudo gdisk /dev/xvde, sudo /dev/xvdf ```

![partitions](https://github.com/user-attachments/assets/5b1688e3-ce9a-4b2f-8a8d-5dd6e75a4308)

6. Install lvm2 package using sudo yum install lvm2. Run sudo lvmdiskscan command to check for available partitions.

Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.

   ![Screenshot (453)](https://github.com/user-attachments/assets/93115636-2edd-4555-9ff7-cb387855ec74)

7. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM.

   ``` sudo pvcreate /dev/xvdd1,  sudo pvcreate /dev/xvde1, sudo pvcreate /dev/xvdf1 ```

8. Verify that your Physical volume has been created successfully by running sudo pvs

   ![physical volumes](https://github.com/user-attachments/assets/76f85c63-a012-485e-b0da-eb48051c13d4)

9. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

``` sudo vgcreate webdata-vg /dev/xvdd1 /dev/xvde1 /dev/xvdf1 ```

10. Verify that your VG has been created successfully by running

``` sudo vgs ```
11. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

``` sudo lvcreate -n apps-lv -L 14G webdata-vg ```
``` sudo lvcreate -n logs-lv -L 14G webdata-vg ```

12. Verify that your Logical Volume has been created successfully by running.

    ``` sudo lvs ```

![image](https://github.com/user-attachments/assets/bae624d6-bcfe-4364-9a92-1f49e9eb4e22)

13. sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk.

``` sudo vgdisplay -v #view complete setup - VG, PV, and LV ```
``` sudo lsblk ```

14. Use mkfs.ext4 to format the logical volumes with ext4 filesystem.

``` sudo mkfs -t ext4 /dev/webdata-vg/apps-lv, sudo mkfs -t ext4 /dev/webdata-vg/logs-lv ```
![ext4](https://github.com/user-attachments/assets/854ec32e-049b-4c7d-8cf3-1dac8327988a)

15. Create /var/www/html directory to store website files

``` sudo mkdir -p /var/www/html ```

16. Create /home/recovery/logs to store backup of log data.

``` sudo mkdir -p /home/recovery/logs ```

17. Mount /var/www/html on app-lv logical volume

``` sudo mount /dev/webdata-vg/apps-lv /var/www/html/ ```

![mount](https://github.com/user-attachments/assets/dcb28da0-6327-4bc6-a4d2-48a7d3326738)

18. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system).

``` sudo rsync -av /var/log/. /home/recovery/logs/ ```

![copy](https://github.com/user-attachments/assets/656a0bf2-79b9-4f28-9d2f-ba8066793f15)

19. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very important)

``` sudo mount /dev/webdata-vg/logs-lv /var/log ```

20. Restore log files back into /var/log directory.

``` sudo rsync -av /home/recovery/logs/. /var/log ```

## Step 2: Update The /ETC/FSTAB File :

The UUID of the device will be used to update the /etc/fstab file;

``` sudo blkid ```
![image](https://github.com/user-attachments/assets/8e87eef6-6964-4835-91db-9ba80c3c89c1)

``` sudo vi /etc/fstab ```
Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.

![image](https://github.com/user-attachments/assets/30a92eb3-74a2-480d-a5f1-955aecc55eaf)

1. Test the configuration and reload the daemon.

``` sudo mount -a               sudo systemctl daemon-reload ```

2. Verify your setup by running df -h, output must look like this:

![image](https://github.com/user-attachments/assets/036c108b-f10c-428c-9b35-95a4a95f3c88)


## Step 3: Prepare a Database Server 

1. Launch a Second Redhat Ec2 Instance (database server).
2. Repeat the same steps as for the Web Server.
3. Instead of app-lv create db-lv and mount it to /db directory insead of /var/www/html/.

## Step 4 : Install WordPress on your web Server EC2

1. Update the repository

``` sudo yum -y update ```

2. Install wget, Apache and it’s dependencies.

``` sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json ```

3. Start Apache

``` sudo systemctl enable httpd ```

``` sudo systemctl start httpd ```

4. To install PHP and it’s depemdencies

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```


5 Restart Apache

``` sudo systemctl restart httpd ```

6. Download wordpress and copy wordpress to var/www/html.

```
  mkdir wordpress
  cd   wordpress
  sudo wget http://wordpress.org/latest.tar.gz
  sudo tar xzvf latest.tar.gz
  sudo rm -rf latest.tar.gz
  cp wordpress/wp-config-sample.php wordpress/wp-config.php
  cp -R wordpress /var/www/html/
```

7. Configure SELinux Policies.

```
sudo yum update
sudo yum install mysql-server
```

## Install MySQL on your DB Server EC2
```
sudo yum update
sudo yum install mysql-server
```
Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:
```
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```
- Configure DB to work with wordpress
```
sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit

```
![Screenshot (455)](https://github.com/user-attachments/assets/74ef29ed-0f1e-47d9-a7aa-e307e3602fea)

- Configure WordPress to connect to remote database. Hint: Do not forget to open MySQL port 3306 on DB Server EC2. For extra security, you shall allow access to the DB server ONLY from your Web Server’s IP address, so in the Inbound Rule configuration specify source as /32.

1. Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

```
sudo yum install mysql
sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
```

2. Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.
3. Change permissions and configuration so Apache could use WordPress:
4. Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)
5. Try to access from your browser the link to your WordPress.

http://< public Ip >/wordpress/

![Screenshot (456)](https://github.com/user-attachments/assets/ea5946cf-6643-43c1-92f6-d0abd82be659)

![Screenshot (457)](https://github.com/user-attachments/assets/7b0960f1-ff8b-4be7-aa09-6d74d5fa0732)

![Screenshot (458)](https://github.com/user-attachments/assets/aeb89e6c-cb20-4c9d-8973-5f0311b6623d)

![Screenshot (459)](https://github.com/user-attachments/assets/a98bbb22-d89b-4bc8-a817-5f05091aea7d)


### Important: STOP your EC2 instances after completion of the project to avoid extra costs.

### We have successfully configured linux storage subsystem and have also deployed a web solution using wordpress CMS and MySQL RDBMS!.





 
