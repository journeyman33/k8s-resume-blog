---
layout:      post
title:       "Civo Commands"
subtitle:    ""
description: "My reference to civo setup and civo commands plus"
excerpt: "My reference civo setup and civo commands"
date:        "2024-03-04"
author:      "Charles Vosloo"
image:       ""
tags:  
   - mynotes
categories:  ["mynotes" ]
URL: "/2024/03/10/civo_comands/"
---
### Civo: Setup and Teardown 



### Some civo aliases 
kubernetes = k3s, k8s, kube  
applications = apps, addons, marketplace  
save = save, add, store, create, new

ecom = the name of the cluster   
ecom = the name of the kubernetes namespace used

### Set up Civo api key (done once)

Get API key from civo Dashboard > login at https://dashboard.civo.com/   
#### On laptop 
brew install civo  
civo apikey add my-civo  DAb75..   
civo apikey ls (confirm)   

###  Setup 

> civo kubernetes create ecom --remove-applications=traefik2-nodeport --applications traefik2-loadbalancer,cert-manager --cni-plugin cilium  -n 1 -s g4s.kube.medium create-firewall  --wait --save --merge --switch

```civo kubernetes config ecom --save``` (adds kubeconfig to ~/.kube/config)  
The flags used above ``--save --merge --switch`` do the same.   
```k ctx``` (confirm the current cluster is 'ecom' with kubectl ctx krew plugin)

```kubectl create namespace ecom```   
```k ns ecom``` (set namespace using the kubectl ns krew plugin)

###  Cofirm what is installed:
```civo k3s apps ls```  (list everyting in the marketplace)
civo k3s apps show cert-manager ecom        
civo k3s apps show traefik2-loadbalancer ecom      
civo loadbalancer show civo-kube-system-traefik  
```civo k3s show ecom```  (everything inside the ecom cluster)

###  Add additional marketplace apps to existing ecom cluster (skip) 
civo k3s apps add argocd --cluster ecom   
civo k3s show ecom               
civo k3s apps show argocd ecom     

### Exposing ecom to HTTPS, Traefik and cert-manager:

> cd ~/k8s-resume-challenge/kubernetes/civo  
(cloned from htps://github.com/journeyman33/k8s-resume-challenge)   
k apply -f clusterissuer.yaml  
civo k3s show ecom   

> #### From output get DNS name and place it in both commonName and dnsNames in the certification.yaml file

> vim certification.yaml      
k apply -f certification.yaml

> ### Match again the DNS name and place it in both the host and hosts section of ingress.yaml
> vim ingress.yaml  
k apply -f ingress.yaml

> k get ing (confirm)

#### Apply the manifests

❯ ls  ~/k8s-resume-challenge/kubernetes/deploy-civo  
mysql-cm-db-load-script.yaml  
mysql-deployment.yaml  
mysql-service.yaml    
website-service.yaml  
mysql-cm.yaml  
mysql-secret.yaml        
website-deployment.yaml  
  
```K apply -f ~/k8s-resume-challenge/kubernetes/deploy-civo/```  

### Place domain name in browser  e.g.
256cd9d5-1098-40d9-baf7-aa1351716a3d.lb.civo.com

### Or test the domain name  on cli
curl -k 256cd9d5-1098-40d9-baf7-aa1351716a3d.lb.civo.com

### Tear down 'ecom' cluster
```civo k3s remove ecom cluster```  
```nvim  ~/.kube/config```  
(manually delete 'ecom' lines under _cluster_, _context_ and _user_ section)


