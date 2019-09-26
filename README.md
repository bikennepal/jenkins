# jenkins
First of all install Vm if you are using windows Operating System.
after installing linux vm operating system set VM pssword and root password.
=========================================================================

Lets know the ip address of the machine by the command(ip a) when you type this command on your linux terminal you will get the ip address.
something like"192.168.2.8/24" after that download putty to connect to your VM.
Install docker for centos from docker.com.

copy the following command for installation of docker.

sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

======================================
followed by avobe completion

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
===========================================================
followed by above completion.

sudo yum install docker-ce
=============================
Start docker:sudo systemctl start docker

Now lets automate our docker to start at boot time:sudo systemctl enable docker.
if while testing any error occurs run this command:sudo usermod -aG docker jenkins.we are adding docker to usergroup.
Inatall docker compose from docker compose installation guide from docker website after copying and pasting the command given the next step is to change permission to binary you can find the required command or code on docker.com
===============================================================================
Download the Jenkins Docker images.
Pull jenkins image from docker hub using this command:docker pull jenkins/jenkins
check docker images:docker images
You may have a question where is docker installed on centos.
check it by this command:docker info | grep -i root
To check how much space docker used:sudo du -sh /var/lib/docker
make a jenkins-data directory inside /home/jenkins/jenkins-data.
after this create a docker-compose.yml file usign vi editor:vi docker-compose.yml
what is mean by docker-compose file?
By docker compose file we defined which service we are going to use below is the script.
mind spacing while writing the script


version:'3'
services:
  jenkins:
    container_name:jenkins
    image:jenkins/jenkins
    ports:
        - "8080:8080"
    volumes:
      - "$PWD/jenkins_home:/var/jenkins_home"
     networks:
       - net
networks:
    net:
===================================================
Create a docker container for jenkins
As we have created a folder called jenkins_home to write data by jenkins make sure you give proper premission to write data to the Jenkins_home folder.
use this command to change the ownership:sudo chown 1000:1000 jenkins_home -R.
now start the docker-compose:docker-compose up -d
list the docker conatainer using:docker ps
use this command to see the logs of container:docker logs -f (container-name)
This is the password:d0b83675cb4e4384a25f7f56f23a913e
after this grab the ip of your linux and paste it to chrome and give the port number you have defined in your docker-compose file sthm like(6060 0r8080 etc and give the jenkins password that you get in your docker logs)

after this you will get a dashboard to enter your username password and so on.
fill in all the details and don't forget your credentials.
================================================================
create a local Dns for your jenkins
after this go to windows etc file and edit there with your ipaddress so that you can use jenkins.












