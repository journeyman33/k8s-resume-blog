---
title:       "Week One"
subtitle:    "dockerize the eccomerce site"
description: ""
date:        "2024-03-05"
author:      "Charles Vosloo"
image:       ""
tags:        ["tag1", "tag2"]
categories:  ["Tech" ]
---
## Step 1: Certification
The Certified Kubernetes Application Developer (CKAD) course is a great resource. Also from Kodekloud, the Ultimate Certified Kubernetes CKAD mock exams was what I enjoyed the most.  I completed the CKA on 26 may 2021 and the CKS on 18 Nov 2021.
![cka](/img/certificates.png)  


<!--[![cka](/img/certificates.png)](/img/clr-vosloo-certified-kubernetes-administrator-cka-certificate.pdf) -->


## Step 2: Containerize Your E-Commerce Website and Database
### A. Web Application Containerization

The first step was to set up the civo and kubectl cli client running on ubuntu Windows WSL running with Docker Desktop installed.

There are two application to deploy to kuberetes, which I call web and mysql  

    1. Web-app (the 'learning-app-ecommerce' site cloned from kodekloudhub)  
    2. mariadb:lts (pulled form DockerHub)   

The first needs containerizing: 

```docker build -t journeyman/ecom-web:v1```

I used the following Dockerfile, arrived at through trial and error:  

```FROM php:7.4-apache  
RUN docker-php-ext-install pdo pdo_mysql mysqli
WORKDIR /var/www/html
COPY  ./Web-app/ /var/www/html/
EXPOSE 80
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

I initially included in the line:  

```COPY ./db-load-script.sql /docker-entrypoint-initdb.d/```  

but then pulled it because the task is to run this database initialization script, which populates the database, (db-load-script.sql) as a Kubernetes configMap.    

For mysql to communicate with the PHP website, requires default password(s) and usernames, which as a best practice, is not a good idea to hard code into the Dockefile or environment variables. Therefore I didn't wait for step 12 to implemented another configmap for the myaql host, port and database and to add  a kubernetes secret for the mysql user,password and root password.   

The other basic security measure is to put the project in a namespace.

