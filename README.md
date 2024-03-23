# CAPSTONE--PROJECT DEPLOYMENT OF THE SOCK-SHOP APPLICATION AND PROMETHEUS ON EKS USING TERRAFORM AND JENKINS

Terraform is a great IaC tool that is going to be used to deploy an eks cluster with two worker nodes and auto scaling.

Terraform will also be used to deploy the sock-shop application, Prometheus and nginx ingress controller on the eks cluster.. 

I am going to explain what every directory is meant for so you do not get confused.


## WHAT MAKES IT WORK?

In the repository above there are two directories and three files and I am going to be explaining to you how they work together… sit tight




### THE EKS DIRECTORY

The **eks directory** is the first directory on the repository and it contains all our code in terraform to spin up our eks cluster.. but not just the eks cluster.. 

we are also setting up the environment where the eks cluster is going to run. For instance we are creating the vpc, subnets, internet gateway, eks roles, node group roles and also a bastion host if you choose to use the private instance..



### THE KUBERNETES DIRECTORY

Now this directory is split into fours sub directory:

1. **Ingress-rule**
2. **Micro-service**
3. **Nginx-controller**
4. **Prometheus-helm**


Let’s dive deep into each of this!

#### **MICRO-SERVICE**:

The **micro-service directory** holds all the code that would be used to deploy the sock-shop application. And there are 10 services that make up this micro-service 

No changes needs to be made to this file as the codes are exactly the way it is on their GitHub repository. It was only converted to terraform.


#### **PROMETHEUS-HELM**


The **Prometheus-helm directory** holds all the code that would be needed to install Prometheus and grafana to our eks cluster for monitoring.

The values.yaml file is the default configuration of Prometheus. If you want Prometheus to behave in a certain way the that’s where you go.


#### **NGINX-CONTROLLER**

The **nginx-controller directory** is where we provide the code in terraform to install our nginx-ingress controller that would help us route traffic within our cluster 

Also in this directory we are creating our route 53 hosted zone with CNAME records for grafana and sock-shop

You can make a change to the domain name used in the variables.tf file if you have a live domain you want to use.

Lastly we are also requesting an SSL certificate from ACM (Amazon certificate manager) for our domain names and sub domains respectively 


#### **INGRESS-RULE**

The ingress-rule directory holds the code in terraform that would be used to create our ingress rule and route the traffic to our nginx ingress load balancer which will the route the traffic to the selected service.

In this case there are just two - the micro-service (sock-shop) and grafana - you can also observe that they are both opened on port 80 in both files

Here also is where you make a change to the domain name in the host name section to:

```bash script
sock-shop.yourdomain.com
```

In both files..




#### **THE JENKINS FILES**

There are two Jenkins files present in this repository.

- cluster-Jenkinsfile
- Jenkinsfile

##### **CLUSTER-JENKINSFILE**

As you can tell that the cluster-Jenkinsfile file is created for Jenkins to help us deploy the eks cluster first 


##### **JENKINSFILE**

The Jenkinsfile is where the code to run everything in the kubernetes directory is and this is ran only after the cluster-Jenkinsfile is done running 

It creates all the resources in this manner:

```
Prometheus > microservice > ingress-rule > nginx-ingress controller 
```

And this is because it take a while for the acm in the kubernetes/nginx-ingress directory to completely run. That’s why we are running it last..


#### **INSTALLER.sh**

This is a script file that helps you setup the environment for your newly launched instance 

It install all the prerequisites like Jenkins, Kubectl, AWS cli and terraform



