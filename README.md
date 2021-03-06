
Deploying Java Spring Boot program as Bluemix Container
========================================================

Customer uses Java Spring Boot framework for its internal application development. So, a sample Java Spring Boot program is created and uploaded on GitHub. The intend of this document is to describe the steps required to build/test a sample program as container on local host and finally to Bluemix pubic. 

Prerequisites
==================
¥	You have a git client installed locally from https://git-scm.com/downloads

¥	You have signed up for IBM Bluemix (Public) https://console.ng.bluemix.net/registration/ and have an ID to log in with.

¥	Install Docker locally on your machine from https://www.docker.com/products/docker. This will give you access to the Docker command-line tools.

¥	Install the IBM Bluemix command-line tools from http://clis.ng.bluemix.net/ui/home.html, which will require installing the prerequisite Cloud Foundry command line tools from https://github.com/cloudfoundry/cli/releases.

Instructions for this tutorial are written assuming you are using OS X


Build the Spring Boot Container on localhost
=============================================

You need to build the Docker container for Java Spring Boot program locally before pushing it up to the Bluemix containers repository. All the required source code files including jar file are uploaded on GitHub. 

¥	Open a terminal and clone the repository, checking out:
 	
  $git clone https://github.com/vksinghibm/spring-boot-docker.git
 	
  $cd spring-boot-docker

¥	If the clone does not work, go to 

https://github.com/vksinghibm/spring-boot-docker and download the zip file. 
Copy the spring-boot-docker-master.zip file in your favorite location, unzip the file and go to directory spring-boot-docker

¥	Now build the Docker image :
 	
  $cd docker
 	
  $docker build -t customer_spring_boot_image .

Verify the image is built and available on your local host machine
 	
  $docker images | grep customer_spring_boot_image

Finally run the container and test it
 	
  $docker run --name customer_spring_boot_localcontainer -d -p 8080:8080 customer_spring_boot_image

Verify the container is up & running.
 	
  $docker ps -a | grep customer
 	
  Test the container 
 	
  $ curl http://localhost:8080
 	
  Hello Customer, This is Bluemix container with spring framework

If you see above message on console it means the container is deployed successfully.
Also, you access the container in your favorite browser by typing 

http://localhost:8080

Push the Spring Boot Container to Bluemix and start it running
==================================================================

¥	Firstly, log into Bluemix and initialize the container runtime:

$cf login -a  https://api.ng.bluemix.net -u vksingh@us.ibm.com -p xxxxxx -o Org -s Space

$cf ic init

¥	Tag your built image to prepare it for upload to the remote registry:

$docker tag customer_spring_boot registry.ng.bluemix.net/<namespace>/customer_spring_boot

The image is tagged with Bluemix repository prefix and then actual registry name. For Bluemix organization, the registry name needs to be known. 

Now actually push the image to the remote registry (this may take a little while):

$docker push registry.ng.bluemix.net/<namespace>/customer_spring_boot


Verify the image

$ cf ic images | grep customer_spring_boot


¥	Finally run the container 

$cf ic group create -p 8080 -m 256 --min 1 --auto --name customerspringbootconatiner-group -n customerspringbootcontainer -d 
mybluemix.net registry.ng.bluemix.net/<namespace>/customer_spring_boot

Once the container has started running, the Bluemix CLI will print out the container ID. You typically only need the first few characters - enough to uniquely identify it (e.g. abc1234).

Verify the running container
$cf ic ps | grep customer

You can also check at Bluemix console https://console.ng.bluemix.net/dashboard/apps

Note: it takes few minutes to configure networking for container group. 

Once container is created completely, you can access the “Routes” from dashboard. For current example the route is 

https://customerspringbootcontainer.mybluemix.net/

