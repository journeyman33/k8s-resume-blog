---
layout:     post
title:      "Cloud Architectural Overview"
subtitle:   "Civo"
date:       "2024-03-26"
author:     "Charles Vosloo"
image:      "img/k8s-resume-banner.png"
# image:      "img/discord_chat3.png"
# baseurl:    "{{ .Site.BaseURL }}"
---
### Cloud Architecture and Overview
I once suggested using Civo on a new project and was immediately dismissed in favour of AWS. A Civo managed Kubernetes cluster costs less than half of the the price of any of the three hyperscalers EKS, AKS and GKE, and being a dedicated Kubernetes cloud provider, running a lightweight version of Kubernetes (k3s), the set up times are super fast. This means, for a developer, it is cost effective in a second sense: you can start up a cluster and then shut it down each time you work on it.

#![screenshot](/img/discord_chat3.png)
#![screenshot](https://journeyman33.github.io/k8s-resume-blog/img/discord_chat3.png)
#![screenshot](https://github.com/journeyman33/k8s-resume-blog/img/half_page2.png)
#![screenshot]({{ .Params.baseurl }}/img/half_page2.png) # no!
#![screenshot]({{< baseurl >}}/img/discord_chat3.png)   # no!
#![screenshot]({{ .Site.BaseURL }}/img/half_page2.png)   # no?
<img src="/k8s-resume-blog/img/discord_chat3.png" alt="screenshot">


> "No guarantees it will have all the features you need"  

What features are missing? 

Compared to AWS I am sure the list is long: networking and security features, the IAM service, CloudTrail, managed databases (Amazon RDS, DynamoDB), S3 storage. Plus the AWS SLA (Service level agreement) guarantees High Availability and reliability for top-end enterprise production workloads. AWS Lambda (serverless) ?  

> But, isn't Kubernetes ***the*** platform of the cloud?  


#### My Setup  

![cloud-arch](/img/cloud_diagram2.png)

A convenient next step after setting up git and github is to use Github Actions as part of the CI/CD implementation to automate the build process. The workflow arrows on this diagram are a best practice goal.   
<!-- After that, setting up Flux or ArgoCD provides a full CI/CD GitOps environment. -->

While the mysql image, mariadb:lts remains fixed, it is the ecom-web image that gets  continuously built.   
 
<!-- The [Docker commands](docker_commands.md) in the MYNOTES menus shows the process of building a new ecom-web image and pushing it to Gihub.  

```kubectl set image deployment/web web=journeyman ecom-web:v2```    
adds the new image to the web deployment and  
``` kubectl -n ecom rollback  ```  
allows you to rollback to verison v1. 

Github Acitons can automate this process. -->

##### The interations of ecom-web:  
  
> v1 - the configuration of ecom-web:v1 with mariadb:lts  
> v2 - add a advertising banner + day/night toggle  
> v3 - add integration to OpenFaas - Faasd server  


The installation of applications from the Civo Marketplace is very useful. The civo Marketplace can be found on the UI Dashboard or on the client CLI where you can get a description of each application or get a list of the application with:  

```civo kubernetes applications ls ```
![civo_applications](/img/civo_applications2.png)

You can install applications with: 

``` civo kubernetes applications add cert-manager traefic2-loadbalancer --cluster ecom ```  

Leveraging Helm, everything then gets integrated into the existing cluster using default or sensible defaults and the idea at *most* is that it works seamlessly. <!--Helm is used under the hood.-->  
<!-- , but the integration is smoother than with vanilla helm. -->


### Improving the environment

What is wrong with our ecomerce application?  
    
##### Security  
RBAC (Role-Based Access Control) is not set up. All pods and services within the cluster can communicate with each other without any restrictions.

No Network Policies defined where ingress and egress traffic based on IP addresses, ports, or labels are restricted to minimize the attacks on the network.

No third party solutions ues to manage secrets securely.

##### Devops Best Practices

No argocd or flux installed: We are not applying GitOps.    
No real Infrastructure as code system used like Terraform or Crossplane.   
No CI/CD pipeline set up with Github actions, Jenkins, Gitlab or Azure Devops.  
No monitoring and observability tools set up.    

So, what's next?  

Apply RBAC and Network policy rules. Go out and install Hashicorp vault, argocd, terraform, Prometheus and Grafana and hope that you get it right with  all the services communicating with each other with the new security features?  

No! 
>There is no need to reinvent the wheel. 

I like the approach outlined below.

### Kubefirst

Kubernetes and the Kuberetes ecosystem has many moving parts.  Putting them all together does actually take for ever, hence the rise of platform engineering. A better approach is to spend the time, after the fact, figuring out how the parts fit together rather than trying to put them together first.


This one line civo installation command, achieves the goal: 

``` civo kubernetes applications add kubefirst --cluster ecom ```  

We are effectively installing a ready made fully functioning Platform as a Service! In order for the Kubefirst platform to work at the start it needs to be opinionated which is what is required from any running system. However, Kubefirst also has it's own 'marketplace' which means different parts are interchangeable. So what you get is a beginners IDP (Independent Developers Platform) that works out of the box. Below is an architectural diagram showing the defaults. Many moving parts to control many moving parts.        

![kubefirst-architecture](/img/kubefirst-arch.png)  

<!-- Here is what we get
--
--
At the outset eveyting works.  
We don't have to spend the time figuring out why the cluster is craashin before  we have learnt the system.  -->