---
title:       "Week Two"
subtitle:    "Kubernetes Management and scaling"
description: ""
date:        "2024-03-15"
author:      "Charles Vosloo"
image:       ""
tags:        ["tag1", "tag2"]
categories:  ["Tech" ]
---
## Step 3: Set Up Kubernetes on a Public Cloud Provider
Since it takes less than 2 minutes it was my first step. This command being the final iteration:

```civo kubernetes create ecom –remove-applications=traefik2-nodeport –applications traefik2-loadbalancer,cert-manager –cni-plugin cilium --nodes 1 --size g4s.kube.medium create-firewall –wait –save –merge –switch```

You can interact with Civo in three ways:

1. The Dashboard
2. Civo CLI 
3. Terraform

I used the CLI.  I was able to set up a bash script to automate the next step  of exposing the cluster to https, Traefic LoadBalancer and cert-manager shown in the scripts section of my code-repository  https://github.com/journeyman33/kubernetes-resume-challenge, but actually was unnecessary for testing and only required if you are paranoid like me and don't want to leave a cluster running doing nothing. I outline these commands in the MYNOTES > civo commands section of this blog. 


## Step 4: Deploy Your Website to Kubernetes
Kubernetes runs on yaml. Also called configuration files or manifests. We have already created three. Two config maps and one secret. Both applications, web and mysql, require a deployment config and a service config.  Mysql communicates internally with web so it only needs a ClusterIP whereas web need a service of type LoadBalancer because it communicates outside. The cloud provider responds to the the loadbalancer request. 

I am not very good at remembering kubernetes yaml, to get a template, I default to a habit I learned from preparing for the CNCF exams:  

> In our example, we have dockerized our web application pushed to to Dockerhub, and now we need to use this image to create a container for kubernetes to manage.  We know the following:  

   - image: journeyman/ecom-web:v1  
   - deployment name: web   
   - a configmap and secret containing values needs to pass to the deloyment  

Use the following imperative command to create a declarative template:  

```kubectl -n ecom create deployment web --image journeyman/ecom-web:v1 --dry-run=client -oyaml > website-deployment.ymal```    

All that remains is to figure out the configmap spec and the secret spec when editing the website-deployment.ymal, then run:

```kubectl apply -f website-deployment.yaml```

## Step 5: Expose Your Website

```k expose deploy web --name web --type LoadBalancer --port 80 --dry-run=client -oyaml > website-service.yaml```  
```nvim website-service.yaml```  
```k apply -f website-service.yaml```

