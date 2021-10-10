### Making Microservices Resilient using Resilience4j patterns
---

**Description:** This repository has five maven projects with the names **accounts, loans, cards, configserver, eurekaserver** which are continuation from the section8 repository. Below are the key steps that are followed inside this **section9** where we focused on making our microservices resilient using **Resilience4j** patterns.

**Key steps:**
- Open the **pom.xml** of **accounts** microservice and add the below **Resilience4j** related dependencies to it,

### accounts\pom.xml

```xml
<dependency>
	<groupId>io.github.resilience4j</groupId>
	<artifactId>resilience4j-spring-boot2</artifactId>
</dependency>
<dependency>
	<groupId>io.github.resilience4j</groupId>
	<artifactId>resilience4j-circuitbreaker</artifactId>
</dependency>
<dependency>
	<groupId>io.github.resilience4j</groupId>
	<artifactId>resilience4j-timelimiter</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```
-  In order to implement Circuit Breaker pattern for **"/myCustomerDetails"** API inside **accounts** microservice Open the SpringBoot main class **EurekaserverApplication.java** . We can always identify the main class in a Spring Boot project by looking for the annotation 
   **@SpringBootApplication**. On top of this main class, please add annotation **'@EnableEurekaServer'**. This annotation will make your microservice to act as a 
   Spring Cloud Netflix Eureka Server. After making the changes your **EurekaserverApplication.java** class should like below,

### \src\main\java\com\eaztbytes\eurekaserver\EurekaserverApplication.java

```java
package com.eaztbytes.eurekaserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaserverApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaserverApplication.class, args);
	}

}
```
- Open the **application.properties** inside **eurekaserver** microservices and make the following entries inside it like we discussed in the course. These entries will help
  in connecting to the Config Server and to disable the **ribbon** features. Please note if you are using a Spring Boot version of >=2.5 then providing **ribbon** 
  configurations is not required. After making the changes, your **application.properties** should like below,
### \src\main\resources\application.properties
```
spring.application.name=eurekaserver
spring.config.import=optional:configserver:http://localhost:8071
spring.cloud.loadbalancer.ribbon.enabled=false
```
- Like we discussed in the course please make sure to create a **eurekaserver.properties** with the below content inside the location where your Config Server is reading the 
  properties,
### /eurekaserver.properties
```
server.port=8070
eureka.instance.hostname=localhost
eureka.client.registerWithEureka=false
eureka.client.fetchRegistry=false
eureka.client.serviceUrl.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka/
```
- Please make sure to start your **configserver** microservices
- Go to your Spring Boot main class **EurekaserverApplication.java** and right click-> Run As -> Java Application. This will start your Spring Boot application successfully 
  at port 8070 which is the port we configured inside **eurekaserver.properties**. Your can confirm the same by looking at the console logs.
- Access the URL http://localhost:8070 inside your browser and make sure that you are able to access the Eureka Dashboard home page.
- In order to make your **accounts** microservice to connect with **eurekaserver**, add the below dependencies inside accounts **pom.xml** like we discussed inside the course,
```xml
    <dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
```
- Open the **application.properties** inside **accounts** microservices and add the below entries inside it which will help in integrating with **eurekaserver**
### \accounts\src\main\resources\application.properties
```
eureka.instance.preferIpAddress = true 
eureka.client.registerWithEureka = true
eureka.client.fetchRegistry = true
eureka.client.serviceUrl.defaultZone = http://localhost:8070/eureka/

## Configuring info endpoint
info.app.name=Accounts Microservice
info.app.description=Eazy Bank Accounts Application
info.app.version=1.0.0

endpoints.shutdown.enabled=true
management.endpoint.shutdown.enabled=true
```
-  Go to your Spring Boot main class **AccountsApplication.java** and right click-> Run As -> Java Application. This will start your **accounts** microservice successfully 
   at port 8080 which is the port we configured inside **application.properties**. Your can confirm the same by looking at the console logs.
- Access the Eureka Server Dashboard URL http://localhost:8070 inside your browser and make sure that you are able to see that **accounts** microservice details on
  the Eureka Dashboard home page.
- In order to make your **loans, cards** microservice to connect with **eurekaserver**, add the below dependency inside loans, cards **pom.xml** like we discussed inside 
  the course,
```xml
    <dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>
```
- Open the **application.properties** inside **loans** microservices and add the below entries inside it which will help in integrating with **eurekaserver**
### \loans\src\main\resources\application.properties
```
eureka.instance.preferIpAddress = true 
eureka.client.registerWithEureka = true
eureka.client.fetchRegistry = true
eureka.client.serviceUrl.defaultZone = http://localhost:8070/eureka/

## Configuring info endpoint
info.app.name=Loans Microservice
info.app.description=Eazy Bank Loans Application
info.app.version=1.0.0

endpoints.shutdown.enabled=true
management.endpoint.shutdown.enabled=true
```
- Open the **application.properties** inside **cards** microservices and add the below entries inside it which will help in integrating with **eurekaserver**
### \cards\src\main\resources\application.properties
```
eureka.instance.preferIpAddress = true 
eureka.client.registerWithEureka = true
eureka.client.fetchRegistry = true
eureka.client.serviceUrl.defaultZone = http://localhost:8070/eureka/

## Configuring info endpoint
info.app.name=Cards Microservice
info.app.description=Eazy Bank Cards Application
info.app.version=1.0.0

endpoints.shutdown.enabled=true
management.endpoint.shutdown.enabled=true
```
-  Go to your Spring Boot main class **LoansApplication.java** and right click-> Run As -> Java Application. This will start your **loans** microservice successfully 
   at port 8090 which is the port we configured inside **application.properties**. Your can confirm the same by looking at the console logs.
-  Go to your Spring Boot main class **CardsApplication.java** and right click-> Run As -> Java Application. This will start your **cards** microservice successfully 
   at port 9000 which is the port we configured inside **application.properties**. Your can confirm the same by looking at the console logs.
- Access the Eureka Server Dashboard URL http://localhost:8070 inside your browser and make sure that you are able to see that **loans, cards** microservices details on
  the Eureka Dashboard home page.
- In order to set up Client side load balancing using **Feign client**, add **@EnableFeignClients** annotation on top of **AccountsApplication.java** class
  which is present inside **accounts** microservice.
- Like discussed in the course, create two interfaces with the name **LoansFeignClient.java**,**CardsFeignClient.java** inside **accounts** microservice project. 
  These two interfaces and the methods inside them will help to communicate with **loans** and **cards** microservices using Feign client from **accounts** microservice.
  These two interfaces should like below,
### \accounts\src\main\java\com\eazybytes\accounts\service\client\LoansFeignClient.java
```java
package com.eazybytes.accounts.service.client;

import java.util.List;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.eazybytes.accounts.model.Customer;
import com.eazybytes.accounts.model.Loans;

@FeignClient("loans")
public interface LoansFeignClient {

	@RequestMapping(method = RequestMethod.POST, value = "myLoans", consumes = "application/json")
	List<Loans> getLoansDetails(@RequestBody Customer customer);
}
```
### \accounts\src\main\java\com\eazybytes\accounts\service\client\CardsFeignClient.java
```java
package com.eazybytes.accounts.service.client;

import java.util.List;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.eazybytes.accounts.model.Cards;
import com.eazybytes.accounts.model.Customer;

@FeignClient("cards")
public interface CardsFeignClient {

	@RequestMapping(method = RequestMethod.POST, value = "myCards", consumes = "application/json")
	List<Cards> getCardDetails(@RequestBody Customer customer);
}
```
- In order to fetch the cards and loans details using Feign client from accounts microservice, update the **AccountsController.java** to expose a new REST API 
  **/myCustomerDetails** like we discussed inside the course. Your **AccountsController.java** should look like below,
### \accounts\src\main\java\com\eazybytes\accounts\controller\AccountsController.java
```java
/**
 * 
 */
package com.eazybytes.accounts.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import com.eazybytes.accounts.config.AccountsServiceConfig;
import com.eazybytes.accounts.model.Accounts;
import com.eazybytes.accounts.model.Cards;
import com.eazybytes.accounts.model.Customer;
import com.eazybytes.accounts.model.CustomerDetails;
import com.eazybytes.accounts.model.Loans;
import com.eazybytes.accounts.model.Properties;
import com.eazybytes.accounts.repository.AccountsRepository;
import com.eazybytes.accounts.service.client.CardsFeignClient;
import com.eazybytes.accounts.service.client.LoansFeignClient;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.ObjectWriter;

/**
 * @author Eazy Bytes
 *
 */

@RestController
public class AccountsController {
	
	@Autowired
	private AccountsRepository accountsRepository;

	@Autowired
	AccountsServiceConfig accountsConfig;
	
	@Autowired
	LoansFeignClient loansFeignClient;

	@Autowired
	CardsFeignClient cardsFeignClient;
	
	@PostMapping("/myAccount")
	public Accounts getAccountDetails(@RequestBody Customer customer) {

		Accounts accounts = accountsRepository.findByCustomerId(customer.getCustomerId());
		if (accounts != null) {
			return accounts;
		} else {
			return null;
		}

	}
	
	@GetMapping("/account/properties")
	public String getPropertyDetails() throws JsonProcessingException {
		ObjectWriter ow = new ObjectMapper().writer().withDefaultPrettyPrinter();
		Properties properties = new Properties(accountsConfig.getMsg(), accountsConfig.getBuildVersion(),
				accountsConfig.getMailDetails(), accountsConfig.getActiveBranches());
		String jsonStr = ow.writeValueAsString(properties);
		return jsonStr;
	}
	
	@PostMapping("/myCustomerDetails")
	public CustomerDetails myCustomerDetails(@RequestBody Customer customer) {
		Accounts accounts = accountsRepository.findByCustomerId(customer.getCustomerId());
		List<Loans> loans = loansFeignClient.getLoansDetails(customer);
		List<Cards> cards = cardsFeignClient.getCardDetails(customer);

		CustomerDetails customerDetails = new CustomerDetails();
		customerDetails.setAccounts(accounts);
		customerDetails.setLoans(loans);
		customerDetails.setCards(cards);
		
		return customerDetails;

	}

}
```
- Create the below three model classes inside **accounts** microservice which are needed by **/myCustomerDetails** REST API,
### \accounts\src\main\java\com\eazybytes\accounts\model\Loans.java
```java
package com.eazybytes.accounts.model;

import java.sql.Date;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Getter
@Setter
@ToString
public class Loans {

	private int loanNumber;

	private int customerId;

	private Date startDt;

	private String loanType;

	private int totalLoan;

	private int amountPaid;

	private int outstandingAmount;

	private String createDt;

}
```
### \accounts\src\main\java\com\eazybytes\accounts\model\Cards.java
```java
package com.eazybytes.accounts.model;

import java.sql.Date;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Getter
@Setter
@ToString
public class Cards {

	private int cardId;

	private int customerId;

	private String cardNumber;

	private String cardType;

	private int totalLimit;

	private int amountUsed;

	private int availableAmount;

	private Date createDt;

}
```
### \accounts\src\main\java\com\eazybytes\accounts\model\CustomerDetails.java
```java
/**
 * 
 */
package com.eazybytes.accounts.model;

import java.util.List;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

/**
 * @author EazyBytes
 *
 */
@Getter
@Setter
@ToString
public class CustomerDetails {
	
	private Accounts accounts;
	private List<Loans> loans;
	private List<Cards> cards;

}
```
- Restart the **accounts** microservice and test the feign client changes done by invoking the endpoint  http://localhost:8080/myCustomerDetails through Postman by passing 
  the below request in JSON format. You should get the response from the accounts microservices which has all the details related to account, loans and cards.
  ```json
  {
    "customerId": 1
  }
  ```
-  Generate the docker images and push them into Docker hub by following the similar steps we discussed in the previous sections.
- Now write **docker-compose.yml** files inside **accounts/docker-compose** folder for each profile with the following content,

### \accounts\docker-compose\default\docker-compose.yml
	
```yaml
version: "3.8"

services:

  configserver:
    image: eazybytes/configserver:latest
    mem_limit: 700m
    ports:
      - "8071:8071"
    networks:
     - eazybank
   
  eurekaserver:
    image: eazybytes/eurekaserver:latest
    mem_limit: 700m
    ports:
      - "8070:8070"
    networks:
     - eazybank
    depends_on:
      - configserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 15s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: default
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      
  accounts:
    image: eazybytes/accounts:latest
    mem_limit: 700m
    ports:
      - "8080:8080"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: default
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
  
  loans:
    image: eazybytes/loans:latest
    mem_limit: 700m
    ports:
      - "8090:8090"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: default
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
    
  cards:
    image: eazybytes/cards:latest
    mem_limit: 700m
    ports:
      - "9000:9000"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: default
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
      
networks:
  eazybank:
```
### \accounts\docker-compose\dev\docker-compose.yml
	
```yaml
version: "3.8"

services:

  configserver:
    image: eazybytes/configserver:latest
    mem_limit: 700m
    ports:
      - "8071:8071"
    networks:
     - eazybank
   
  eurekaserver:
    image: eazybytes/eurekaserver:latest
    mem_limit: 700m
    ports:
      - "8070:8070"
    networks:
     - eazybank
    depends_on:
      - configserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 15s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: dev
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      
  accounts:
    image: eazybytes/accounts:latest
    mem_limit: 700m
    ports:
      - "8080:8080"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: dev
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
  
  loans:
    image: eazybytes/loans:latest
    mem_limit: 700m
    ports:
      - "8090:8090"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: dev
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
    
  cards:
    image: eazybytes/cards:latest
    mem_limit: 700m
    ports:
      - "9000:9000"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: dev
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
      
networks:
  eazybank:
```
### \accounts\docker-compose\prod\docker-compose.yml
	
```yaml
version: "3.8"

services:

  configserver:
    image: eazybytes/configserver:latest
    mem_limit: 700m
    ports:
      - "8071:8071"
    networks:
     - eazybank
   
  eurekaserver:
    image: eazybytes/eurekaserver:latest
    mem_limit: 700m
    ports:
      - "8070:8070"
    networks:
     - eazybank
    depends_on:
      - configserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 15s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: prod
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      
  accounts:
    image: eazybytes/accounts:latest
    mem_limit: 700m
    ports:
      - "8080:8080"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: prod
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
  
  loans:
    image: eazybytes/loans:latest
    mem_limit: 700m
    ports:
      - "8090:8090"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: prod
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
    
  cards:
    image: eazybytes/cards:latest
    mem_limit: 700m
    ports:
      - "9000:9000"
    networks:
      - eazybank
    depends_on:
      - configserver
      - eurekaserver
    deploy:
      restart_policy:
        condition: on-failure
        delay: 30s
        max_attempts: 3
        window: 120s
    environment:
      SPRING_PROFILES_ACTIVE: prod
      SPRING_CONFIG_IMPORT: configserver:http://configserver:8071/
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eurekaserver:8070/eureka/
      
networks:
  eazybank:
```
-  Stop any running microservices inside your eclipse
-  Based on the active profile that you want start the microservices, open the command line tool where the **docker-compose.yml** is present and run the docker compose command 
   **"docker-compose up"** to start all the microservices containers with a single command. All the running containers can be validated by running a docker command 
   **"docker ps"**.
-  To validate if individual microservices like **accounts, loans & cards** are able to register themselves with **eurekaserver**, invoke the Eureka Dashboard URL
   http://localhost:8070 through browser and validate the same. To test the feign client changes, invoke the endpoint  http://localhost:8080/myCustomerDetails through Postman 
   by passing the below request in JSON format. You should get the response from the accounts microservices which has all the details related to account, loans and cards.
```json
{
    "customerId": 1
}
```
-  Stop the three running containers by executing the docker compose command **"docker-compose down"** from the location where **docker-compose.yml** is present.
---
### HURRAY !!! Congratulations, you successfully set up Eureka Server and Feign client inside microservices network using Spring Cloud Netflix Eureka.
---
