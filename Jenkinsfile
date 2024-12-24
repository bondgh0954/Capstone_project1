#!/user/bin/env groovy


pipeline{
  agent any
  tools{
    maven 'maven'
  }

  stages{

    stage('version increment'){
      steps{
        script{
          echo 'incrementing application version....'
         
        }
      }
    }

    stage('build jar'){
      steps{
        script{
          echo 'building jar file'
          
        }
      }
    }
   
    stage('deploy application'){
      environment {
        AWS_ACCESS_KEY_ID = credentials('aws_access_key_id')
        AWS_SECRET_ACCESS_KEY = credentials('aws_secret_access_key')
      }
      steps{
        script{
          echo 'deploying the application to eks cluster from ecr......'
          sh 'kubectl create deployment nginx-deployment --image=nginx'
          
        }
      }
    }
    stage('commit changes'){
      steps{
        script{
          echo 'commiting changes back to git repository'
          
          }
        }
      }
    }
  }

}