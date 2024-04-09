# Modernizing Legacy Infrastructure with Docker

## Background

You work for a medium-sized software company that has been in operation for over a decade. Over the years, the company has developed several applications using various technologies, resulting in a diverse tech stack. However, managing these applications has become increasingly challenging due to their monolithic nature and dependencies on specific server configurations.

## Current Situation

One of the company's flagship applications, a customer management system, was developed several years ago using a traditional lift and shift approach. The application comprises a frontend written in AngularJS, a backend implemented in Java using Apache Tomcat, and a MySQL database. Additionally, the application relies on Memcache for caching and RabbitMQ for message queuing.

## Challenges

- **Dependency Hell**: Each component of the application has its own dependencies and requires specific configurations. Managing these dependencies across different environments (development, testing, production) is time-consuming and error-prone.
  
- **Scalability Issues**: As the application grows, scaling becomes a challenge. Adding more instances of the application or its components requires manual intervention and often leads to inconsistencies.

- **Resource Management**: Allocating resources for each component of the application is inefficient. Some components may be over-provisioned, while others are underutilized, leading to wasted resources.

## Solution: Dockerization

To address these challenges, you propose modernizing the customer management system by containerizing it using Docker and orchestrating the containers with Docker Compose.

## Benefits of Dockerization

### 1. Simplified Deployment and Management

- **Containerized Components**: Each component of the application (frontend, backend, database, caching, message broker) is packaged as a Docker container, encapsulating its dependencies and configuration.
  
- **Consistent Environments**: Docker ensures consistency across development, testing, and production environments, reducing the likelihood of deployment issues.

### 2. Enhanced Scalability and Flexibility

- **Scalable Architecture**: Docker's lightweight containers enable easy scaling of individual components based on demand. You can spin up additional containers dynamically to handle increased load.

- **Microservices Architecture**: By breaking down the monolithic application into smaller, loosely coupled services, you can adopt a microservices architecture, allowing for independent development, deployment, and scaling of each service.

### 3. Resource Optimization

- **Resource Isolation**: Docker's containerization ensures resource isolation, preventing one component from monopolizing system resources.

- **Efficient Resource Utilization**: Docker Compose allows you to define resource constraints for each container, ensuring efficient utilization of available resources.

## DevOps Project: Containerized Lift and Shift

This project involves containerizing a previous lift and shift project, which consists of a front-end using Nginx as a reverse proxy, a backend Java application hosted using Apache Tomcat, MySQL as the database, Memcache as a database proxy, and RabbitMQ as the message broker. The orchestration is done using Docker and Docker Compose.

### Project Structure

- `Docker-files/`
  - `app/` : Contains Dockerfile for the backend application hosted on Tomcat.
  - `web/` : Contains Dockerfile for the Nginx reverse proxy.
  - `db/` : Contains Dockerfile for MySQL database.
- `nginvproapp.conf` : Nginx configuration file.
- `db_backup.sql` : SQL backup file for initializing MySQL database.

## Dockerfiles

### Backend Application (Tomcat)

#### Dockerfile (`Docker-files/app/Dockerfile`)

```Dockerfile
FROM openjdk:11 AS BUILD_IMAGE
RUN apt update && apt install maven -y
RUN git clone -b docker https://github.com/seunayolu/devopsproject.git
RUN cd devopsproject && mvn install

FROM tomcat:9-jre11

RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=BUILD_IMAGE devopsproject/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080
CMD ["catalina.sh", "run"]
```

This Dockerfile starts by building the backend Java application using Maven and OpenJDK 11. Then, it copies the built WAR file into the Tomcat webapps directory.

### Nginx Reverse Proxy

#### Dockerfile (`Docker-files/web/Dockerfile`)

```Dockerfile
FROM nginx

RUN rm -rf /etc/nginx/conf.d/default.conf
COPY nginvproapp.conf /etc/nginx/conf.d/vproapp.conf
```

This Dockerfile configures Nginx as a reverse proxy by removing the default configuration and adding a custom configuration file `nginvproapp.conf`.

### MySQL Database

#### Dockerfile (`Docker-files/db/Dockerfile`)

```Dockerfile
FROM mysql:8.0.33

ENV MYSQL_ROOT_PASSWORD="devclouddbpass"
ENV MYSQL_DATABASE="accounts"

ADD db_backup.sql /docker-entrypoint-initdb.d/db_backup.sql
```

This Dockerfile sets up MySQL database by configuring the root password, creating a database named "accounts", and adding an SQL backup file to initialize the database.

## Docker Compose

The `docker-compose.yml` file orchestrates the entire application, including MySQL, Memcache, RabbitMQ, PHPMyAdmin, Tomcat application server, and Nginx proxy server.

```DockerCompose
Define the services for the application

services:
  # MySQL DB Service
  devclouddb:
    build:
      context: ./Docker-files/db
    image: oluwaseuna/devclouddb
    container_name: devclouddb
    ports:
      - "3306:3306"
    volumes:
      - devclouddbdata:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=devclouddbpass
      - MYSQL_DATABASE=accounts
  
  # phpmyadmin to Manage MySQL DB

  phpmyadmin:
    image: phpmyadmin
    container_name: phpmyadmin
    restart: always
    ports:
      - "8081:80"
    environment:
      PMA_HOST: devclouddb
      PMA_USER: root
      PMA_PASSWORD: devclouddbpass
    depends_on:
      - devclouddb
  
  # Memcache Container

  devcloudmemcache:
    image: memcached
    container_name: devcloudmemcache
    ports:
      - "11211:11211"
  
  # Rabbit MQ Container

  devcloudrmq:
    image: rabbitmq
    container_name: devcloudrmq
    ports:
      - "15672:15672"
    environment:
      - RABBITMQ_DEFAULT_USER=guest
      - RABBITMQ_DEFAULT_PASS=guest

  # Tomcat App Server Container

  devcloudapp:
    build:
      context: ./Docker-files/app
    container_name: devcloudapp
    image: oluwaseuna/devcloudapp
    ports:
      - "8080:8080"
    volumes: 
      - devcloudappdata:/usr/local/tomcat/webapps

  # Nginx Proxy Server for the Tomcat App Container

  devcloudweb:
    build: 
      context: ./Docker-files/web
    container_name: devcloudweb
    image: oluwaseuna/devcloudweb
    ports:
      - "80:80"
volumes:
  devcloudappdata:
  devclouddbdata:
```

## Usage

1. Clone this repository.
2. Edit any configuration files if necessary.
3. Run `docker-compose up --build` to build and start all containers.
4. Access the application at `http://yourip:80` and PHPMyAdmin at `http://yourip:8081`.

## Conclusion

By containerizing the lift and shift project using Docker and Docker Compose, the deployment, management, and scalability have been simplified, allowing for easy integration of additional services and seamless connectivity between components.
