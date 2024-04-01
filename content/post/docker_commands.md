---
title:       "Docker Commands"
subtitle:    ""
description: ""
date:        "2024-03-04"
author: Charles Vosloo
image:       ""
tags:        ["tag1", "tag2"]
categories:  ["mynotes" ]
---
## How to build and push Web-app Dockerfile from local environment
cd ~/k8s-resume-challenge/web-app  
cat Dockerfile

FROM php:7.4-apache   
RUN docker-php-ext-install mysqli   
COPY ./Web-app /var/www/html/     
EXPOSE 80    
ENV MYSQL_HOST=mysql-service    

docker build -t journeyman/ecom-web:v**3** .

docker run -p 8080:80 journeyman/ecom-web:v3 
docker run --name myweb -it -d  -p 8080:80 journeyman/ecom-web:v1     
docker push journeyman/ecom-web:v3   
