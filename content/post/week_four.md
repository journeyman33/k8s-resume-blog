---
title:       "Week Four"
subtitle:    "Extra Credit"
description: ""
date:        "2024-03-25"
author: "Charles Vosloo"
image:       ""
tags:        ["tag1", "tag2"]
categories:  ["Tech" ]
---
### Extra Credit
##### Three tasks were suggested here:        
     
     

1. Package Everything in Helm
2. **Implement Persistent Storage**
3. Implement Basic CI/CD Pipeline  
4. <span style="color:green;">**Make use of Serverless**</span> 

#### 2 Persistent Storage

When the mysql deployment pods go down due to restarts, updates, or scaling the data is lost.  Therefore we want to persist the contents of the container directory  ***/var/lib/mysql*** with outside storage.    

```kubectl get storageclass -oyaml```

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: civo-volume
provisioner: csi.civo.com
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```
The presence of a StorageClass configuration indicates that Civo Cloud offers a default persistent storage solution to dynamically provision storage with PVs (Persistent volumes) and PVC's (Persistent Volume Claims). 

Here is how it is done:

First create a pvc   
```cat >> pvc.yaml``` (this time we are pasting from the docs)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-vol
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 4Gi
```
```bash
❯ k create -f pvc.yaml  
persistentvolumeclaim/mysql-vol created
```

Update the mysql-deployment.yaml  
Add the following snippet at ```spec.template.spec.containers[0]``` 
```yaml
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-vol
```
```bash
❯ k apply -f mysql-deployment.yaml   
deployment.apps/mysql configured  
```

```bash
❯ k get pv   
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS    
pvc-2f330444-...   4Gi        RWO            Delete           Bound    ecom/mysql-vol   civo-volume      43s
```
```bash
❯ k get pvc  
NAME        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE  
mysql-vol   Bound    pvc-2f330444-      4Gi        RWO            civo-volume    43m
``` 

#### 4 Serverless

Actually, I added the fourth option, Make use of Serverless. In the previous version of this challenge one of the steps was to add a visitor counter to the website using the cloud provider's serverless function which would be AWS Lambda, Google Cloud Functions or Azure functions. I wanted to do something similar **using non proprietary solutions.** On our ecomerce site additional features like email notifications, image processing, payment processing, inventory management or any kind of analytics could probably more easily be carried out using serverless or rather functions as a service. Just to prove the concept, my goal was to add the day-night toggle switch as a serverless function, but that didn't pan out.


How can one implement Open source Serverless on civo cloud?
1. OpenFunction
2. OpenFaas

OpenFunction uses Knative, a comprehensive more complex solution that requires a control plain  kubernetes cluster with a minimum of 2 nodes, 4CPU and 8GB memory plus Istio running. 

Civo is a dedicated kubernetes (k3s) cloud provider which embraces popular Open source CNCF products like OpenFaaS:  

> "a Function as a Service serverless framework that deploys event-driven functions and microservices to Kubernetes in the form of containers." 

  'Serverless' requires a server. It's only called serverless because when the function is called, from the perspective of the user, where and how the task is carried out is is unnoticed. So, how do we set up and manage an OpenFaas server which we control and own?

There are two ways to run OpenFaas:
1. faas-netes (on a kubernetes cluster)
![faas-netes](https://journeyman33.github.io/k8s-resume-blog/img/of-workflow.png)
OpenFaas makes use of the PLONK stack. While the LAMP stack (linux, apache, mysql, php) is used by our ecomerce site, The JAM stack (javascript, API, markup) is used by this hugo blog, the PLONK stack is comprised of:  
>>Prometheus - metrics and time-series  
>>Linkerd - service mesh   
>>OpenFaaS - management and auto-scaling of compute - PaaS/FaaS   
>>NATS - asynchronous message bus / queue   
>>Kubernetes - declarative, extensible, scale-out, self-healing clustering    

The second option, which I have set up, is a scaled down version of the above: the kubernetes cluster is scraped in favour of contaierd containers running each function. Easier and more accessible. The downside: loose built in kubernetes features like High Availability.

2. faasd (on a VM running containerd)  
![faasd](https://journeyman33.github.io/k8s-resume-blog/faasd-wf.png)

Instead of running it on a VM I chose to run the faasd server On Prem, on a raspberry pi. Faasd, a light weight version of OpenFaas, uses Containerd instead of kubernetes which results in it running faster.  This is procedure is explained by the creator, Alex Ellis in his book, [Serverless for everyone else](https://openfaas.gumroad.com/l/serverless-for-everyone-else)

  
The downside: This setup has a single point of failure. However, there are no cloud fees, and the raspberry pi address running on my local LAN, is routable externally thanks to the installation of inlets. The other problem particular to South Africa are daily scheduled power outages. After about a day without power my backup system will fail, the cell phone towers follow, and then not only can the server go down but the internet too.

<!-- ![laptop_pic]({{.Params.baseurl }}/img/half_page2.png) -->
<!-- ![laptop_pic]({{ .Params.baseurl }}/img/half_page2.png) -->
![laptop_pic](https://journeyman33.github.io/k8s-resume-blog/img/half_page2.png)
      

<!-- the raspberry pi address running on my local >LAN, is routable externally thanks to the installation of inlets:  -->
<!-- ![inlets](/img/inlets-concept.png) -->







