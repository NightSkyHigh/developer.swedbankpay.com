---
title: Test Client
sidebar:
  navigation:
  - title: Payment Api
    items:
    - url: /giftcard/
      title: Introduction
    - url: /giftcard/operations
      title: Operations
    - url: /giftcard/security
      title: Security
    - url: /giftcard/payment-client
      title: Test Client
    - url: /giftcard/other-features
      title: Other Features
---

----
## Prerequisites 

* Java 11
* VueJS
* Maven
* Postgres

## Project setup

```
vas-payment-api-client
├─┬ backend     → backend module with Spring Boot code
│ ├── src
│ └── pom.xml
├─┬ frontend    → frontend module with Vue.js code
│ ├── src
│ └── pom.xml
└── pom.xml     → Maven parent pom managing both modules
```

## First App run

__NB! The application expects a PostgreSQL server to be running on localhost with a username `test` and password `test` to exist.__  
__This can automatically be configured if PostgreSQL server is started in docker with environment variables `POSTGRES_USER=test` and `POSTGRES_PASSWORD=test` are set (See [docker-compose.yml](https://github.com/PayEx/vas-payment-api-client/blob/master/docker-compose.yml)).__ 

Clone the repository from Github: [Payment Client](https://github.com/PayEx/vas-payment-api-client): 

Inside the root directory, do a: 

```bash
mvn clean install
```

Run the Spring Boot App:

```bash
mvn --projects backend spring-boot:run
```

Now go to http://localhost:8080/ and have a look at your new client.

## Testing application

1. Add a new Merchant with the details provided by PayEx
2. Click on Gift Cards and add a new Gift card
 

## Build docker image:
```bash
mvn --projects backend clean compile jib:dockerBuild
```
    
## Deploy to local docker:
```bash
docker-compose up -d    
```