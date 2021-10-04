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
- Open the **application.properties** inside **configserver** microservices and make the following entries inside it which will help in reading the properties from a given classpath location.
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
- Stop the Config Server microservices which started at port 8071 earlier.  
- Open the **application.properties** inside **configserver** microservices and make the following entries inside it which will help in reading the properties from a given file system location. Please make sure to create the configured folder/filesystem in your system and copy all the 9 property files related to **accounts, loans and cards** microservices like mentioned in the course.
### \src\main\resources\application.properties
```
spring.application.name=configserver
spring.profiles.active=native
spring.cloud.config.server.native.search-locations=file:///C://config
server.port=8071
```
-  Go to your Spring Boot main class **ConfigserverApplication.java** and right click-> Run As -> Java Application. This will start your Spring Boot application                successfully at port 8071 which is the port we configured inside **application.properties**. Your can confirm the same by looking at the console logs.
- Access the URLs like http://localhost:8071/accounts/default, http://localhost:8071/loans/dev, http://localhost:8071/cards/prod inside your browser to randomly validate that
  properties are being read from configured file system by Config Server for all the three microservices **accounts, loans and cards**.
- Stop the Config Server microservices which started at port 8071 earlier.  
- Create Github repository and upload all the 9 property files related to **accounts, loans and cards** microservices in to it like mentioned in the course. You can refer to https://github.com/eazybytes/microservices-config as a sample reference.
- - Open the **application.properties** inside **configserver** microservices and make the following entries inside it which will help in reading the properties from a given Github repository. 
### \src\main\resources\application.properties
```
spring.application.name=configserver
spring.profiles.active=git
spring.cloud.config.server.git.uri=https://github.com/eazybytes/microservices-config.git
spring.cloud.config.server.git.clone-on-start=true
spring.cloud.config.server.git.default-label=main
server.port=8071

```
-  Go to your Spring Boot main class **ConfigserverApplication.java** and right click-> Run As -> Java Application. This will start your Spring Boot application                successfully at port 8071 which is the port we configured inside **application.properties**. Your can confirm the same by looking at the console logs.
- Access the URLs like http://localhost:8071/accounts/default, http://localhost:8071/loans/dev, http://localhost:8071/cards/prod inside your browser to randomly validate that
  properties are being read from configured Github location by Config Server for all the three microservices **accounts, loans and cards**.

---
### HURRAY !!! Congratulations, you successfully generated docker images for three microservices related to Accounts, Loans and Cards of EazyBank Application.
---
