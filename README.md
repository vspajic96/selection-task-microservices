# selection-task-microservices

This project consists of 2 microservices, Service A and Service B. The goal is to perform monetary transactions on a user account.
Service A receives POST requests with JSON payload containing transaction information, and sends it to Service B. Service B then updates the account information using the received event.

# Setting up the project
To clone this repository with all the submodules, execute the command:

`git clone --recurse-submodules https://github.com/vspajic96/selection-task-microservices` (Git 2.13 and later)
`git clone --recursive https://github.com/vspajic96/selection-task-microservices` (Git 1.9 and later)

There are two ways to run the project on your system:
 
1. Running each component individually
2. Running the project using docker-compose (recommended)

## Running each component individually
### Prerequisites:
* JRE 8 (https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)
* RabbitMQ (https://www.rabbitmq.com/download.html)
* MySQL (https://dev.mysql.com/doc/mysql-installation-excerpt/5.7/en/)

### Running: 
1. Create a MySQL database with the name db_example, and a user with the following credentials: name - springuser; password- ThePassword. Then grant all privileges to the user.
  ```
  mysql> create database db_example; -- Create the new database
  mysql> create user 'springuser'@'%' identified by 'ThePassword'; -- Creates the user
  mysql> grant all on db_example.* to 'springuser'@'%'; -- Gives all the privileges to the new user on the newly created database
  ```
2. Run RabbitMQ server with default configuration 
`rabbitmq-server`

3. Run the microservices from the root project directory
```
java -jar selection-task-serviceA/build/libs/service-a-0.0.1-SNAPSHOT.jar
java -jar selection-task-serviceB/build/libs/service-b-0.0.1-SNAPSHOT.jar

```

## Running the project using Docker Compose
### Prerequisites:
* Docker (https://docs.docker.com/install/)
* Docker Compose (https://docs.docker.com/compose/install/)

### Running: 
1. From the project's root directory, run the command: 
`docker-compose up`

Note: You may need root privileges to execute Docker and Docker Compose commands.

**WARNING**: Service B might fail and throw exceptions while trying to boot. This is expected behaviour because it needs to have the database server up and ready before it can start. Whenever the fails to boot it will restart until database is ready, then after the boot succeeds it will run normally. So just wait until everything is ready before trying to use the services.

2. If you want to remove created containers after you're no longer using the services, run the command:
`docker-compose down`

# Using the services
There are two ways of using the services: 
1. Using the front-end UI
2. Sending requests manually using a tool like Postman (https://www.getpostman.com/)
## Using the front-end UI
After you have set everything up, you can access the front-end on localhost:8080/index.html

![image](https://i.imgur.com/GM3nJtj.png)

When you submit your transaction, a single table row will appear displaying your current account balance and timestamp of latest change.
If you refresh the page and want to display the balance without making a transaction, simply submit an empty or 0 valued transaction and the balance will display.

Alternatively, you can view the balance in JSON format on localhost:8081/balance.

## Sending requests manually using Postman or similar tool
After you have set everything up, you can send HTTP POST requests to localhost:8080/send with JSON payload that contains information about the transaction you want to perform.
JSON payload must be formatted as following: 
```           
   {
    "amount": 10,
    "currency": "EUR"
   }
               
```
Amount must not be lower than -100000000 or higher than -100000000, and currency must be "EUR".

To view your balance, send a HTTP GET request to localhost:8081/balance
