Terraform is a great IaC tool that I will be using to deploy an EKS cluster with two worker nodes and auto-scaling.

Terraform will also be used to deploy the sock-shop application, Prometheus, and nginx ingress controller on the EKS cluster.

I'd like to explain what every directory means so you don't get confused.
WHAT MAKES IT WORK?

In the repository above there are two directories and three files and I am going to explain to you how they work together… sit tight
THE EKS DIRECTORY

The EKS directory is the first directory on the repository and it contains all our code in Terraform to spin up our EKS cluster.. but not just the EKS cluster.

we are also setting up the environment where the EKS cluster will run. For instance, we are creating the VPC, subnets, internet gateway, EKS roles, node group roles, and also a bastion host if you choose to use the private instance.
THE KUBERNETES DIRECTORY

Now this directory is split into four subdirectorys:

    Ingress-rule
    Micro-service
    Nginx-controller
    Prometheus-helm

Let’s dive deep into each of these!
MICRO-SERVICE:

The micro-service directory holds all the code that would be used to deploy the sock-shop application. And 10 services make up this micro-service

No changes need to be made to this file as the codes are exactly the way it is on their GitHub repository. It was only converted to terraform.
PROMETHEUS-HELM

The Prometheus-helm directory holds all the code that would be needed to install Prometheus and Grafana to our EKS cluster for monitoring.

The values.YAML file is the default configuration of Prometheus. If you want Prometheus to behave in a certain way that’s where you go.
NGINX-CONTROLLER

The nginx-controller directory is where we provide the code in Terraform to install the nginx-ingress controller that would help us route traffic within our cluster

Also in this directory, we are creating our route 53 hosted zone with CNAME records for Grafana and sock-shop

You can make a change to the domain name used in the variables.tf file if you have a live domain you want to use.

Lastly, we are also requesting an SSL certificate from ACM (Amazon certificate manager) for our domain names and sub domains respectively
INGRESS-RULE

The ingress-rule directory holds the code in Terraform that would be used to create our ingress rule and route the traffic to our nginx ingress load balancer which will route the traffic to the selected service.

In this case, there are just two - the micro-service (sock-shop) and Grafana - you can also observe that they are both opened on port 80 in both files

Here also is where you make a change to the domain name in the hostname section to:

sock-shop.yourdomain.com

In both files.
THE JENKINS FILES

There are two Jenkins files present in this repository.

    cluster-Jenkinsfile
    Jenkinsfile

CLUSTER-JENKINSFILE

As you can tell the cluster-Jenkinsfile file is created for Jenkins to help us deploy the EKS cluster first
JENKINSFILE

The Jenkinsfile is where the code to run everything in the Kubernetes directory is and this is running only after the cluster-Jenkinsfile is done running

It creates all the resources in this manner:

Prometheus > microservice > ingress-rule > nginx-ingress controller 

This is because it takes a while for the ACM in the Kubernetes/nginx-ingress directory to completely run. That’s why we are running it last.
INSTALLER.sh

This is a script file that helps you set the environment for your newly launched instance

It install all the prerequisites like Jenkins, Kubectl, AWS cli and terraform
