## Creating Hello World Service using Spring Boot

**Description:** This project is a maven project to build a simple REST Service using Spring Boot. Below are the key steps that are followed while creating this project.

**Key steps:**
- Go to https://start.spring.io/
- Fill all the details required to generate a Spring Boot project and add dependencies 'Spring Web','Spring Boot Actuator'. Click GENERATE which will download the maven project in a zip format
- Extract the downloaded maven project and import the same into Eclipse by following the steps mentioned in the course
- Visit pom.xml and make sure all the required dependencies are present in it like shown below,
---
  ### /pom.xml
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.4.4</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.eazybytes</groupId>
	<artifactId>helloworldservice</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>helloworldservice</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<java.version>11</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```
---

-  and associated libraries are downloaded under maven dependencies
