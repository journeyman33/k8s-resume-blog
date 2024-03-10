---
title:       "Week Three"
subtitle:    "Kubernetes automation and persistance"
description: ""
date:        "2024-03-05"
author:      "Charles Vosloo"
image:       ""
tags:        ["tag1", "tag2"]
categories:  ["Tech" ]
---
## Step 6: Implement Configuration Management
### Task: Add a feature toggle to the web application to enable a “dark mode” for the website.

Modify the Web Application: Add a simple feature toggle in the application code (e.g., an environment variable FEATURE_DARK_MODE that enables a CSS dark theme).
Use ConfigMaps: Create a ConfigMap named feature-toggle-config with the data FEATURE_DARK_MODE=true.
Deploy ConfigMap: Apply the ConfigMap to your Kubernetes cluster.
Update Deployment: Modify the website-deployment.yaml to include the environment variable from the ConfigMap.
Outcome: Your website should now render in dark mode, demonstrating how ConfigMaps manage application features.
## Step 7: Scale Your Application
### Task: Prepare for a marketing campaign expected to triple traffic.

Evaluate Current Load: Use kubectl get pods to assess the current number of running pods.
Scale Up: Increase replicas in your deployment or use kubectl scale deployment/ecom-web --replicas=6 to handle the increased load.
Monitor Scaling: Observe the deployment scaling up with kubectl get pods.
Outcome: The application scales up to handle increased traffic, showcasing Kubernetes’ ability to manage application scalability dynamically.
## Step 8: Perform a Rolling Update
### Task: Update the website to include a new promotional banner for the marketing campaign.

Update Application: Modify the web application’s code to include the promotional banner.
Build and Push New Image: Build the updated Docker image as yourdockerhubusername/ecom-web:v2 and push it to Docker Hub.
Rolling Update: Update website-deployment.yaml with the new image version and apply the changes.
Monitor Update: Use kubectl rollout status deployment/ecom-web to watch the rolling update process.
Outcome: The website updates with zero downtime, demonstrating rolling updates’ effectiveness in maintaining service availability.
## Step 9: Roll Back a Deployment
### Task: Suppose the new banner introduced a bug. Roll back to the previous version.

Identify Issue: After deployment, monitoring tools indicate a problem affecting user experience.
Roll Back: Execute kubectl rollout undo deployment/ecom-web to revert to the previous deployment state.
Verify Rollback: Ensure the website returns to its pre-update state without the promotional banner.
Outcome: The application’s stability is quickly restored, highlighting the importance of rollbacks in deployment strategies.
## Step 10: Autoscale Your Application
### Task: Automate scaling based on CPU usage to handle unpredictable traffic spikes.

Implement HPA: Create a Horizontal Pod Autoscaler targeting 50% CPU utilization, with a minimum of 2 and a maximum of 10 pods.
Apply HPA: Execute kubectl autoscale deployment ecom-web --cpu-percent=50 --min=2 --max=10.
Simulate Load: Use a tool like Apache Bench to generate traffic and increase CPU load.
Monitor Autoscaling: Observe the HPA in action with kubectl get hpa.
Outcome: The deployment automatically adjusts the number of pods based on CPU load, showcasing Kubernetes’ capability to maintain performance under varying loads.
## Step 11: Implement Liveness and Readiness Probes
### Task: Ensure the web application is restarted if it becomes unresponsive and doesn’t receive traffic until ready.

Define Probes: Add liveness and readiness probes to website-deployment.yaml, targeting an endpoint in your application that confirms its operational status.
Apply Changes: Update your deployment with the new configuration.
Test Probes: Simulate failure scenarios (e.g., manually stopping the application) and observe Kubernetes’ response.
Outcome: Kubernetes automatically restarts unresponsive pods and delays traffic to newly started pods until they’re ready, enhancing the application’s reliability and availability.
## Step 12: Utilize ConfigMaps and Secrets
### Task: Securely manage the database connection string and feature toggles without hardcoding them in the application.

Create Secret and ConfigMap: For sensitive data like DB credentials, use a Secret. For non-sensitive data like feature toggles, use a ConfigMap.
Update Deployment: Reference the Secret and ConfigMap in the deployment to inject these values into the application environment.
Outcome: Application configuration is externalized and securely managed, demonstrating best practices in configuration and secret management.
## Step 13: Document Your Process
### Finalize Your Project Code: Ensure your project is complete and functioning as expected. Test all features locally and document all dependencies clearly.
Create a Git Repository: Create a new repository on your preferred git hosting service (e.g., GitHub, GitLab, Bitbucket).
Push Your Code to the Remote Repository
Write Documentation: Create a README.md or a blog post detailing each step, decisions made, and how challenges were overcome.