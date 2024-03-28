---
layout:      post
title:       "civo commands"
subtitle:    ""
description: "My reference to civo setup and civo commands plus"
excerpt: "My reference civo setup and civo commands"
date:        "2024-03-10"
author:      "Charles Vosloo"
image:       ""
tags:  
   - mynotes
categories:  ["mynotes" ]
URL: "/2024/03/10/civo_comands/"
---
## civo cli commands


### some civo aliases 
kubernetes = k3s, k8s, kube  
applications = apps, addons, marketplace  
save = save, add, store, create, new

ecom = the name of my cluster ` ` ecom = applicaion namespace 

### Set up civo api key

Get API key from civo Dashboard > login at https://dashboard.civo.com/
civo apikey add my-civo  DAb75..
civo apikey ls (confirm)

### 1. Setup 

civo kubernetes create ecom --remove-applications=traefik2-nodeport --applications traefik2-loadbalancer,cert-manager --cni-plugin cilium  -n 1 -s g4s.kube.medium create-firewall  --wait --save --merge --switch

civo kubernetes config ecom --save (adds kubeconfig to ~/.kube/cofig)
k ctx    (confirm current cluster with krew plugin)

kubectl create namespace ecom 
k ns ecom (krew ns plugin)

### 2. Cofirm what is installed:
civo k3s apps ls  (list everyting in the marketplace)
civo k3s apps show cert-manager ecom        
civo k3s apps show traefik2-loadbalancer ecom      
civo loadbalancer show civo-kube-system-traefik  
civo k3s show ecom  (everything inside the ecom cluster)

### 3. Add additional marketplace apps to existing ecom cluster...  
civo k3s apps add argocd --cluster ecom   
civo k3s show ecom               
civo k3s apps show argocd ecom     

### Exposing ecom app to HTTPS, Traefik and cert-manager:

cd ~/k8s-resume-challenge/kubernetes/civo (cloned from htps://github.com/journeyman33/k8s-resume-challenge)
k apply -f clusterissuer.yaml
civo k3s show ecom 

### from output get DNS name and place it in both commonName and dnsNames in the certification.yaml file

vim certification.yaml
k apply -f certification.yaml

### test

k apply dp.yaml
k apply svc.yaml

### match again the DNS name and place it in both the host and hosts section of ingress.yaml
vim ingress.yaml
k apply -f ingress.yaml

 k get ing
NAME            CLASS    HOSTS                                              ADDRESS         PORTS     AGE
nginx-ingress   <none>   256cd9d5-1098-40d9-baf7-aa1351716a3d.lb.civo.com   212.2.242.153   80, 443   77s

### place domain name in browser
256cd9d5-1098-40d9-baf7-aa1351716a3d.lb.civo.com

### or test the domaain name  on cli
curl -k 256cd9d5-1098-40d9-baf7-aa1351716a3d.lb.civo.com


### remove cluster ecom cluster
civo k3s remove ecom cluster
nvim  ~/.kube/config (manually delete 'ecom' cluster, context and user sections)

------------

ls ~/k8s-resume-challenge/civo  
❯ ls  
certificate.yaml    cm-db.yaml   dp-mysql.yaml      ingress.yaml  website-deployment.yaml  webste-service.yaml
clusterissuer.yaml  cm-env.yaml  ingress-http.yaml  nginx.yaml    website-service.yaml

k apply -f cm-env.yaml 
k apply -f cm-db.yaml
k apply -f dp-mysql.yaml 
k apply -f website-deployment.yaml    >  k expose deployment web --port 80   (all that is required)
k apply -f website-service.yaml









--------old -------

civo kubernetes create civo -n 1 -s g4s.kube.medium --cluster-type k3s --create-firewall --firewall-rules "6443" --region NYC1 --wait --save --merge --switch

civo k3s applications add traefik2-loadbalancer,cert-manager --cluster civo    no!!!!!

civo kubernetes remove civo (CLUSTER_NAME)

civo kubernetes create civo --applications traefik2-loadbalancer,cert-manager -n 1 -s g4s.kube.medium create-firewa
ll   --wait --save --merge --switch


civo kubernetes applications add reloader --cluster <CLUSTER-NAME>

civo k3s create civo --remove-applications=traefik2-nodeport --applications traefik2-loadbalancer,cert-manager

civo kubernetes create apps-demo-cluster --nodes=2 --applications=Redis,Linkerd --remove-applications=metrics-server

$ civo kubernetes applications add Longhorn --cluster=apps-demo
Added Longhorn 0.5.0 to Kubernetes cluster apps-demo-cluster


civo kubernetes create apps-demo-cluster --nodes=2 --applications=Redis,Linkerd --remove-applications=metrics-server

-------old -----



civo kubernetes create test --remove-applications=traefik2-nodeport --applications traefik2-loadbalancer,cert-manager --cni-plugin cilium  -n 1 -s g4s.kube.medium create-firewall  --wait --save --merge --switch