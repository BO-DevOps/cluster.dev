# Cluster.dev - Kubernetes-based Dev Environment in Minutes

Cluster.dev is an open-source system delivered as GitHub Action or Docker Image 
for creating and managing Kubernetes clusters with simple manifests by GitOps approach.   

Designed for developers that are bored to configure Kubernetes stuff
and just need: kubeconfig, dashboard, logging and monitoring out-of-the-box.  

Based on DevOps and SRE best-practices. GitOps cluster management and application delivery.
Simple CICD integration. Easily extandable by pre-configured applications and modules. 
Supports different Cloud Providers and Kubernetes versions.

----
## Principle diagram. What it does?

![cluster.dev diagram](docs/images/cluster-dev-diagram.png)

## How it works

In background:

 - Terraform creates a "state bucket" in your cloud   account where all infrastructure objects would stored. Typically it is defined on Cloud Object Storage like AWS S3.
 - Terraform modules create Minikube/EKS/GKE/etc.. cluster, VPC and DNS zone within your Cloud Provider.
 - ArgoCD Continuous Deployment system deployed inside Kubernetes cluster enables you to deploy your applications.
 - GitHub CI runner deployed into your Kubernetes cluster and used for your apps building CI pipelines with GitHub Actions.

You receive:

 - Automatically generated kubeconfig, ssh-access, and ArgoCD UI url’s.
 - Kubernetes Dashboard, Logging(ELK), Monitoring(Prometheus/Grafana),  

## Quick Start

 1. Dedicate a separate repository for infrastructure that would be managed by `cluster.dev`. This repo would host code for your clusters, deployments and other resources managed GitOps way.  
 Next steps should be done in that repo.

 2. Obtain access credentials for your cloud account.
 For example, in AWS it is called "Programmatic Access user",and looks like: 
 ```yaml
 aws_access_key_id =  ATIAAJSXDBUVOQ4JR
 aws_secret_access_key = SuperAwsSecret
 ```
 3. Add credentials to you repo's Secrets under GitHub's: "Settings->Secrets", ex: 
 ![GitHub Secrets](/docs/images/gh-secrets.png)

 4. Create a new cluster.dev config yaml with your cluster definition: `.cluster.dev/minikube-a.yaml` :
 ```yaml
 cluster:
  name: minikube-a
  cloud: 
    provider: aws
    region: eu-central-1
  provisioner:
    type: minikube
    instanceType: "m4.large"
 ```   
 
 4. Create a Github Workflow file: `.github/workflows/main.yml`: 
```yaml 
on: [push]
jobs:
  deploy_cluster_job:
    runs-on: ubuntu-latest
    name: Deploy and Update K8s Cluster
    steps:
    - name: Checkout Repo
      uses: actions/checkout@v1
    - name: Reconcile Clusters
      id: reconcile
      uses: shalb/cluster.dev@master
      with:
     # Change setting below with path to config and creds
        cluster-config: './.cluster.dev/minikube-one.yaml' 
        cloud-user: $
        cloud-pass: $
     # end of chages
    - name: Get the execution status
      run: echo "The status $"
```
5. Commit and Push changes and follow the Github Action execution and in its output you'll receive access instructions to your cluster and its services.

## Contributing 

If you want to spread the project with your own code, you could start contributing with this quick guide: [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md)
