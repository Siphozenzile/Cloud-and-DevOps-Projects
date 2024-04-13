# Creating a simple CI/CD Pipeline on AWS 

The purpose of this project is to create a simple CI/CD Pipeline using Git, Jenkins, Ansible, Docker and Kubernetes on AWS.

### Steps : CI/CD Pipeline

1. Jenkins Installation
2. Git and Maven Setup
3. Integrating Tomcat_Server into the CI/CD pipeline
4. Integrating Docker (CI/CD)
5. Integrating Ansible (CI/CD)
6. Integrating Kubernates (CI/CD)


## Step 1 : Jenkins Installation on AWS EC2

Jenkins is an open source automation server. It helps automate the parts of software development related to building, testing, and deploying, facilitating continuous integration. and continuous delivery.

Prerequites :

1.  EC2 instance (with port 8080 on the Security Group)
2.  Java 11

Create your EC2 instance, use Amazon linux OS and SSH using putty or mobaxterm or gitbash to connect to server.

You can install jenkins using the rpm or by setting up the repo. We will set up the repo so that we can update it easily in the future.

```
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
amazon-linux-extras install epel 
amazon-linux-extras install java-openjdk11

```

Start Jenkins :

```
# Start jenkins service
service jenkins start

# Setup Jenkins to start at boot,
chkconfig jenkins on
```

Now you can access jenkins at :
```
http://YOUR-SERVER-PUBLIC-IP:8080
```
![Screenshot (119)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/978503ec-2cf1-4f9c-bf8e-d72a246d839b)



## Step 2 : Git and Maven Setup on Jenkins Server

In the previous step we installed Jenkins, which is our CI/CD automation server. Step 2, we will install and setup Git and Maven on Jenkins. 

**Git** : Is a distributed version control system used for tracking changes in code, files and coordinating work on those files among multiple people. In this project we will pull code from git using jenkins through github plugin.
  
**Maven** : Is a code build tool which used to convert your code to an artifact. this is a widely used plugin to build in continuous integration

#### Prerequisites:
 - Jenkins (Installed in step 1)

#### 1. Install and Setup Git on Jenkins Server

Install Git packages on Jenkins Server :
```
yum install git -y
```

- Setup Git on jenkins console :

Navigate to : - Manage Jenkins > Jenkins Plugins > available > github (install github integration)


![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/0e18b5ff-3124-4649-b06b-2140f3502cd0)



- Configure git path :

Navigate to : - Manage Jenkins > Global Tool Configuration > git (provide path to your git executable)


![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/3705e3a2-0ef3-4fd1-839e-a222e2832bff)


![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/e5091ffd-e4c0-4efa-a566-c59114a401a3)




#### 2. Install and Configure Maven on Jenkins

Maven is a code build tool which used to convert your code to an artifact. this is a widely used plugin to build in continuous integration.

#### Prerequites :
- Jenkins

##### Install Maven on Jenkins :

1. Download maven packages https://maven.apache.org/download.cgi onto Jenkins server.
   
2. Create maven directory under /opt
```
mkdir /opt/maven
cd /opt/maven
```

3.  Download maven 3.6.1
```
 wget http://mirrors.estointernet.in/apache/maven/maven-3/3.6.1/binaries/apache-maven-3.6.1-bin.tar.gz
 tar -xvzf apache-maven-3.6.1-bin.tar.gz
```

4. Setup M2_HOME and M2 paths in .bash_profile of the user and add these to the path variable
```
vi ~/.bash_profile
M2_HOME=/opt/maven/apache-maven-3.6.1
M2=$M2_HOME/bin
PATH=<Existing_PATH>:$M2_HOME:$M2
```

5. Checkpoint (check which version of maven)
```
mvn --version
```



 - **Setup maven on Jenkins console**

1. Install maven plugin without restart

   Navigate to :- Manage Jenkins > jenkins plugins > available > maven invoker (type in the search bar and install)

   ![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/d1617726-8d3b-48df-b41b-d79737980ca7)

   Navigate to :- Manage Jenkins > jenkins plugins > available > maven integration (type in the search bar and install)

   ![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/c476ec0e-4a09-4333-a0e1-5352ca3750aa)


2. Configure maven path :

   Navigate to :- Manage Jenkins > Global Configuration Tools > Maven (choose version)

   ![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/ef66621b-fe53-4855-b00a-14d56c4ed35a)
   

## Step 3 : Integrating Tomcat_Server into the CI/CD pipeline

Apache Tomcat is an open-source implementation of the Java Servlet, JavaServer Pages, Java Expression Language, and Java WebSocket technologies. It acts as a web server and a Java servlet container, allowing Java code to run dynamically to generate web pages. In this step we install it on EC2 instance and integrate into the CI/CD pipeline.

**Pre-requisite**

1. EC2 instance with Java 11 and SSH via (Mobaxterm or Putty)

### Install Apache Tomcat

1. Download tomcat packages from https://tomcat.apache.org/download-80.cgi onto /opt on EC2 instance :
```
# Create tomcat directory
cd /opt

# Download using the command :
wget http://mirrors.fibergrid.in/apache/tomcat/tomcat-8/v8.5.35/bin/apache-tomcat-8.5.35.tar.gz
tar -xvzf /opt/apache-tomcat-<version>.tar.gz
```

2. Give executing permissions to startup.sh and shutdown.sh which are under bin :
```
chmod +x /opt/apache-tomcat-8.5.35/bin/startup.sh
chmod +x /opt/apache-tomcat-8.5.35/bin/shutdown.sh
```

3. Create link files for tomcat startup.sh and shutdown.sh :
   
```
ln -s /opt/apache-tomcat-<version>/bin/startup.sh /usr/local/bin/tomcatup
ln -s /opt/apache-tomcat-<version>/bin/shutdown.sh /usr/local/bin/tomcatdown
tomcatup
```

Now we can access tomcat application from browser on port 8080 :

 - http://<Public_IP>:8090 (75.101.171.209:8090)

![Screenshot (117)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/f7d318e6-6edb-40aa-8c65-f4efc09c3fd2)

**Access Tomcat from browser**


1. Now our application is accessible on port 8080. but tomcat application doesnt allow to login from browser. We will change a default parameter in context.xml to address this issue :

```
#search for context.xml
find / -name context.xml
```

2. The above command gives 3 context.xml files. comment () Value ClassName field on files which are under webapp directory. After that restart tomcat services to effect these changes. At the time of writing this lecture below 2 files are updated :
```
/opt/tomcat/webapps/host-manager/META-INF/context.xml
/opt/tomcat/webapps/manager/META-INF/context.xml

# Restart tomcat services
tomcatdown  
tomcatup
```

3. Update users information in the tomcat-users.xml file goto tomcat home directory and Add below users to conf/tomcat-users.xml file :

```
 <role rolename="manager-gui"/>
 <role rolename="manager-script"/>
 <role rolename="manager-jmx"/>
 <role rolename="manager-status"/>
 <user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
 <user username="deployer" password="deployer" roles="manager-script"/>
 <user username="tomcat" password="s3cret" roles="manager-gui"/>
```

4. Restart serivce and try to login to tomcat application from the browser. Navigate to manager app to check it work. You should get the following output :

![Screenshot (118)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/1d5c1324-e33e-489f-951f-a3b09d105b07)

**Exercise : Deploying on Tomcat Server** 

In this exercise we deploy a war file on tomcat using jenkins (the pictures are output):

![Screenshot (120)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/2972a08a-016e-42af-ac0e-9e5d4de21b00)

![Screenshot (121)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/c8d95f70-2c75-4369-8554-092dedfd5805)

![Screenshot (122)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/77b06879-7238-4a0f-b368-14fc7ca2bfa0)

Output from the browser :

![Screenshot (123)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/cf9c7d82-3c9e-48db-be81-d38a6a7ef52d)


## Step 4 : Integrating Docker (CI/CD)

In this step we install docker on a Linux Server and integrate to the CI/CD. 

**Pre-requisites**

1. Amazon linux EC2 instance and SSH via (Putty or Mobaxterm)

### Installation Steps :

1. Install docker and start docker services.

```
yum install docker -y
docker --version 

# start docker services
service docker start
service docker status
```

2. Create a user called dockeradmin.

```
useradd dockeradmin
passwd dockeradmin
```
3. Add a user to docker group to manage docker.

```
usermod -aG docker dockeradmin
```

**Validation test**

```
docker run -d --name test-tomcat-server -p 8090:8080 tomcat:latest
```

 - **Integrating docker host with Jenkins**:

-Navigate to Jenkins manage > plugins > available > publish over ssh (install this plugin)

-Create dockeradmin on your linux terminal

-Add docker host under SSH Server on jenkins

![image](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/fa7aef08-b424-470e-8b9b-fb487c77bd8e)

![Screenshot (127)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/b1ab9291-da77-4b9a-a532-9e4e625da165)

**Execirse 1** : Deploying on Docker 

See output :

![Screenshot (129)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/5c8e27d8-e498-404b-bc7e-99f822658716)

![Screenshot (130)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/b3d868fc-ee22-4b8b-af07-22729043545c)

![Screenshot (131)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/296e5c8a-9c37-4fd2-874a-602ed1a40004)

**Exercise 2** : Deploying on Docker container

See output : 

From a dockerfile, we can create a docker image from a docker image, we can create a docker container.

![Screenshot (132)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/70aa67c8-6f36-42cc-9d01-a82c49a2855e)

![Screenshot (133)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/35d6e9ee-d82c-4321-8e71-8711140be6aa)

![Screenshot (134)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/31a820ed-e483-44bf-a81e-5ff7e4b33fa4)

![Screenshot (135)](https://github.com/Siphozenzile/Cloud-and-DevOps-Projects/assets/161639765/97111296-4384-44e8-a972-972b4797328e)


## Step 5 : Integrating Ansible into the CI/CD






























           
