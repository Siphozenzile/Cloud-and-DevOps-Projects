# Implementing a basic client/server architecture using MySQL.. 

Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another.

In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server".

A simple diagram of Web Client-Server architecture is presented below:

![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/22745826-9e1a-46fb-9edd-24ffb116b6a7)

**The purpose of this project is to create a simple implementation of a client/server architecture using a relational database (MySQL):**

- To that we follow the instructions below :

### **1. Create and configure two Linux-based virtual servers (EC2 instances in AWS).**

```
Server A name - `mysql server`
Server B name - `mysql client`
```

![Screenshot (218)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/04255118-eabe-439e-bb73-c8dca192fa9f)

### **2. On mysql server Linux Server install MySQL Server software.**

```
sudo apt update -y
sudo apt install mysql-server -y
sudo systemctl enable mysql
```

![Screenshot (223)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/5fb25653-fe8b-4918-82ee-ddd3d0010c66)

### **3. On mysql client Linux Server install MySQL Client software.**


```
sudo apt update -y
sudo apt install mysql-server -y
sudo systemctl enable mysql
```

![Screenshot (196)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/87f905e4-e62e-4f01-977b-8678de44e419)

### **4. By default, both of ourr EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so we will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.**

- To do this we navigate to our server security group and edit inbound rules ( copy private IP of the client server and paste in the inboound rules as shown below)

- ![Screenshot (224)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/d5254514-951e-4b7b-9f06-f3022958cccb)


- ![Screenshot (225)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/7fb59996-750c-4a41-ab03-4efae3ced25f)

### **5. We need to configure MySQL server to allow connections from remote hosts.**
 
  ```
  sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
  ```
  Replace ‘127.0.0.1’ to ‘0.0.0.0’ like this:

  ![Screenshot (201)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/e7153f14-aa12-4cbd-ac7f-f85d2b2a253f)
  

### **6. From mysql client Linux Server connect remotely to mysql server Database Engine without using SSH. You must use the mysql utility to perform this action.**

- Copy the private ip of mysql-server and exwcute the following command to connect remotely to myswl server database engine.

```
sudo mysql -u remote_user -h 172.31.81.223 -p
```

Replace the above ip with the server's private ip 

![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/b4f293ce-bd45-44f5-a7eb-f538f5d6f349)

### **7. Check that you have successfully connected to a remote MySQL server and can perform SQL queries:**

```
Run : show dabases
```

![Screenshot (219)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/a1d5711f-556f-47df-90fd-a74d9068dce9)






   










