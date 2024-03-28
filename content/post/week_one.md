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
The Certified Kubernetes Application Developer (CKAD) course is a great resource from Kodekloud. The Ultimate Certified Kubernetes CKAD mock exams also by Kodekloud, in my opinion, offers the best learning experience. I completed the CKA on 26 may 2021 and the CKS on 18 Nov 2021.
![cka](/img/certificates.png)  


<!--[![cka](/img/certificates.png)](/img/clr-vosloo-certified-kubernetes-administrator-cka-certificate.pdf) -->


## Step 2: Containerize Your E-Commerce Website and Database
### A. Web Application Containerization
Create a Dockerfile: Navigate to the root of the e-commerce application and create a Dockerfile. This file should instruct Docker to:

Use php:7.4-apache as the base image.
Install mysqli extension for PHP.
Copy the application source code to /var/www/html/.
Update database connection strings to point to a Kubernetes service named mysql-service.
Expose port 80 to allow traffic to the web server.
Build and Push the Docker Image:

Execute docker build -t yourdockerhubusername/ecom-web:v1 . to build your image.
Push it to Docker Hub with docker push yourdockerhubusername/ecom-web:v1.
Outcome: Your web application Docker image is now available on Docker Hub.
B. Database Containerization
Database Preparation: Instead of containerizing the database yourself, you’ll use the official MariaDB image. Prepare the database initialization script (db-load-script.sql) to be used with Kubernetes ConfigMaps or as an entrypoint script.
