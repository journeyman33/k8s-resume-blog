---
layout:     post
title:      "Cloud Architecture"
subtitle:   "Civo"
date:       "2024-03-05"
author:     "Charles Vosloo"
image:      "img/k8s-resume-banner.png"
---
### Architecture
I once suggested using Civo on a new project and was immediately dismissed in favour of AWS. A Civo managed Kubernetes cluster costs less than half of the the price of any of the three hyperscalers EKS, AKS and GKE, and being a dedicated Kubernetes cloud provider, running a lightweight version of Kubernetes (k3s), the set up times are super fast. This means it is cost effective in a second sense: you can start up a cluster and then shut it down each time you work on it.


![screenshot](/img/discord_chat3.png)



> "No guarantees it will have all the features you need"  

What features are missing? 

Compared to AWS: advanced networking features, integration with services like IAM, CloudTrail, Amazon RDS, DynamoDB and S3. The AWS SLA (Service level agreement) offers HA (HIgh Availablity) and reliability for top-end enterpise production workloads. AWS Lambda?  

Isn't Kubernetes *the* platform of the cloud?  


#### My Setup  





![cloud-arch](/img/cloud_diagram.png)

The most convenient next step after setting up git and github is to use Github Actions as part of the CI/CD implementation. This diagram is a best practice goal.   
<!-- After that, setting up Flux or ArgoCD provides a full CI/CD GitOps environment. -->

While the mysql image, mariadb:lts remains fixed, it is the ecom-web image that requires developing.
 
The [Docker commands](docker_commands.md) in the MYNOTES menus shows the process of building a new ecom-web image and pushing it to Gihub.  

```kubectl set image deployment/web web=journeyman ecom-web:v2```    
adds the new image to the web deployment and  
``` kubectl -n ecom rollback  ```  
allows you to rollback to verison v1. 

Github Acitons can automate this process.

##### The interations of ecom-web:  
  
> v1 - the configuration of ecom-web:v1 with mariadb:lts image  
> v2 - add a advertising banner + day/night switch  
> v3 - add integration to OpenFaas - Faasd server on local LAN  


The installation of applications from the Civo Marketplace is very usefull. The civo Marketplace can be found on the UI Dashboard or on the client CLI where you can get a description of each application or get a list of the application with:  

```civo kubernetes applications ls ```
![civo_applications](/img/civo_applications2.png)

You can install an application with: 

``` civo kubernetes applications add kubefirst --clster ecom ```  

Everything then gets integrated into the existing cluster using default or sensible defaults and ***presumably*** works seemlessly. <!--Helm is used under the hood.-->  
<!-- , but the integration is smoother than with vanilla helm. -->


### Improving the Developer Environment

What is wrong with our ecom-web application?  

<!-- Nothing necessarily; DevOps is about improving and automating:    -->
A few things:

no secret management  
no gitops  
bash scipting (IaC)

So, what do we do next, go out and install argocd, argo workflows,   terraform, vault, ...  
and hope that they interact with each other  

No!

I like the approach outlined below:

### Kubefirst

Kubernetes has many moving parts.  Putting them all together can take for ever. That is the job of platform engineering. A better approach is to spend the time, after the fact, figuring out how the parts fit together rather than trying to put them together first.

The one line kubefirst installation command shown earlier, achieves this goal. It is a ready made fully functioning Platform as a Service. The kubefirst platform is opinionated. This is required for a running system. However, Kubefirst also has a 'marketplace' which means different parts are interchangable. So what you get is a beginners Independent Developers Platform that works out of the box. Below is an architectual diagram showing the defaults. Many moving parts to control many moving parts.        














![kubefirst-architecture](/img/kubefirst-arch.png)
















<!-- ## Screenshots

![screenshot](/img/fullscreenshot.png)

**Post**
![screenshot](/img/post.png)

**Search**
![screenshot](/img/sitesearch.png)

**Disqus**
![screenshot](/img/disqus.png)
-->





## Describe the Task

Imagine you are going to deploy an e-commerce website. It’s crucial to consider the challenges of modern web application deployment and how containerization and Kubernetes (K8s) offer compelling solutions:

Scalability: How can your application automatically adjust to fluctuating traffic?
Consistency: How do you ensure your application runs the same across all environments?
Availability: How can you update your application with zero downtime?
Containerization, using Docker, encapsulates your application and its environment, ensuring it runs consistently everywhere. Kubernetes, a container orchestration platform, automates deployment, scaling, and management, offering:

Dynamic Scaling: Adjusts application resources based on demand.
Self-healing: Restarts failed containers and reschedules them on healthy nodes.
Seamless Updates & Rollbacks: Enables zero-downtime updates and easy rollbacks.
By leveraging Kubernetes and containerization for your challenge, you embrace a scalable, consistent, and resilient deployment strategy. This not only demonstrates your technical acumen but aligns with modern DevOps practices.