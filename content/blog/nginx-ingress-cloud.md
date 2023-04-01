---
title: "Setup nginx Ingress in cloud"
description: "In this blog, I will guide you through the process of setting up an Nginx Ingress controller in a cloud environment. "
dateString: Dec 2022
draft: false
tags: ["Kubernetes", "Azure", "Nginx Ingress Controller","Azure Kubernetes Service","Nginx Ingress"]
weight: 102
cover:
    image: "/blog/nginx-ingress-cloud/cover.jpeg"
---
This document describes how to install the NGINX Ingress Controller in your Kubernetes cluster using kubectl .

## What is Ingress ?

Ingress is a native Kubernetes resource like pods, deployments, etc. Using ingress, you can maintain the DNS routing configurations. The ingress controller does the actual routing by reading the routing rules from ingress objects stored in etcd.

## Before Ingress ?

Before the Kubernetes Ingress was stable, a custom Nginx or an HAproxy kubernetes deployment would be exposed as a Loadbalancer service for routing external traffic to the internal cluster services.

## Kubernetes Ingress : How it works?

![](https://miro.medium.com/v2/resize:fit:700/1*C0lRqJwyOR0Ww3xAwrkpBg.png)

Actual Ingress relies on two concepts

- Kubernetes Ingress Resource: Kubernetes ingress resource is responsible for storing DNS routing rules in the cluster.

- Kubernetes Ingress Controller: Kubernetes ingress controllers (Nginx/HAProxy etc.) are responsible for routing by accessing the DNS rules applied through ingress resources.

![](https://miro.medium.com/v2/resize:fit:700/1*SZx9EEu58sc7bKovceSdxA.png)

## Setup

First, we will understand all the associated Kubernetes objects by deploying Nginx controllers using YAML manifests. Once we have the understanding, we will deploy it using kubectl.

## Install NGINX Ingress Controller

An ingress controller, because it is a core component of Kubernetes, requires specific configuration to be performed at the cluster level as part of installation. The NGINX project simplifies this by providing a single deployment yaml file that captures all the required steps for the cluster configuration :

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```
Output:

![](https://miro.medium.com/v2/resize:fit:700/1*jf7B22bTxjuhRZzvlCQ0bA.png)

## Exposing the NGINX Ingress Controller

The above command will by default expose the NGINX Ingress Controller to the outside world to allow it to start receiving connections.

## Validate the NGINX Ingress Controller
```
kubectl get pods --all-namespaces -l app.kubernetes.io/name=ingress-nginx  
kubectl get services ingress-nginx-controller --namespace=ingress-nginx
```
![](https://miro.medium.com/v2/resize:fit:700/1*BELVC5tx9bpHXp3ulNGxdw.png)

## Exposing Services using NGINX Ingress Controller

Now we will be wirting the Ingress manifest :
```
apiVersion: networking.k8s.io/v1  
kind: Ingress  
metadata:  
  name: hello-world  
  annotations:  
    kubernetes.io/ingress.class: "nginx"  
    nginx.ingress.kubernetes.io/rewrite-target: /  
spec:  
  rules:  
    - http:  
        paths:  
          - pathType: Exact  
            path: /bookstore  
            backend:  
              service:  
                name: app1-svc  
                port:  
                  number: 80  
          - pathType: Exact  
            path: /library  
            backend:  
              service:  
                name: app2-svc  
                port:  
                  number: 80
```
Testing out things :

After deploying our applications manifests :

![](https://miro.medium.com/v2/resize:fit:700/1*YaLQqnBIq3iW7jPvMwLpdQ.png)

We can go the LoadBalancer IP and test things :

![](https://miro.medium.com/v2/resize:fit:700/1*ObviPhB8nHDR0ldqLgW3DA.png)

and the bookstore :

![](https://miro.medium.com/v2/resize:fit:700/1*6OH3JfgEr1dwM8jf3OBGBw.png)

So, to conclude, we demonstrated how to successfully deploy an Ingress controller in Azure Kubernetes Service and serve two ClusterIP services in the cluster .