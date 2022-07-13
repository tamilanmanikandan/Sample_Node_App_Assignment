@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent any
    environment {
        registry = "859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest "
    }
    
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/tamilanmanikandan/Sample_Node_App_Assignment.git']]])
            }
        }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }

    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
        steps{
            script {
                sh 'docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest  '
                sh 'docker push 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest '
            }
        }
    }

    stage('Docker Deploy') {
     steps{
         script {
             sshagent(credentials : ['upgrad']){
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.21 "docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest  && docker pull 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest && (docker ps -f name=node -q | xargs --no-run-if-empty docker container stop) && (docker container ls -a -fname=node -q | xargs -r docker container rm) && docker run -d -p 8081:8081 --rm --name node 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest "'

             }
                
                
            }
      }
    }
    }
}
