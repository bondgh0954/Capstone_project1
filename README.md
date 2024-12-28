# Capstone_project1

### Complete CI/CD pipeline with EKS and private Docker registry

#### Technologies used
Kubernetes
Jenkins
AWS EKS
Docker Hub
Java Maven
Linux
Docker
Git

## Project Overview

The deployment workflow includes the following steps:

1. **Server Setup**:
   - A server is provisioned on the DigitalOcean platform.
   - Jenkins is deployed as a container on the server.

2. **Docker Registry**:
   - A private AWS ECR (Elastic Container Registry) is created to store Docker images of the application.

3. **Pipeline Stages**:
   - **Dynamic Application Versioning**: Automatically increments the application version.
   - **Artifact Build**: Builds the Java Maven application artifact.
   - **Docker Image Build and Push**: Builds the Docker image and pushes it to the AWS ECR.
   - **Application Deployment**: Deploys the new application version to the EKS cluster using Kubernetes manifests.
   - **Version Commit**: Updates the application version in the Git repository.


## Motivation

The motivation for this project stems from the need to streamline application deployment processes in modern software development. By leveraging containerization, Kubernetes orchestration, and CI/CD pipelines, this project showcases:

- **Automation**: Minimizing manual intervention in deployment workflows.
- **Scalability**: Utilizing Kubernetes to scale applications based on demand.
- **Efficiency**: Reducing deployment times and ensuring consistent environments.
- **Reliability**: Integrating version control, artifact management, and container registries to enhance deployment robustness.

This setup serves as a practical example of implementing DevOps best practices in a real-world scenario, providing a reliable and repeatable process for deploying Java applications.

## Prerequisites

- **DigitalOcean Account**: For provisioning the server.
- **AWS Account**: For setting up ECR and EKS.
- **Jenkins**: Installed as a container on the DigitalOcean server.
- **Docker**: For building and pushing images.
- **kubectl**: To interact with the Kubernetes cluster.
- **AWS CLI**: To authenticate with AWS services.
- **Git**: For version control.


## Setup Instructions

### 1. Provision Server and Install Jenkins

- Create a server on DigitalOcean.
- ssh into the server and install docker on the server

    ssh root@138.68.108.14
    apt install docker.io
- Install Docker on the server.
- Run the Jenkins container:
  ```bash
   docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
  ```
- Access Jenkins at `http://<server-ip>:8080`.

Enter the jenkins container to retrive the admin credentials of jenkins

    docker exec -it 32114f01d3e8  bash
Enter the publicIp  of server and port open for jenkins container in the browser

    cat /var/jenkins_home/secrets/initialAdminPassword

install docker inside jenkins container to be able to execute docker commands in jenkins
install envsubst inside jenkins to be able to subtitute environment variable in the yaml configuration files



   


#### Step 2 : Create eks cluster and ecr registry
EKS cluster is created using the eksctl command in the commandline.
The app is deployed into the ecks cluster from the Jenkins pipeline by packaging the application with incremented version and pushing to ecr registry <br/>
To be able to deploy to Eks cluster from Jenkins, two plugins need to be installed inside jenkins container; <br/>
     1. install kubectl <br/>

      curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/       amd64/kubectl; chmod +x ./kubectl; mv ./kubectl /usr/local/bin/kubectl
     
2. install aws i am authenticator <br/>

     curl -Lo aws-iam-authenticator https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.6.11/aws-iam-authenticator_0.6.11_linux_amd64
      chmod +x ./aws-iam-authenticator
      mv ./aws-iam-authenticator /usr/local/bin

3. Create a configuration file with the credentials of the eks cluster and copy it to the home directory of jenkins container in a created .kube directory

          docker cp config ip:/var/jenkins_home/.kube/



      



    

#### Credentials in Jenkins for ECR and EKS cluster
Jenkins need the credentials of ECR to be able to push and pull images to the registry. 

to get the password of ECR registry

      password: aws ecr get login-password --region eu-central 1a
      username: AWS

EKS cluster needs permission to to be able to pull the image from private docker registry. create secret in EKS cluster with the credentials of ECR using kubectl

     kubectl create secret docker-registry aws_ecr_key docker-server="ecr endpoint" docker-username=AWS docker-password='add password'

#### Adjust jenkins pipeline with the stages below:
##### Stage1 : 
Version increment:  Application version in dynamically increased <br/>
##### Stage2: 
Package application <br/>
##### Stage3: 
build Application:  Docker image is built from the application with the dynamically increased version number and jenkins build number<br/>
##### Stage4:
Longin to ecr private docker registry and push the built image into the registry<br/>
##### Stage5:
Deploy application into kubernetes cluster: <br/>
Configuration files for deployment and service are created for the application and the imagePullPolicy is set to always pull image from the private registry. The ecr credentials created in the kubernetes cluster is defined in the configuration as the imagePullSecret.<br/>
##### Stage5:
commit changes back to the git repo with the updated version increment 

<img src='./cap/w3.png' height="80%" width="80%" alt="Disk Sanitization Steps">
<img src='./cap/w2.png' height="80%" width="80%" alt="Disk Sanitization Steps">
