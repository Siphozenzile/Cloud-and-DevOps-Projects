# Linux Administration (NFS, LVM, PHP, APACHE) - DevOps Tooling Website

In this project, we will implement a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible.

The tools we want our team to be able to use are well known and widely used by multiple DevOps teams, so we will introduce a single DevOps Tooling Solution that will consist of:

1. Jenkins – free and open source automation server used to build CI/CD pipelines.
2. Kubernetes – an open-source container-orchestration system for automating computer application deployment, scaling, and management.
3. Jfrog Artifactory – Universal Repository Manager supporting all major packaging formats, build tools and CI servers. Artifactory.
4. Rancher – an open source software platform that enables organizations to run and manage Docker and Kubernetes in production.
5. Grafana – a multi-platform open source analytics and interactive visualization web application.
6. Prometheus – An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting approach.
7. Kibana – Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack.

## Requirements :

We will implement a solution that consists of following components:

- Infrastructure: AWS
- Webserver Linux: Red Hat Enterprise Linux 8
- Database Server: Ubuntu 20.04 + MySQL
- Storage Server: Red Hat Enterprise Linux 8 + NFS Server
- Programming Language: PHP
- Code Repository: GitHub

On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.

![image](https://github.com/user-attachments/assets/59b4bb50-cfbc-400c-9ec2-acd30a095044)


## STEP 1 – PREPARE NFS SERVER

1. Spin up a new EC2 instance with RHEL Linux 8 Operating System.

![Screenshot (464)](https://github.com/user-attachments/assets/254a1d34-7d0b-4df3-97df-8deb23fb8cf8)


2. Configure LVM on the Server.

- Instead of formating the disks as ext4 you will have to format them as xfs

- Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs

- Create mount points on /mnt directory for the logical volumes as follow:

Mount lv-apps on /mnt/apps – To be used by webservers Mount lv-logs on /mnt/logs – To be used by webserver logs Mount lv-opt on /mnt/opt – To be used by Jenkins server 

![Screenshot (466)](https://github.com/user-attachments/assets/98c93fd0-7804-4ea3-991f-4afcb9161dca)


![logical volumes](https://github.com/user-attachments/assets/092a5061-ea88-48b4-9cdd-d360d25f77f7)


![lvm2](https://github.com/user-attachments/assets/072ab288-2ecf-4112-ac17-3a845fd67ff1)


![pvcreate](https://github.com/user-attachments/assets/fbd4257a-541c-4f6d-a2e6-0bcb369e6d91)


![vgcreate](https://github.com/user-attachments/assets/0f0e2959-7d2c-456b-9221-679a9516cc0a)


![formatting disks as xfs](https://github.com/user-attachments/assets/3fb4239a-91dd-4aff-8b82-4e83291d38bc)


4. Install NFS server, configure it to start on reboot and make sure it is u and running

```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

5. Export the mounts for webservers’ subnet cidr to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security. To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:

![image](https://github.com/user-attachments/assets/8699a9cc-6442-40b2-b23c-849fd930e9c8)

Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:

```
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service

```

Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):

```
sudo vi /etc/exports

/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

Esc + :wq!

sudo exportfs -arv

```

6. Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)

```
rpcinfo -p | grep nfs
```

![image](https://github.com/user-attachments/assets/676cd33a-00b4-4118-806b-d9c2fe23f791)

Important note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049


![edit inbound](https://github.com/user-attachments/assets/4de2b5bb-013c-46f3-b073-529f12e9e24b)
















