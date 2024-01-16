# Microservice project with Docker Containers

### Emart Application build with containers (docker-compose)

The Emart Applicatiion is an e-commerce application for shopping and was designed using a Microservice Architecture. The Frontend has an API Gateway created with NGINX and this listens at three endpoints: `/`, `/api` and `/webapi`.

The Application has many microservices operating at the end points when accessed by a user namely
- Angular JS
- Node JS
- MongoDB
- Java
- MySQL DB

These Microservices will be deployed on containers. 


### Provisioning
- Power Up a Vagrant Box running on Ubuntu 20.0 with a Vagrantfile, Move into the folder where the Vagrantfile is placed

- Verify is other VMs are powered up

      vagrant global-status

- Terminate other powered Up VMs

      vagrant global-status --prune

- Power up the VM for our Project

      vagrant up

- Log into the VM

      vagrant ssh


- Switch to the root user

      sudo -i

- create a script to update the server and install docker and docker compose and make the script executable

      nano installation.sh
      chmod +x installation.sh

- place the commands in the script


      sudo apt-get update
      sudo apt-get install \
      ca-certificates \
      curl \
      gnupg \
      lsb-release -y
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
      echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update
      sudo apt-get install docker-ce docker-ce-cli containerd.io -y
      sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      sudo chmod +x /usr/local/bin/docker-compose


    
- run the script

      ./inatallation.sh

- verify that docker service is up and running with any of these two commands

      systemctl status docker
      service docker status


- If git isn't installed by default, install git

      apt install git -y

- now, we have to get the source code for our emart application from github

      git clone https://github.com/devopshydclub/emartapp.git

- get into the source code folder and view the docker-compose.yaml file

      cd emartapp
      cat docker-compose.yaml

there are code steps to build the application and the microservice endpoints with containers
- client,
- api,
- webapi,
- nginx,
- emongo,
- emartdb

As seen below

<img width="810" alt="Screenshot 2024-01-05 at 11 36 36 PM" src="https://github.com/travdevops/v-projects/assets/137777644/23538083-c8f0-4f77-a565-2e70387ed1b8">


### Build and Run 

    docker compose up -d

<img width="1500" alt="Screenshot 2024-01-05 at 11 40 04 PM" src="https://github.com/travdevops/v-projects/assets/137777644/575df510-446f-49b1-b9da-842a6d996f47">


containers are running!

<img width="354" alt="Screenshot 2024-01-05 at 11 46 00 PM" src="https://github.com/travdevops/v-projects/assets/137777644/ff7c795a-e916-49ed-915d-325a0cb6edcd">

- run this command to check the running containers

      docker compose ps

<img width="1372" alt="Screenshot 2024-01-05 at 11 46 39 PM" src="https://github.com/travdevops/v-projects/assets/137777644/5cc5cb6a-c450-466c-b17d-db6355546b87">


- run this command to check for any exited containers

      docker compose ps -a

- access the nginx container running on port 80 through the browser with out public ip address
- show our public address

      ip addr show

<img width="804" alt="Screenshot 2024-01-05 at 11 47 08 PM" src="https://github.com/travdevops/v-projects/assets/137777644/95571347-f5bf-4b7f-b54b-eeb8e37d71a6">

- copy the public ip address and paste it on the browser to access our emart application. 






<img width="1673" alt="Screenshot 2024-01-05 at 11 49 12 PM" src="https://github.com/travdevops/v-projects/assets/137777644/5894b057-944c-4e83-9533-6002eafb32a3">



Thank you!

