---
title: "VM creation SaaS"
description: ""
dateString: May 2023
draft: false
tags:
  ["ReactJS", "ExpressJS", "Cloud", "Azure", "CI/CD", "Ansible", "Terraform"]
showToc: false
weight: 203
cover:
  image: "https://media.licdn.com/dms/image/D4D22AQHcTfZvQhK4Bw/feedshare-shrink_2048_1536/0/1685749989095?e=1697068800&v=beta&t=K7UTkOAu-Tf3lUIn5T0rS8IjQNpFH_QpTmTDGvYHsiQ"
---

# Development of a web interface for managing and monitoring virtual machines in the Cloud

In this article i m going to resume our end of year project that consists on the development of a web interface to create , manage and monitor virtual machines in the Cloud . The application will take CloudiVops as name and i will present for you the main features and the process of the development of this application

### **Tools to use**

- ReactJS , NodeJS , MongoDB , ChartJS

- Jenkins

- Ansible

- Terraform

- Azure

### Github Repo

[Click Here](https://github.com/Malek-Zaag/Projects)

### How CloudiVops works ?

![](https://cdn-images-1.medium.com/max/2000/1*txYenDfftaNOa8T3-M6s6w.png)

Once the user access to our application he will be face to a web interface after the creation of an account and the login the user will find a form where he can fulfill the desired infrastructure and the he can choose to configure the existent infra by our application that it will acts as an intermediary between the user and Azure because all the infra will be set up on Azure (me and my team we choosed azure so that we can use our subscription that contains 100$) our application will set up the infra and do all the configurations using pipelines and to understand more the workflow lets go through the next diagram

### workflow of the Application

<p align="center">
   <img src="https://cdn-images-1.medium.com/max/2000/1*PRxJzlsD4Pwfb45feDhuEA.png"/>
</p>

Well this can make the system of cloudivops more clear

Our system contains the frontend part developed on reactJS and the backend that is developed with expressJS and that will initiate the pipelines and will play a role on the automation of the processes .The api will contains the functions for the login and signup and the functions to trigger the pipelines

first of all the user will create and account which will be saved on our database then he will be redirected to the home page inside the home page the user will find a button to create a virtual machine or a button to navigate to the dashboard and configure the VMs that exists once the form is submitted the function called is to trigger pipeline and that make a call to jenkins api and jenkins api will initiate terraform api and this will create the desired virtual machine by a simple trigger to azure api and azure will return the details to the user through our front pages

if the user wants to configure a VM our api will trigger a second jenkins pipeline and this time jenkins api will call ansible api to configure the desired resource inside azure and then the state of the configuration will be passed to the user on the front pages .

let me now give some definitions of the used tools for the automation

### Jenkins

<p align="center">
    <img src="https://cdn-images-1.medium.com/max/2000/0*wnlEG7R_s5a4VjHN.png">
</p>

Jenkins is a Java-based program considered as an automation build tool that should be installed on a dedicated server , it is used to configure the tasks , install the tools used for the build and automatically trigger the workflow

jenkins offer different jobs the most used one and suitable for our usecase is the pipeline job

### Terraform

<p align="center">
    <img src="https://cdn-images-1.medium.com/max/2000/0*ZM74l4c8lpKKMYYr.png">
</p>

Terraform is an infrastructure as code tool that lets you build, change, and version infrastructure safely and efficiently.

Terraform automates and manages the infrastructure so it is a tool used for infrastructure provisioning

Terraform take the desired state from the provided code and set up the infrastructed that will be stored in a state file

### Ansible

<p align="center">
    <img src="https://cdn-images-1.medium.com/max/2000/0*ydjsCt5lclbA5Ral.png">
</p>

Ansible is like terraform an automation tool that helps to automate the IT tasks , by just making a configuration file we can make tasks from a machine to another and use the same file in different environments

ansible architecture is based on modules where a module is a small program that is responsible of doing the task and each module is responsible of a specific task it is pushed to the server than removed after it is done

to know on which server it is gonna be pushed ansible use the inventory list that will get the hosts names and all the data about the ansible client

the ansible playbook contains a group of modules where each task performs the action that we want to do .the ansible playbook contains also the host to describe where the tasks are going to be running and also contains the remote user to tell with which user the tasks should be executed

### Development of CloudiVops

The home page will looks like this

![HomePage](https://cdn-images-1.medium.com/max/2432/1*FrOSFrS7hIK5q_2K6aeC-w.png)

it is just a simple page that contains a description of our services , button for login and button for sign up

once logged in the email will appear in the place of the buttons of login and sign up and the page will looks like this

![After Login](https://cdn-images-1.medium.com/max/2322/1*p9bi--FUqZ41bG1nvij38w.png)

If the user wants to create a vm the vm creation will happens through this form

![](https://cdn-images-1.medium.com/max/2272/1*8UokPA4_Wsf-L6sFzhYuIA.png)

and once the vm is created the dashboard will look like this

![Dashboard](https://cdn-images-1.medium.com/max/2542/1*3E-CvYsxSYOhKFcoAnDJtA.png)

So here the user will find the IP of the machine the region and 2 buttons either to install docker or mysql and he can also delete the resource

also the dashboard contains monitoring graphs

![](https://cdn-images-1.medium.com/max/2000/1*TiCTxGfaKdSz5y_-ZgbcAw.png)

the submit of the form will initiate jenkins pipeline and the page that we got on the front will gives an idea about the state of the build of the pipeline

![](https://cdn-images-1.medium.com/max/2316/1*FBfvnemVeTDQQNx_eAWwLA.png)

means that our infrastructure is gonna be created and once the pipeline is build succesfully we got a changed status

![](https://cdn-images-1.medium.com/max/2274/1*xU0CXTghx1eqjhG6xvfZzA.png)

and the same for the ansible pipeline

The code of the pipelines , the playbooks , the terraform templates are in the github repository

So that was a brieve summary of this web application that we would like to improve it in the future by adding more features

Finally i hope that this article was usefull and it was not that much long

the full web application is in this repo where you can check also the full configuration files since the screenshots were so limited

## Was this helpful? Confusing?

If you have any questions, feel free to comment below! Make sure to follow on [Linkedin](https://www.linkedin.com/in/malekzaag/) and [github](https://github.com/Malek-Zaag) if you’re interested in similar content and want to keep learning alongside me!
