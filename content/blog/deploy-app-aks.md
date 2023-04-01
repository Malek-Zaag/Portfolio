---
title: "I Deployed application on Azure Kubernetes Service"
description: "In this blog, I'll be sharing my experience of deploying an application on Azure Kubernetes Service (AKS)."
dateString: Nov 2022
draft: false
tags: ["Kubernetes", "Azure", "Kubernetes Cluster","Azure Kubernetes Service","Web Development"]
weight: 102
cover:
    image: "/blog/deploy-app-aks/cover.jpg"
---

In this article, we are going to demonstrate how to deploy a web application in AKS (Azure Kubernetes Service) .

**Azure Kubernetes Service**

![](https://miro.medium.com/v2/resize:fit:700/1*eRd_vLThUJ3kcEp49CID4w.png)

Azure Kubernetes Service is a managed container orchestration service based on the open source Kubernetes system, which is available on the Microsoft Azure public cloud. An organization can use AKS to handle critical functionality such as deploying, scaling and managing Docker containers and container-based applications.

**Azure Container Registry**

![](https://miro.medium.com/v2/resize:fit:600/1*_ypMqRLb6CkDCZQrX4dxwg.png)

Azure Container Registry

Azure Container Registry is a private registry service for building, storing, and managing container images and related artifacts. In this quickstart, you create an Azure container registry instance with the Azure portal.

Now we are going to push our images into the container registry:

docker push myprivaterepo.azurecr.io/client  
docker push myprivaterepo.azurecr.io/server

Now we can see our images pushed successfully to the remote registry :

![](https://miro.medium.com/v2/resize:fit:700/1*o3gsQkAqSTO7tNnndDxijg.png)

**Create our Kubernetes cluster**

We are going now to create our cluster, we connect to the azure portal then we search for kubernetes service .

![](https://miro.medium.com/v2/resize:fit:700/1*t0te_-NCq7yO7CnD_i84Pg.png)

For the main page, we set the resource group and the cluster name. For the node agent, we set it to Standard_B2s which is the cheapest one available :

![](https://miro.medium.com/v2/resize:fit:700/1*dHNPlRjV8zm8bfMXHviw5w.png)

For networking inside the cluster, we used the default Kubenete network configuration and Calico for connectivity between pods and services :

![](https://miro.medium.com/v2/resize:fit:700/1*tLHuVdqWC_Oq8VCXNNDitg.png)

Finally, we connect our docker registry to the cluster so we can pull images :

![](https://miro.medium.com/v2/resize:fit:700/1*59giraSf_RJLX_lIH6JGPQ.png)

and we hit Create .

**Deploy the app**

Now, we need to connect to the cluster

az account set --subscription ####################  
az aks get-credentials --resource-group aks-rg1 --name mk-aks

We deploy our deployments and services using :

 kubectl apply -f .\client-deploy.yaml  
 kubectl apply -f .\server-deploy.yaml  
 kubectl apply -f .\client-svc.yaml  
 kubectl apply -f .\server-svc.yaml

> Note : We are going to use the public cloud LoadBalancer to achieve that our front is served in the internet .

## Nginx reverse-proxy :

![](https://miro.medium.com/v2/resize:fit:324/1*QHrIz-Zm8TU_INJyJOmbbw.png)

nginx

To achieve forwarding the API requests to our ClusterIP backend, we are going to use a nginx server which we will serve as a reverse-proxy .

a  **reverse proxy**  is the application that sits in front of back-end applications and forwards client (e.g. browser) requests to those applications. Reverse proxies help increase scalability, performance, resilience and security. The resources returned to the client appear as if they originated from the web server itself.

My nginx.conf :

server {  
    listen 80;  
  
    location /api {  
        proxy_http_version 1.1;  
        proxy_set_header Upgrade $http_upgrade;  
        proxy_set_header Connection "upgrade";  
        proxy_set_header Host $host;  
        proxy_cache_bypass $http_upgrade;  
        proxy_pass http://${API}:${PORT}/api;   
    }  
  
    location / {  
        root /usr/share/nginx/html;  
        index index.html index.htm;  
        try_files $uri $uri/ /index.html;  
    }  
  
    error_page 500 502 503 504 /50x.html;  
  
    location = 50x.html {  
        root /usr/share/nginx/html;  
    }  
}

In this configuration, every request for “/api” we will be forwarded to “[http://${API}:${PORT}/api](https://medium.com/$%7BAPI%7D:$%7BPORT%7D/api)”. For example, a request a /api/user will be forwarded to  [http://${API}:${PORT}/api](https://medium.com/$%7BAPI%7D:$%7BPORT%7D/api)/user .

## Testing out things :

So, our services are now available and we can see our the public IP of our loadbalancer that serves our frontend application :

![](https://miro.medium.com/v2/resize:fit:700/1*KoqxkyODcaKDbJMRFMS50g.png)

we access it and our application is running successfully :

![](https://miro.medium.com/v2/resize:fit:700/1*BppHDjvoUoDmWZQ8xBd-bw.png)

We try to create and account and login :

![](https://miro.medium.com/v2/resize:fit:399/1*IHeBFrl1hv9kD3VaTGnelg.png)

![](https://miro.medium.com/v2/resize:fit:700/1*2nUumO_8ZYF2gykK30tn_Q.png)

Now we create a new user to test our API :

![](https://miro.medium.com/v2/resize:fit:700/1*L1ntuz-a919lrOqXolwm7A.png)

and our API works successfully :

![](https://miro.medium.com/v2/resize:fit:700/1*bo4e7XRxZ2wZy7gSrMHREA.png)

I hope you enjoyed reading the article and you learnt how to deploy a fullstack web application using the Azure Kubernetes Service .