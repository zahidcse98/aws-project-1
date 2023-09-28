# Configuring MySQL for Data Persistence Between Two Elastic Compute Cloud (EC2) Instances Using Elastic Block Storage (EBS) on AWS

**Introduction:**

This tutorial will show you how to ensure data persistence between two MySQL databases hosted on different AWS EC2 instances. We will guide you through the process using AWS Elastic Block Storage (EBS) to make it even easier.

**Prerequisites:**

1. An AWS (Amazon Web Service) Account
2. Knowledge about VM Instance, Subnetting, Basic MySQL knowledge, MySQL Commands, EC2, EBS.
    
    **VM Instance:**
    
    A Virtual Machine (VM) instance is a dynamic computing environment that replicates the capabilities of a physical computer. It offers users the opportunity to execute applications and workloads within isolated and tailor-made settings.
    
    **Subnetting:**
    
    Subnetting is the practice of dividing a larger network into smaller, logically segmented subnetworks or subnets. It helps in efficient IP address management and network organization.
    
    **MySQL:**
    
    MySQL is an open-source relational database management system (RDBMS) that is widely used for storing, managing, and retrieving data. It is known for its speed, reliability, and ease of use.
    
    **EC2:**
    
    EC2 (Elastic Compute Cloud) is a web service provided by Amazon Web Services (AWS) that offers re-sizable compute capacity in the cloud. It allows users to create and manage virtual servers, known as EC2 instances, to run applications and services.
    
    **EBS:**
    
    EBS (Elastic Block Storage) is a scalable block storage service provided by AWS. It offers durable and high-performance block-level storage volumes that can be attached to EC2 instances, providing data persistence and flexibility for storage needs.
    

**Overview Diagram:**

![Fig: Overview Diagram](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled.png)

Fig: Overview Diagram

**Step 1:**

In this step we create our first vm and install a docker container in it, then install MySQL in the Docker container. Map Docker container’s MySQL data store directory  with vm’s local directory, where we will mount our EBS storage. Then we will create a MySQL table and create some data to it.

1st Open AWS dashboard and click EC2.

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%201.png)

Now, click Launch Instance.

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%202.png)

Then, select Image Ubuntu and then select Proceed without a key pair.

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%203.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%204.png)

Then, select Add New Volume and type 15 GB to create a 15 GB storage,

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%205.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%206.png)

Then, click launch instance

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%207.png)

From Instances list select your created instance, then click connect to launch to go to the Connect to instance page and select connection type and Click connect to launch instance terminal.

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%208.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%209.png)

In instance terminal tab, type following commands to install Docker and verify if the docker is running correctly.

```bash
sudo apt update -y
sudo apt install docker.io docker-compose -y
sudo docker run hello-world
```

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2010.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2011.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2012.png)

Now type the following codes to create a directory called dbData, then make the volume a file system, then mount the volume into that directory.

```bash
mkdir dbData
lsblk => to check the EBS Storage
sudo mkfs -t xfs /dev/xvdb -> create file storage in EBS
sudo file -s /dev/xvdb  --> check file system in EBS
sudo mount /dev/xvdb /home/ubutu/dbData -> mount the EBS in dbData directory 

```

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2013.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2014.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2015.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2016.png)

Now create a docker-compose.yml file and write the following lines:

```bash
vim docker-compose.yml
```

![new.png](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/new.png)

```yaml
version: "3"

services:
  mysql:
    image: mysql:latest
    container_name: db
    environment:
      MYSQL_ROOT_PASSWORD: a
      MYSQL_DATABASE: db
      MYSQL_USER: a
      MYSQL_PASSWORD: a
    volumes:
      - ./dbData:/var/lib/mysql
    ports:
      - "3306:3306"
    restart: always
```

Now run the following command to run the docker services and check if the container running successfully:

```bash
sudo docker-compose up -d
sudo docker ps
```

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2017.png)

Now type this command to enter into docker container’s bash

```bash
sudo docker exec -it db bash
```

then login to mysql server using mysql username and password defined in docker-composer.yml file then create a table and add some data into it:

```sql
mysql -q root -p
show databases;
use db;
show tables;
CREATE TABLE my_table (id INT AUTO_INCREMENT PRIMARY KEY,name VARCHAR(255) 
NOT NULL,age INT);
INSERT INTO my_table (name, age) VALUES ('Zahid Hasan', 23),('Tonmoy Ghosh', 25),
('Mahadi Hasan', 45),('Arif Jawad', 28),('Aadi Ahmed', 35);

```

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2018.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2019.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2020.png)

Step 1 Successfully Completed.

At this moment our diagram look like this:

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2021.png)

 

**Step 2:**

Now following the same step we will launch our 2nd vm and open a vm’s terminal.

Then run following command to install docker, teseting if the docker is running, create a dbData directory, create a docker-compose.yml file then write the docker commands into it:

```sql
sudo apt update -y
sudo apt install docker.io docker-compose -y
```

```yaml
version: "3"

services:
  mysql:
    image: mysql:latest
    container_name: db
    environment:
      MYSQL_ROOT_PASSWORD: a
      MYSQL_DATABASE: db
      MYSQL_USER: a
      MYSQL_PASSWORD: a
    volumes:
      - ./dbData:/var/lib/mysql
    ports:
      - "3306:3306"
    restart: alway
```

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2022.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2023.png)

Now go EBS volumes list. Select you created volume then detach it from vm1 and then attach it with vm2.

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2024.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2025.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2026.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2027.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2028.png)

Then, write following codes to vm-2 terminal to attach the volume into vm-2 and check the data remains into the storage we created into the 1st vm.

```bash
lsblk
sudo mount /dev/xvdf /home/ubuntu/dbData/
sudo docker exec -it db bash
```

```sql
mysql -u root -p
show datases;
use db;
select * from my_table;
```

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2029.png)

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2030.png)

we can see that the data we insert into mysql from vm-1 remains into the storage.

Finally our diagram looks like this:

![Untitled](Configuring%20MySQL%20for%20Data%20Persistence%20Between%20Two%204ac15fd76e414b37a418702dd5b67cd1/Untitled%2031.png)