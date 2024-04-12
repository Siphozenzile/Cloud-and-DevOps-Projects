# Creating a simple CI/CD Pipeline on AWS 

### The purpose of this project is to create a simple CI/CD Pipeline using Git, Jenkins, Ansible, Docker and Kubernetes on AWS.

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










           
