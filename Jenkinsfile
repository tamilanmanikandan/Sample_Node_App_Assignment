// @Library('github.com/releaseworks/jenkinslib') _

// pipeline {
//     agent any
//     environment {
//         registry = "859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest"
//     }
    
//     stages {
//         stage('Cloning Git') {
//             steps {
//                 checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/tamilanmanikandan/Sample_Node_App_Assignment.git']]])
//             }
//         }
//     // Building Docker images
//     stage('Building image') {
//       steps{
//         script {
//           dockerImage = docker.build registry
//         }
//       }
//     }

//     // Uploading Docker images into AWS ECR
//     stage('Pushing to ECR') {
//         steps{
//             script {
//                 sh 'docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest '
//                 sh 'docker push 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest'
//             }
//         }
//     }

//     stage('Docker Deploy') {
//      steps{
//          script {
//              sshagent(credentials : ['upgrad']){
//                 sh 'ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.21 "docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest && docker pull 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest && (docker ps -f name=node -q | xargs --no-run-if-empty docker container stop) && (docker container ls -a -fname=node -q | xargs -r docker container rm) && docker run -d -p 8081:8081 --rm --name node 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest"'

//              }
                
                
//             }
//       }
//     }
//     }
// }




pipeline{
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '2'))
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
    }
    stages{
        stage('Git Checkout') {
        steps {
                checkout scm
            }
        }

        stage('Build'){
            steps{
                sh 'docker build -t node_app .'
                sh 'docker tag node_app:latest 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest'
                sh 'aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin 859058206093.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest'
            }
        }

        stage('Deploy'){
            // def dockerRun = 'docker pull 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest'
            // sh'ssh -i ~/session.pem -o StrictHostKeyChecking=no ubuntu@ec2-44-208-23-240.compute-1.amazonaws.com ${dockerRun}'
            steps{
                script {
                    sh 'ssh -i ~/project2-key.pem -o StrictHostKeyChecking=no ubuntu@10.0.1.56'
                    // sh'''ssh -tt -i ~/session.pem -o StrictHostKeyChecking=no ubuntu@ec2-18-212-64-133.compute-1.amazonaws.com && aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 859058206093.dkr.ecr.us-east-1.amazonaws.com && docker pull 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest && docker ps'''
                    sh 'ssh ubuntu@10.0.1.56 aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin 859058206093.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'ssh ubuntu@10.0.1.56 sudo docker pull 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest'
                    sh 'ssh ubuntu@10.0.1.56 sudo docker images'
                    sh 'ssh ubuntu@10.0.1.56 sudo docker ps -q'
                    // sh 'ssh ubuntu@10.0.1.56 sudo docker kill $(ssh ubuntu@10.0.1.56 sudo docker ps -q)'
                    sh 'ssh ubuntu@10.0.1.56 sudo docker run -d -p8080:8080 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest'
                }
                
            }
        }
    }
    post {
        always {
            deleteDir()
            sh 'docker rmi 859058206093.dkr.ecr.us-east-1.amazonaws.com/courseassignment:latest'
        }

    }
}







