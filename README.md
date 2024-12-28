

# Complete CI/CD pipeline with EKS and private Docker registry

## Prerequisites and Technologies used

- **DigitalOcean Account**: For provisioning the server.
- **AWS Account**: For setting up ECR and EKS.
- **AWS EKS**: Kubernetes cluster for scaling application.
- **Jenkins**: Installed as a container on the DigitalOcean server.
- **Docker**: For building and pushing images.
- **AWS ECR**: Private artifact registry for docker images.
- **Git**: For version control.

## Project Overview

Complete CI/CD pipeline project that dynamically increment application version, build artifact, build docker image and push to AWS ECR registry and deploy application to kubernetes cluster by pulling image from AWS ECR regisry with the pipeline stage below:

   **Pipeline Stages**:
   - **Dynamic Application Versioning**: Automatically increments the application version.
   - **Artifact BuiEKS cluster is created using the eksctl command in the commandline. The app is deployed into the ecks cluster from the Jenkins pipeline by packaging the application with incremented version and pushing to ecr registry
To be able to deploy to Eks cluster from Jenkins, two plugins need to be installed inside jenkins container;
1. install kubectl ld**: Builds the Java Maven application artifact.
   - **Docker Image Build and Push**: Builds the Docker image and pushes it to the AWS ECR.
   - **Application Deployment**: Deploys the new application version to the EKS cluster using Kubernetes manifests.
   - **Version Commit**: Updates the application version in the Git repository.


## Motivation

The motivation for this project stems from the need to streamline application deployment processes in modern software development. By leveraging containerization, Kubernetes orchestration, and CI/CD pipelines, this project showcases:

- **Automation**: Minimizing manual intervention in deployment workflows.
- **Scalability**: Utilizing Kubernetes to scale applications based on demand.
- **Efficiency**: Reducing deployment times and ensuring consistent environments.
- **Reliability**: Integrating version control, artifact management, and container registries to enhance deployment robustness.



## Setup Instructions

### 1. Provision Server and Install Jenkins

- Create a server on DigitalOcean with appropriate firewall configuration. Open port 8080 and set jenkins to run on
- ssh into the server and install docker on the server
  ```bash
     ssh root@138.68.108.14
     apt install docker.io
  ```

- Run the Jenkins container:
        ```bash
    docker run -d -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkins/jenkins:lts
  ```
- Access Jenkins at `http://<server-ip>:8080`.

- Enter the jenkins container to retrive the admin credentials of jenkins
    ```bash
    docker exec -it 32114f01d3e8  bash
  ```

- Admin credentials for jenkins can be obtain at the path below which is used to initialize jenkins
  ```bash
     cat /var/jenkins_home/secrets/initialAdminPassword
  ```
    

- install docker inside jenkins container to be able to exeEKS cluster is created using the eksctl command in the commandline. The app is deployed into the ecks cluster from the Jenkins pipeline by packaging the application with incremented version and pushing to ecr registry
To be able to deploy to Eks cluster from Jenkins, two plugins need to be installed inside jenkins container;
1. install kubectl cute docker commands in jenkins
     ```bash
     curl https://get.docker.com/ >dockerinstall && chmod 777 dockerinstall && /dockerinstall
     chmod 666 /var/run.docker.sock
  ```
- install envsubst inside jenkins to be able to subtitute environment variable in the yaml configuration files


### 2. Create eks cluster and ecr registry

- Create a private repository in AWS ECR:
  
- Jenkins and kubernetes need the credentials of ECR to be able to push and pull images to the registry. 

- to get the password of ECR registry:
  ```bash
  aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <account-id>.dkr.ecr.<region>.amazonaws.com
  ```

- EKS cluster is created using the eksctl command in the commandline.
  ```bash
  eksctl create cluster --name capstone --region eu-central-1 --node-type t2.micro --nodes 2
  ```
- To be able to deploy to Eks cluster from Jenkins, two plugins need to be installed inside jenkins container:
- Install kubectl inside jenkins container
   ```bash
  curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/       amd64/kubectl; chmod +x ./kubectl; mv ./kubectl /usr/local/bin/kubectl
  ```
- install aws i am authenticator
  ```bash
      curl -Lo aws-iam-authenticator https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.6.11/aws-iam-authenticator_0.6.11_linux_amd64
      chmod +x ./aws-iam-authenticator
      mv ./aws-iam-authenticator /usr/local/bin
  ```
- Create .kube directory inside jenkins container in the home director "/var/jenkins_home/" and create a config file for kubernetes outside the container by substituting the cluster name , server endpoint and certificate authority data of the created cluster.
- copy the config file to the '/var/jenkins_home/.kube' directory
     ```bash
       docker cp config ip:/var/jenkins_home/.kube/
  ```

- Create credentials in kubernetes to authorize pulling image from the private registry
   ```bash
       kubectl create secret docker-registry aws_ecr_key docker-server="ecr endpoint" docker-username=AWS docker-password='add password'
  ```
 EKS cluster is created using the eksctl command in the commandline. The app is deployed into the ecks cluster from the Jenkins pipeline by packaging the application with incremented version and pushing to ecr registry
To be able to deploy to Eks cluster from Jenkins, two plugins need to be installed inside jenkins container;
1. install kubectl 


- When a build in jenkins is triggered, the application version is increased and a copy is push to the ECr registry which is pull and deployed into the kubernetes cluster

<img src='./cap/w3.png' height="80%" width="80%" alt="Disk Sanitization Steps">
<img src='./cap/w2.png' height="80%" width="80%" alt="Disk Sanitization Steps">

**files**:
   - **Jenkinsfile**: configuration for pipeline job.
   - **deployment.yaml**: yaml configuration file for deployment.
   - **service.yaml**: yaml configuration file for creating service for the deployment.
   - **dockerfile**: build application into docker image.
   - **Version Commit**: Updates the application version in the Git repository.
