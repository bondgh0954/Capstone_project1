# Capstone_project1

# Complete CI/CD pipeline with EKS and private Docker registry

# Technologies used
Kubernetes
Jenkins
AWS EKS
Docker Hub
Java Maven
Linux
Docker
Git

# Project Description:
Create private AWS ECR Docker repository
Adjust Jenkinsfile to build and push Docker image to AWS ECR
Integrate deploying to K8s cluster in the CI/CD pipeline from AWS ECR private registry
Configuration of CI/CD pipeline:
 1. Increment Version
 2. Build artifact for java Maven application
 3. Build and push Docker image to AWS ECR
 4. Deploy new application version to EKS cluster
 5. Commit the version update


# Step 1 Install and initialise Jenkins 
Create a server on DigitalOcean platform and install jenkins container on the server.

ssh into the server and install docker on the server

    ssh root@138.68.108.14
    apt install docker.io
Run Jenkins as a container on the server

    docker run -p 8080:8080 -p 50000:50000 -d -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts

Enter the jenkins container to retrive the admin credentials of jenkins

    docker exec -it 32114f01d3e8  bash
Enter the publicIp  of server and port open for jenkins container in the browser

    cat /var/jenkins_home/secrets/initialAdminPassword

install docker inside jenkins container to be able to execute docker commands in jenkins

    

# Create credentials for ECR repository in Jenkins
to get the password of ECR registry

      password: aws ecr get login-password --region eu-central 1a
      username: AWS
# Create credentials in K8s cluster using secretes

     kubectl create secret docker-registry aws_ecr_key docker-server="ecr endpoint" docker-username=AWS docker-password='add password'
