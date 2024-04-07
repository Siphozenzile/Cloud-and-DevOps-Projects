# Creating a simple CI/CD Pipeline on AWS 

## The purpose of this project is to create a simple CI/CD Pipeline using Git, Jenkins, Ansible, Docker and Kubernetes on AWS.

## Steps : CI/CD Pipeline

1. Jenkins Installation
2. Git and Maven Setup
3. Integrating Tomcat_Server into the CI/CD pipeline
4. Integrating Docker (CI/CD)
5. Integrating Ansible (CI/CD)
6. Integrating Kubernates (CI/CD)


### Step 1 : Jenkins Installation on AWS EC2

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

           
           
