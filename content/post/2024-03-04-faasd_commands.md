---
title:       "Faasd"
subtitle:    ""
description: "My reference to OpenFaas(Faasd) commands"
date:        2023-03-24
author:      "Charles Vosloo"
image:       ""
tags:        ["mynotes"]
categories:  ["mynotes" ]
---


### login from LAN
faas-cli login -g http://192.168.0.124 -p d1e2..

## login script 
❯ cat connect.sh  
#!/bin/bash  
export PASSWORD=d1e...  
export IP=192.168.0.127  
export OPENFAAS_URL=http://$IP:8080  
faas-cli login --gateway $OPENFAAS_URL --password $PASSWORD --username "admin"  

## list functions
❯ faas-cli ls  
Function                        Invocations     Replicas  
curl                            0               1  
figlet                          2               1  
identicon                       1               1  
nodeinfo                        1  



