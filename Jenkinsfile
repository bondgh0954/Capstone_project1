#!/user/bin/env groovy

@Library('shared-library')_
pipeline{
  agent any
  tools{
    maven 'maven-3.1'
  }

  stages{

    stage('version increment'){
      steps{
        script{
          echo 'incrementing application version....'
          sh "mvn build-helper:parse-version versions:set \
            -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} versions:commit"
          def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
          def version = matcher[0][1]
          env.IMAGE_NAME = "$version-$BUILD_NUMBER"
        }
      }
    }

    stage('build jar'){
      steps{
        script{
          sh 'mvn clean package'
        }
      }
    }
    stage('building image'){
      steps{
        script{
          buildImage "nanaot/java-app:$IMAGE_NAME"
          dockerLogin()
          pushImage "nanaot/java-app:$IMAGE_NAME"
        }
      }
    }
    stage('deploy application'){
      steps{
        script{
          echo 'deploying the application to ec2 instance......'
          def dockerCMD = "docker run -p 8080:8080 -d nanaot/java-app:$IMAGE_NAME"
          sshagent(['my-server-key']) {
              sh "ssh -o StrictHostKeyChecking=no ec2-user@18.199.159.129 $dockerCMD"
          }
        }
      }
    }
    stage('commit changes'){
      steps{
        script{
          echo 'commiting changes back to git repository'
          withCredentials([usernamePassword(credentialsId:'github-credentials',usernameVariable:'USER',passwordVariable:'PASS')]){
            sh "git config --global user.email 'jenkins@example.com'"
            sh "git config --global user.name 'jenkins'"

            sh 'git remote set-url origin https://${PASS}:${USER}@github.com:bondgh0954/practice-jenkins.git'
            sh 'git add .'
            sh 'git commit -m "commiting changes"'
            sh 'git push origin HEAD:versionIncrement'
          }
        }
      }
    }
  }

}