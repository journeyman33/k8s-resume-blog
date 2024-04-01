---
title:       "Week Three"
subtitle:    "Kubernetes automation and persistance"
description: ""
date:        "2024-03-20"
author: "Charles Vosloo"
image:       ""
tags:        ["tag1", "tag2"]
categories:  ["Tech" ]
---
## Step 6: Implement Configuration Management
### Task: Add a feature toggle to the web application to enable a “dark mode” for the website.

I first attempted this without reading the instructions carefully. It does not ask to create a physical day night switch on the web app to toggle between dark mode and light mode, but rather to create a kubernetes config map to toggle the settings with the default set to dark mode.       

This involves modifying the index.php and editing or creating a custom styles.css file that is not overriden  by the Bootstrap framework. I initially thought this requires PHP knowledge, but rather it is run using a javascript function. I was reminded that PHP runs on the backend and Javascript on the frontend in the browser.


## Step 7: Scale Your Application
### Task: Prepare for a marketing campaign expected to triple traffic.
Scaling is about increasing or decreasing the number of pods an application runs based on the load. This can be done statically or dynamically:  

1. ``` kubectl scale deployment web --replicas=6 ```  
2. ```kubectl autoscale deployment web --cpu-percent=50 --min=2 --max=10```

What happens when the marketing campaign ends and the traffic to our ecomerce site drops? Will we remember to run the command to scale the application down?

Clearly the second option is better. 

## Step 8: Perform a Rolling Update
### Task: Update the website to include a new promotional banner for the marketing campaign.

Rolling updates are a core kubernetes feature: change the image of a running application without experiencing any downtime.

I created a cool banner with the help of canva.com advertising my new favourite cloud provider Civo and added it to the index.php file.

The code base has now changed: The head section has a new style element and the body section has a new image tag ecom-banner2.png. 
A new version ecom-web:v2 of the image needs to built.  The Dockerfile line  ```COPY ./Web-app/ /var/www/html/``` enables all the new changes. Once pushed to Dockerhub, we can now:  

```kubectl rollout status deployment web```  
(check the current image)    
```kubectl set image deployment web web=journeyman/ecom-web:v2```  
(v2 now will now replace v1)   
```kubectl rollout undo deployment web```  
(go back to the previous version)  
```kubectl rollout history deployment web```   
(check the history of image changes)  



## Step 9: Roll Back a Deployment
### Task: Suppose the new banner introduced a bug. Roll back to the previous version.

Identify Issue: After deployment, monitoring tools indicate a problem affecting user experience.
Roll Back: Execute kubectl rollout undo deployment/ecom-web to revert to the previous deployment state.
Verify Rollback: Ensure the website returns to its pre-update state without the promotional banner.
Outcome: The application’s stability is quickly restored, highlighting the importance of rollbacks in deployment strategies.
## Step 10: Autoscale Your Application
### Task: Automate scaling based on CPU usage to handle unpredictable traffic spikes.

The imperative command:   
```kubectl autoscale deployment web --cpu-percent=50 --min=2 --max=10```  
works, but what happens if we want to alter the configuration without issuing a new command? Or maybe we are running a GitOps workflow where kubectl commands are not applied to the cluster? To generate a config file and to add it to the directory that contains our existing yaml manifests files use the following command:  

```kubectl autoscale deploy web --min 2 --max 10 --cpu-percent 50  --dry-run=client -oymal > ~/k8s-resume-challenge/kubernetes/deploy-civo/hpa.yaml```  

On a one a node cluster g4s.kube.small (1 CPU 2 RAM 40 SSD) running our ecomerce project the CPU clocks in at 86%. This is already in the danger zone. But still, How many concurrent hits would it take to take for the system to fail?

###### Install Apache Bench (ab)  
'ab'= the cli tool

> ```ab -n 1000 -c 10 http://956e33be-b7cf-4bbd-85b8-b8a2779c386a.lb.civo.com/``` 

One thousand hits with a concurrency of 10 crashes the server.

The remedy:   
**Create a total of 3 nodes:**  
civo kubernetes node-pool scale ecom 824. --nodes   
**Create a fourth medium size node:**   
civo k8s node-pool create ecom --size g4s.kube.medium     
civo k8s node-pool ls ecom    
**Delete one of the small nodes**   
civo k8s node-pool instance-delete ecom --node-pool-id 824.. --instance-id 880...  
  

## Step 11: Implement Liveness and Readiness Probes
### Task: Ensure the web application is restarted if it becomes unresponsive and doesn’t receive traffic until ready.

These probes can be added to both the web deployment and the mysql deployment.  

```nvim ~/k8s-resume-challenge/kubernetes/deploy-civo/website-deployment.yaml```

```yaml 
kind: Deployment
metadata:
  name: web
  namespace: ecom
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web
        image: journeyman/ecom-web:v1
        ports:
        - containerPort: 80
        envFrom:
        - configMapRef:
            name: web-env-vars
        - secretRef:
            name: mysql-secret
```



Add to deploy.spec.template.spec.containers:
```yaml 
        readinessProbe:
          httpGet:
            path: /index.php
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
        livenessProbe:
          httpGet:
            path: /index.php
            port: 80
          initialDelaySeconds: 15
          periodSeconds: 5
```
Although both the readiness and liveness probe do the same thing:  check the existence of /index.php on port 80 on the web container which is inside the web deployment, they however behave differently:  
> The readiness probe checks if the container is ready to serve traffic.  
If the test fails the container is removed as a valid endpoint   

> the liveness probe checks if the container is still responsive and healthy.  
If the test fails the container will get restarted


```nvim ~/k8s-resume-challenge/kubernetes/deploy-civo/mysql-deployment.yaml```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  namespace: ecom
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mariadb:lts
        envFrom:
        - configMapRef:
            name: web-env-vars
        - secretRef:
            name: mysql-secret
        volumeMounts:
        - name: db-load-script-volume
          mountPath: /docker-entrypoint-initdb.d
      volumes:
      - name: db-load-script-volume
        configMap:
          name: db-load-script
```
Add to deploy.spec.template.spec.containers:
```yaml
    readinessProbe:
      tcpSocket:
        port: 3306
      initialDelaySeconds: 5
      periodSeconds: 5
    livenessProbe:
      tcpSocket:
        port: 3306
      initialDelaySeconds: 10
      periodSeconds: 5
```
Both the readiness and liveness probe check if the mysql-service is up by performing a tcpSocket check to mysql running on port 3306. 

> The readiness probe ensures that the container is ready to serve traffic.   
If the test fails no new request will be accepted

> The liveness probe checks if the container is still responsive and healthy.    
If the test fails the pod will get restarted.





## Step 12: Utilize ConfigMaps and Secrets
### Task: Securely manage the database connection string and feature toggles without hardcoding them in the application.

I created 3 configuration files to store sensitive and non sensitive information used to connect to and populate the mysql database:

> 1. mysql-cm-db-load-script.yaml - database content
> 2. mysql-cm.yaml - for the mysql host, port and database
> 3. mysql-secret.yaml - for the mysql user,password and root password.
  
web-env-vars and mysql-secret were added to web deployment as a config map and a secret.  
db-load-script-volume got added to mysql deployment as a config map volume.

I ran these kubectl commands and followed them with kubectl apply -f 
            
> 1. kubectl create configmap db-load-script --namespace=ecom   --from-file=db-load-script.sql=./db-load-script.sql   
--dry-run=client -oyaml > mysql-cm-db-load-script.yaml  

> 2. kubectl create configmap web-env-vars --namespace=ecom   --from-literal=MYSQL_HOST=mysql-service --from-literal=MYSQL_PORT=3306  --from-literal=MYSQL_DATABASE=ecomdb  
--dry-run=client -oyaml > mysql-cm.yaml  

> 3.  kubectl create secret generic mysql-secret --namespace=ecom   --from-literal=MYSQL_USER=user --from-literal=MYSQL_PASSWORD=secret    --from-literal=MYSQL_ROOT_PASSWORD=secret  
--dry-run=client -oyaml > mysql-secret.yaml  






