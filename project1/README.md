## VPROFILE-PROJECT 


### ARCHITECTURE OF PROJECT SERVICES 
1. NGINX (Web Service)
2. TOMCAT (App Service)
3. RABBITTMQ (Queuing Agent)
4. MEMCACHE (Database Caching)
5. MARIADB (SQL Database)



### FLOW OF EXECUTION
1. Setup tools
2. Clone source code
3. Setup All the services
- MariaDB
- Memcache
- RabbitMQ
- Tomcat
- Nginx
4. App Build & Deploy
5. Verify from the browser


<img width="1000" alt="Screenshot 2023-12-05 at 3 50 11 PM" src="https://github.com/travdevops/v-projects/assets/137777644/3741bd6f-1759-47bf-a0b8-1173fc203595">



### Setting Up Tools
Provisioning our Different Servers with AWS EC2 Instances. Setup should be done in below mentioned order. 
- MariaDB > Memcache > RabbitMQ > Tomcat > Nginx.


 ### 1. MaARIADB Setup.

- Login to the ec2 Instance.
- Switch to the root user

       $ sudo -i

- Update the hosts configuration file with the Private-IP-Address of the other servers. 

       # cat /etc/hosts

- Install Maria DB Package
      
      # yum install mariadb-server -y

- Start & enable mariadb-server

      # systemctl start mariadb
      # systemctl enable mariadb


- Run mysql secure installation script.

      # mysql_secure_installation

NOTE: Set db root password, I will be using **admin123** as password


<img width="730" alt="Screenshot 2023-12-07 at 12 19 11 PM" src="https://github.com/travdevops/v-projects/assets/137777644/abe246e8-25f3-4a17-a87a-a79fe7267c29">



- Login to db Console with and User and Password.

      # mysql -u root -padmin123

- Setup database.
 
      mysql> create database accounts;
      mysql> grant all privileges on accounts.* TO 'admin'@'%' identified by 'admin123' ; mysql> FLUSH PRIVILEGES;
      mysql> exit;

- Clone the Source code & Initialize Database.

      # git clone -b main https://github.com/hkhcoder/vprofile-project.git
      # cd vprofile-project
      # mysql -u root -padmin123 accounts < src/main/resources/db_backup.sql
      # mysql -u root -padmin123 accounts


      mysql> show tables; 
      mysql> exit;


- Restart mariadb-server

      # systemctl restart mariadb

Setup up Listening Ports. 

- Open TCP Port 3306 in AWS security group.

- Restart mariadb service

      # systemctl restart mariadb


### 2. MEMCACHE SETUP

- Login to the ec2 Instance.
- Switch to the root user

      $ sudo -i

Update the hosts configuration file with the Private-IP-Address of the other servers. 

    # cat /etc/hosts

- Install, start & enable memcache

      # sudo yum install memcached -y
      # sudo systemctl start memcached
      # sudo systemctl enable memcached
      # sudo systemctl status memcached
      # sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
      # sudo systemctl restart memcached


- Setup up Listening Ports. 

- Open TCP Port 11211 in AWS security group
- Open UDP Port 11111




### 3. RABBITMQ SETUP

- Login to the ec2 Instance.
- Switch to the root user

      $ sudo -i

- Update the hosts configuration file with the Private-IP-Address of the other servers.

      # cat /etc/hosts

- Disable SELINUX

      # sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
      # setenforce 0

- Install Dependencies

      # curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash # sudo yum clean all
      # sudo yum makecache
      # sudo yum install erlang -y


- Install RabbitMQ Server.

      # curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash # sudo yum install rabbitmq-server -y


- Start & Enable RabbitMQ

      # sudo systemctl start rabbitmq-server
      # sudo systemctl enable rabbitmq-server
      # sudo systemctl status rabbitmq-server


- Configuration Change

      # sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config' # sudo rabbitmqctl add_user test test
      # sudo rabbitmqctl set_user_tags test administrator

- Setup up Listening Ports. 
- Open TCP Port 5671-5672 in AWS security group 


- Restart RabbitMQ service

      # systemctl restart rabbitmq-server

### 4.TOMCAT SETUP

 
- Login to the ec2 Instance.
-  Switch to the root user

       $ sudo -i

- Update the hosts configuration file with the Private-IP-Address of the other servers.

      # cat /etc/hosts

- Update the server

      # yum update -y


- Install epel-release repository

      # yum install epel-release -y
      OR
      # sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm

- Install Dependencies (open-jdk, maven)

      # dnf -y install java-11-openjdk java-11-openjdk-devel
      # dnf install git maven wget -y


- Switch to /tmp directory

      # cd /tmp/

- Download & Unzip Tomcat Package

      # wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz
      # tar xzvf apache-tomcat-9.0.75.tar.gz

- Add tomcat user home dir

      # useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat

- Copy data to tomcat home dir

      # cp -r /tmp/apache-tomcat-9.0.75/* /usr/local/tomcat/ Make tomcat user owner of tomcat home dir


- Make tomcat user owner of tomcat home dir

      # chown -R tomcat.tomcat /usr/local/tomcat


### Setup systemctl command for tomcat

- Create tomcat service file

      # nano /etc/systemd/system/tomcat.service

- Update the file with below content

<img width="523" alt="Screenshot 2023-12-07 at 12 50 23 PM" src="https://github.com/travdevops/v-projects/assets/137777644/093f6fc0-7fdd-413b-bd6f-4620907ab4f7">



- Reload systemd files

      # systemctl daemon-reload

- Start & Enable tomcat service

      # systemctl start tomcat
      # systemctl enable tomcat

  
- Setup up Listening Ports. 

- Open TCP Port 80 in AWS security group 



## CODE BUILD & DEPLOY



- Clone the Source code from Github

      # git clone -b main https://github.com/hkhcoder/vprofile-project.git Update configuration


- Update configuration

      # cd vprofile-project
      # nano src/main/resources/application.properties

- Update file with backend server details



### Build code
- Run command below inside the repository (vprofile-project)

      # mvn install


<img width="736" alt="Screenshot 2023-12-05 at 8 47 18 AM" src="https://github.com/travdevops/v-projects/assets/137777644/21dd1999-d12a-415f-b250-7afc7b180607">

- Stop the tomcat service before deploying the artifact

      # systemctl stop tomcat

- Deploy artifact

      # rm -rf /usr/local/tomcat/webapps/ROOT*
      # cp target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
      # systemctl start tomcat
      # chown tomcat.tomcat /usr/local/tomcat/webapps -R

- Restart tomcat service

      # systemctl restart tomcat



### 5.NGINX SETUP

- Login to the ec2 Instance.
- Switch to the root user

      $ sudo -i


- Update the hosts configuration file with the Private-IP-Address of the other servers.

      # cat /etc/hosts

- Update the server & Install nginx

      # apt update Install nginx
      # apt install nginx -y


- Create Nginx configuration file

      # nano /etc/nginx/sites-available/vproapp

- Update with the below content

<img width="493" alt="Screenshot 2023-12-07 at 12 46 52 PM" src="https://github.com/travdevops/v-projects/assets/137777644/4398b6c2-68ba-4398-b238-d5b073b06e1c">



- Remove default nginx conf

      # rm -rf /etc/nginx/sites-enabled/default Create link to activate website

- Create link to activate website

      # ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp

- Restart Nginx
 
      # systemctl restart nginx


  ### 5. Verify The Website on the Web Broswer



  <img width="1671" alt="Screenshot 2023-12-05 at 11 06 17 AM" src="https://github.com/travdevops/v-projects/assets/137777644/84dc9bb5-608c-460f-beb2-725b115b17e5">


  <img width="1417" alt="Screenshot 2023-12-05 at 9 18 43 AM" src="https://github.com/travdevops/v-projects/assets/137777644/9ebb2415-27a2-40ae-904d-499c268e54dd">

