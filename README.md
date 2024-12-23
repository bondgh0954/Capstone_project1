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


# Step 1 Create ECR registry
Create ECR registry from AWS with the name of the application "Java-maven-app"

# Create credentials for ECR repository in Jenkins
to get the password of ECR registry

      password: aws ecr get login-password --region eu-central 1a
      username: AWS
# Create credentials in K8s cluster using secretes

     kubectl create secret docker-registry aws_ecr_key docker-server="ecr endpoint" docker-username=AWS docker-password='add password'
