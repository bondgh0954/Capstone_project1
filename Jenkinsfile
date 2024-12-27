pipeline {

  agent any

  tools {
    maven 'maven'
  }
  environment{
    IMAGE = "${SERVER}/capstone"
    SERVER = '975050175231.dkr.ecr.eu-central-1.amazonaws.com'
  }

    stages{

      stage('version increment'){
        steps{
          script{
            echo 'incrementing application version dynamically.....'
            sh 'mvn build-helper:parse-version versions:set \
             -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
               versions:commit'
            def getversion = readFile('pom.xml') =~ '<version>(.+)</version>'
            def newversion = getversion[0][1]
            env.IMAGE_VERSION = "$newversion-$BUILD_NUMBER"
          }
        }
      }
      stage('build jar'){
        steps{
          script{
            echo 'packaging application into a jar file.....'
            sh 'mvn clean package'
          }
        }
      }

      stage('build image'){
        steps{
          script{
            echo 'building application into docker image....'
            sh "docker build -t ${IMAGE}:${IMAGE_VERSION} ."
            

          }
        }
      }

      stage('pushing image'){
        steps{
          script{
            echo 'pushing image into private docker registry...'
            withCredentials([usernamePassword(credentialsId: 'ecr-id', usernameVariable: 'USER', passwordVariable: 'PASS')]){
              sh "echo ${PASS} | docker login -u ${USER} --password-stdin ${SERVER}"
              sh "docker push ${IMAGE}:${IMAGE_VERSION}"
            }
          }
        }
      }
      stage('deploy App'){

        environment {
          AWS_ACCESS_KEY_ID = credentials('aws_access_key_id')
          AWS_SECRET_ACCESS_KEY = credentials('aws_secret_access_key')
          APP_NAME = "capstone"
        }
        steps{
          script{
            echo 'deploying application into AWS server.....'
            sh 'envsubst < deployment.yaml | kubectl apply -f -'
            sh 'envsubst < service.yaml | kubectl apply -f -'
            

          }
        }
      }

      stage('commit version changes'){
        steps{
          script{
            echo 'pushing updated app version into git repo.....'
            withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]){

              sh "git remote set-url origin https://${USER}:${PASS}@github.com/bondgh0954/CI-CD_jenkins.git "
              sh 'git config --global user.name "jenkins"'
              sh 'git config --global user.email "jenkins@example.com"'
            

            

              sh 'git add .'
              sh 'git commit -m "commit changes"'
              sh 'git push origin HEAD:main'
            }
          }
        }
      }
    }
}
