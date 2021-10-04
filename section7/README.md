### Configuration management inside microservices network using Spring Cloud Config
---

**Description:** This repository has three maven projects with the names **accounts, loans, cards** which are continuation from the **section5** repository. 
A new microservices **'configserver'** is created in this section based on **Spring Cloud Config** which will act as a Config server. These three microservices **accounts, loans, cards** are updated to read the configurations/properties from the **'configserver'** microservices. Below are the key steps that are followed inside this **section7** where we focused on set up of **Config Server** inside our microservices network.

**Key steps:**
- Go to https://start.spring.io/
- Fill all the details required to generate a **configserver** Spring Boot project and add dependencies **Config Server**,**Spring Boot Actuator**. Click GENERATE which will download the **configserver** maven project in a zip format
- Extract the downloaded maven project of **configserver** and import the same into Eclipse by following the steps mentioned in the course
- Visit **pom.xml** of **configserver** and make sure all the required dependencies are present in it. Add **spring-boot-maven-plugin** plugin details along with docker image name details inside like we discussed in the course. This extra **spring-boot-maven-plugin** details will help us to generate a docker image using Buildpacks easily. Finally
your pom.xml should looks like shown below,

### configserver\pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.4.5</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.eaztbytes</groupId>
	<artifactId>configserver</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>configserver</name>
	<description>Configuration Server for Bank Microservices</description>
	<properties>
		<java.version>11</java.version>
		<spring-cloud.version>2020.0.2</spring-cloud.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<image>
						<name>eazybytes/${project.artifactId}</name>
					</image>
				</configuration>
			</plugin>
		</plugins>
	</build>

</project>

```
-  Open the SpringBoot main class **ConfigserverApplication.java** . We can always identify the main class in a Spring Boot project by looking for the annotation **@SpringBootApplication**. On top of this main class, please add annotation **'@EnableConfigServer'**. This annotation will make your microservice to act as a Spring Cloud Config Server. After making the changes your **ConfigserverApplication.java** class should like below,

### \src\main\java\com\eaztbytes\configserver\ConfigserverApplication.java

```java
package com.eaztbytes.configserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer
public class ConfigserverApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConfigserverApplication.class, args);
	}

}

```
- Create a **config** folder under the path **'configserver\src\main\resources\'** and copy all the 9 property files related to **accounts, loans and cards** microservices like mentioned in the course. The 9 property files can be found at https://github.com/eazybytes/microservices-with-spring-sectionwise-code/tree/master/section7/configserver/src/main/resources/config
- Open the **application.properties** inside **configserver** microservices and make the following entries inside it which will make help in reading the properties from a given classpath location.
### \src\main\resources\application.properties
```
spring.application.name=configserver
spring.profiles.active=native
spring.cloud.config.server.native.search-locations=classpath:/config
server.port=8071
```
-  Go to your Spring Boot main class **ConfigserverApplication.java** and right click-> Run As -> Java Application. This will start your Spring Boot application                successfully at port 8071 which is the port we configured inside **application.properties**. Your can confirm the same by looking at the console logs.
- Access the URLs like http://localhost:8071/accounts/default, http://localhost:8071/loans/dev, http://localhost:8071/cards/prod inside your browser to randomly validate the
  properties being exposed by Config Server for all the three microservices **accounts, loans and cards**.
- Once docker installation is successfull, please verify the same by running a **"docker --version"** command to confirm if the docker installation is successful or not.
- Make sure you are logged in to your docker desktop with the account details that you have created in step 1.
- Make sure maven is installed inside your system by running a command **"mvn --version"**
- Go to the folder location where **accounts** microservice **pom.xml** is present and run the command **"mvn clean install"**. This command will generate accounts microservice jar **"accounts-0.0.1-SNAPSHOT.jar"** under the **target** folder of **accounts** microservice.
- Validate if the jar is generated as expected in the above step by running the command **"mvn spring-boot:run"** from the folder location where **accounts** microservice **pom.xml** is present. This will start the accounts microservice at port 8080. We can validate the same by invoking the REST API http://localhost:8080/myAccount through Postman by passing the below request in JSON format. You should get the response from the corresponding microservices. Post validating the response stop the accounts microservice.
```json
{
    "customerId": 1
}
```
- We can start and verify accounts microservice response by running another command **"java -jar target/accounts-0.0.1-SNAPSHOT.jar"** from the command line tool. Post validating the response stop the accounts microservice.
- Now lets create the **Dockerfile** inside **accounts** microservice root folder with the following content. This **Dockerfile** will be used to generate a docker image by using the basic and lengthy approach that we discussed in the course.
```yaml
#Start with a base image containing Java runtime
FROM openjdk:11-slim as build

#Information around who maintains the image
MAINTAINER eazybytes.com

# Add the application's jar to the container
COPY target/accounts-0.0.1-SNAPSHOT.jar accounts-0.0.1-SNAPSHOT.jar

#execute the application
ENTRYPOINT ["java","-jar","/accounts-0.0.1-SNAPSHOT.jar"]
```
- Make sure docker server is up and running in your machine. Now run the docker command **"docker build . -t eazybytes/accounts"** from the location where **Dockerfile** is
  created in the above step. 
- The above step should have generated **accounts** microservice docker image and the same can be validated by running the docker command **"docker images"**
- Now create the **accounts** microservice container from the docker image generated in the above step by running the docker command **"docker run -p 8080:8080 eazybytes/accounts"**. We can validate the docker container created by invoking the REST API http://localhost:8080/myAccount through Postman by passing the below request in JSON format. You should get the response from the accounts microservice docker container created in this step.
```json
{
    "customerId": 1
}
```
- Now create one more container/instance of accounts microservice by running the docker command **"docker run -p 8081:8080 eazybytes/accounts"**. We can validate the docker container created with exposed port 8081 by invoking the REST API http://localhost:8081/myAccount through Postman by passing the below request in JSON format. You should get the response from the accounts microservice docker container created in this step.
```json
{
    "customerId": 1
}
```
- Run the docker command **"docker ps"** to see the 2 running containers of **accounts** microservice inside the docker server.
- Run the docker command **"docker stop [container-id]"** for both the 2 running containers of **accounts** microservice to stop them.
- Now in order to generate docker images for **loans** and **cards** microservices using **Buildpacks**, update the respective **pom.xml** files with the below build plugin       details. This will allow us to create docker images with out the need of **Dockerfile** like we created for **accounts** microservice.

### loans\pom.xml, cards\pom.xml

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<configuration>
				<image>
					<name>eazybytes/${project.artifactId}</name>
				</image>
			</configuration>
		</plugin>
	</plugins>
</build>  

```	
-  Now run the maven command **"mvn spring-boot:build-image"** for both **loans** & **cards** microservices using command line tool from the folder location where pom.xml is present. This will generate the docker images for both **loans** & **cards** microservices using **Buildpacks**. The same can be validated by running the docker command **"docker images"** which will list all the docker images.
- Now create the **loans** microservice container from the docker image generated in the above step by running the docker command **"docker run -p 8090:8090 eazybytes/loans"**. We can validate the docker container created by invoking the REST API http://localhost:8090/myLoans through Postman by passing the below request in JSON format. You should get the response from the loans microservice docker container created in this step.
```json
{
    "customerId": 1
}
```
- Now create the **cards** microservice container from the docker image generated previously by running the docker command **"docker run -p 9000:9000 eazybytes/cards"**. We can validate the docker container created by invoking the REST API http://localhost:9000/myCards through Postman by passing the below request in JSON format. You should get the response from the cards microservice docker container created in this step.
```json
{
    "customerId": 1
}
```
- Run the docker command **"docker ps"** to see the 2 running containers of **loans & cards** microservices inside the docker server.
- Run the docker command **"docker stop [container-id]"** for both the 2 running containers of **loans & cards** microservice to stop them.
- Push all your docker images of **accounts, loans, cards** into docker hub by running the command **"docker push docker.io/[image-name]"**. Please make sure you logged into 
  your docker account inside your docker desktop server, otherwise you will get authentication related issues while trying to push the docker images into docker hub. Validate
  if the docker images successfully pushed or not by loging into https://hub.docker.com/ website.
- In order to start all our docker images using a single docker compose command, install Docker compose by following the instruction mentioned in the https://docs.docker.com/compose/install/ website. More details can be found in the course.
- Post installation of docker compose, validate the same by running the docker compose command **"docker-compose --version"**
- Now write a **docker-compose.yml** file inside accounts project with the following content,
```yaml
version: "3.8"

services:

  accounts:
    image: eazybytes/accounts:latest
    mem_limit: 700m
    ports:
      - "8080:8080"
    networks:
      - eazybank-network
    
  loans:
    image: eazybytes/loans:latest
    mem_limit: 700m
    ports:
      - "8090:8090"
    networks:
      - eazybank-network
    
  cards:
    image: eazybytes/cards:latest
    mem_limit: 700m
    ports:
      - "9000:9000"
    networks:
      - eazybank-network
    
networks:
  eazybank-network:
```
-  Open the command line tool where the **docker-compose.yml** is present and run the docker compose command **"docker-compose up"** to start all the microservices containers with a single command. All the running containers can be validated by running a docker command **"docker ps"**.
-  Invoke the REST APIs http://localhost:8080/myAccount, http://localhost:8090/myLoans, http://localhost:9000/myCards through Postman by passing the below request in JSON          format. You should get the response from the corresponding microservices.
```json
{
    "customerId": 1
}
```
-  Stop the three running containers by executing the docker compose command **"docker-compose down"** from the location where **docker-compose.yml** is present.
---
### HURRAY !!! Congratulations, you successfully generated docker images for three microservices related to Accounts, Loans and Cards of EazyBank Application.
---
