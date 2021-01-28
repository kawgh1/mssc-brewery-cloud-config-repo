![Cloud Config Diagram](https://raw.githubusercontent.com/kawgh1/kwg-microservices-brewery/master/cloud-config-server-diagram1.png)


### Default Port Mappings - For Single Host (Cloud)
| Project ||
| --------| -----|
| **[KWG MICROSERVICES BREWERY](https://github.com/kawgh1/kwg-microservices-brewery)** ||
| **Service Name** | **Port** | 
| | |
| **[Brewery Beer Service](https://github.com/kawgh1/mssc-beer-service)** | 8080 |
| **[Brewery Beer Order Service](https://github.com/kawgh1/mssc-beer-order-service)** | 8081 |
| **[Brewery Beer Inventory Service](https://github.com/kawgh1/mssc-beer-inventory-service)** | 8082 |
| **[Inventory Failover Service](https://github.com/kawgh1/mssc-inventory-failover)** | 8083 |
| **[Netflix Eureka Server](https://github.com/kawgh1/brewery-eureka-server)** | 8761
| **[Spring Cloud Gateway Server](https://github.com/kawgh1/mssc-brewery-gateway)** | 9090
| **[Spring Cloud Config Server](https://github.com/kawgh1/mssc-spring-cloud-config-server)** | 8888
| | |
| | |
| | |
| **[Brewery Cloud Config Repo](https://github.com/kawgh1/mssc-brewery-cloud-config-repo)** |  -----|


- ### Server Side Application Configuration
    - Setting up a single application using Spring Cloud Configuration
        - Create directories for each of the Spring Boot Microservices
        - Add configurations for active profiles
        - **Goal** - setup application with profile configuration and test in Postman
        
    - Spring Cloud Config RESTful Endpoints
        - .Configuration Endpoints available endpoints:
            - **/{application}/{profile}[/{label}]**
            - /{appliucation}-{profile}.yml
            - /{label}/{application}-{profile}.yml
            - /{application}-{profile}.properties
            -/{label}/{application}-{profile}.properties
            
        - if you do /{application} you will get 404 Not Found
        - if you do /{application}/default you will get properties
        - John notes he has never had a use for labels in configuration
            - Suggests, if you need it, question *why* you need it
            
            
### - Cloud Security

- Spring Cloud Security
- Property Encryption / Decryption
	- At Rest Encryption with Spring CLoud Config
		- Spring Cloud Configuration supports property encryption and decryption
		- Should be used for sensitive data and passwords
	- Java Cryptography Extension (JCE)
		- Prior to Java 8 u162 required additional setup
		- Older examples will refer to download and install JCE
		- Included by default in Java 11+

- Configuration
	- Spring Cloud Configuration will store encrypted properties as:
		- {cyper}<your encrypted value here>
	- When a Spring Cloud Config client requests an encrypted property the value is decrypted and presented to the client in the request
	- Must set a symmetric key in property 'encrypt.key' - should prefer setting this as an environment variable
	- Asymmetric (public / private) keys also supported - see docs for details

- Encryption / Decryption
	- Spring CLoud Configuration provides endpoints for property encryption / decryption
		- POST /encrypt - will encrypt body of post
		- POST /decrypt - will decrypt body of post

- Security Retrospective

	- Problems with HTTP Basic Autentication
		- Eureka Clients sending id / passwords plain text
		- Other clients send in header with base64 encoding
			- Easily converted to plain text
		- Sent in each request - thus more exposure to attack
		- HTTPS can be used to mitigate security riks with HTTP Basic

	- Problems with using User ID / Password
		- Sending password to multiple servers
		- Validation overhead
			- ie, need to verify credentials with authentication server, which becomes a scalability bottleneck
		- Mitigation - Use tokens such as JWT which can be trusted and authenticated by each service
	
	- Encryption in Flight
		- HTTPS for RESTful services
			- Should be a 'must' for public networks
			- Should be highly considered for internal networks
				- There is a CPU cost to using HTTPS - but less of concern than in the past
		- Use encrypted connections for network communication to databases and message brokers (ActiveMQ - JMS, etc.)
			- Typically is not, options vary per implementation

	- Encryption at rest:
		- Consider using encrypted file systems
		- Databases and message brokers have encryption options for data stored
		- Don't overlook backups. Backup files should also be encrypted