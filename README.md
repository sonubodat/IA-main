# Three-Tier Application with Docker

This repository contains a three-tier application built using Docker, utilizing a multi-container setup. The application consists of three layers: presentation layer, application layer, and data layer.

## Architecture Overview


![5](https://github.com/kathan-shah1893/IA/assets/136159210/f434097f-4ec7-48c8-a3f0-e70025e0bb18))

- **Presentation Layer**: Responsible for presenting information to the user. Built with HTML, CSS, and JavaScript.
- **Application Layer**: Contains the business logic and functionality of the application. Built with JAVA.
- **Data Layer**: Stores and manages the data used by the application. Utilizes MYSQL.

## Prerequisites
- JDK 1.8 or later
- Maven 3 or later
- MySQL 5.6 or later

## Technologies 
- Spring MVC
- Spring Security
- Spring Data JPA
- Maven
- JSP
- MySQL

## Database Setup
### Installation Steps for MySQL on Ubuntu 14.04
```bash
sudo apt-get update
sudo apt-get install mysql-server
```
### Importing Database Dump
1. Locate the `accountsdb.sql` file in the `/src/main/resources` directory.
2. This file contains a MySQL dump of the database schema and data.
3. Run the following command to import the dump into the MySQL database:
```bash
mysql -u <user_name> -p accounts < accountsdb.sql
```
Replace `<user_name>` with your MySQL username. You will be prompted to enter your MySQL password after running this command.



## Getting Started

Follow these steps to run the three-tier application locally:

### Prerequisites

- Docker installed on your machine

### Steps

1. Clone this repository to your local machine:

    ```bash
    git clone https://github.com/kathan-shah1893/IA.git
    ```

2. Navigate to the project directory:

    ```bash
    cd IA
    ```

3. Database Dockerfile:

    ```bash
    
        FROM mysql:8.0.33
        LABEL "PROJECT"="VPROFILE"
        LABEL "AUTHOR"="KATHAN"

        ENV MYSQL_ROOT_PASSWORD="vprodbpass"
        ENV MYSQL_DATABASE="accounts"

        ADD db_backup.sql docker-entrypoint-initdb.d/db_backup.sql
    
    ```
4. OpenJDK Dockerfile:
    ```bash
    
        FROM openjdk:11 AS BUILD_IMAGE
        RUN apt update && apt install maven -y
        RUN git clone https://github.com/devopshydclub/vprofile-project.git
        RUN cd vprofile-project && git checkout docker &&  mvn install

        FROM tomcat:9-jre11
        RUN rm -rf /usr/local/tomcat/webapps/*
        COPY --from=BUILD_IMAGE vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
        EXPOSE 8080
        CMD ["catalina.sh","run"]
    
    ```
 5. Nginx Dockerfile:
    ```bash
        FROM nginx
        RUN rm /etc/nginx/conf.d/default.conf
        COPY nginvproapp.conf /etc/nginx/conf.d/
    ```
 6. Docker-compose.yml file:
```yaml
version: '3.8'
services:
  vprodb:
    build: ./Docker-files/db
    image: vprocontainers/vprofiledb
    container_name: vprodb
    ports:
      - "3306:3306"
    volumes:
      - vprodbdata:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWROD=vprodbpass
  vprocache01:
    image: memcached
    ports:
      - "11211:11211"
  vpromq01:
    image: rabbitmq
    ports:
      - "15672:15672"
    environment:
      - RABBITMQ_DEFAULT_USER=guest
      - RABBITMQ_DEFAULT_PASS=guest
  vproapp:
    build: ./Docker-files/app
    container_name: vproapp
    ports:
      - "8080:8080"
    volumes:
      - vproappdata:/usr/local/tomcat/webapps
  vproweb:
    build: ./Docker-files/web
    container_name: vproweb
    ports:
      - "80:80"
volumes:
  vprodbdata:
  vproappdata:
```

7. Build the Docker images

    ```bash
    docker-compose build
    ```

8. Start the Docker containers:

    ```bash
    docker-compose up -d
    ```

9. Access the application in your web browser:

    ```
    http://localhost:3000
    ```



## Docker Image

You can pull the Docker image from Docker Hub using the following command:

```bash
docker pull kathanshah1893/myvprofileapp
```
## Author

- [Kathan shah](https://github.com/Kathan-shah1893)

## Acknowledgements

- This project is inspired by [Imran teli](https://github.com/hkhcoder/vprofile-project) and their work on (https://github.com/hkhcoder/vprofile-project).
