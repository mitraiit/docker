# docker CRUD app in PHP/MYSQL
Sample web app in docker image

Created by Surya Mitra | M20AIE314

We have basically created a simple webapp using PHP and MySQL(db). The app allows users to perform CRUD operations.

Web servers :

1. Apache(Php)


2. PHPMyadmin for mysql operations


Folder structure is given below :

```
/php-apache-mysql/
├── apache
│   ├── apache_php.conf
│   └── Dockerfile
├── Cache
│   ├── cbc42e4c979f69f0_0
│   └── index-dir
│       └── the-real-index
├── docker-compose.yml
├── Network Persistent State
├── php
│   └── Dockerfile
├── public
│   ├── config.php
│   ├── create.php
│   ├── delete.php
│   ├── dump
│   │   └── dump.sql
│   ├── error.php
│   ├── index.php
│   ├── read.php
│   └── update.php
└── README.md
```
### Running the app :


We have to clone these files and Docker and Docker compose should be installed locally, we can use "docker-compose up" from the root of the project to run this project, and open http://localhost:8080 to see it running.

### Application operations :


"dump.sql" creates a table 'employees' in the mydb database. 

"config.php" connects the php code with the 'employees' table of mydb database.

"index.php" creates the frontend grid which displays records from "employees" table.

"create.php" generates web form that is used to insert records in the employees table.

"read.php" retrieves the records from the employees table.

"update.php" updates records in employees table.

"delete.php" deletes records in employees table.

"error.php" will be displyed if a request is invalid.

### Docker compose :

It will allow us to define the dependencies for the services, networks, volumes, etc as code

#### docker-compose.yml
```
version: "3"
services:
  php:
    build: './php/'
    volumes:
      - ./public:/var/www/html/
  apache:
    build: './apache/'
    depends_on:
      - php
      - mysql
    ports:
      - "8080:80"
    volumes:
      - ./public:/var/www/html/
  mysql:
    image: mysql:5.7
    restart: always
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
      # Initially creates employees table
      - ./public/dump:/docker-entrypoint-initdb.d/
    environment:
      MYSQL_ROOT_PASSWORD: "passwd"
      MYSQL_DATABASE: "mydb"
      MYSQL_USER: "user1"
      MYSQL_PASSWORD: "passwd"
volumes:
    db_data:
```

Here we are decoupling Apache, PHP and Mysql by building them out into separate containers.We will be using the below Dockerfiles to decouple them : 

#### apache/Dockerfile
```
FROM httpd:2.4.33-alpine

RUN apk update; \
    apk upgrade;

COPY apache_php.conf /usr/local/apache2/conf/apache_php.conf
RUN echo "Include /usr/local/apache2/conf/apache_php.conf" \
    >> /usr/local/apache2/conf/httpd.conf
```

#### php/Dockerfile
```
FROM php:7.2.7-fpm-alpine3.7

RUN apk update; \
    apk upgrade;

RUN docker-php-ext-install mysqli
```




