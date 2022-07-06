@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent any
    environment {
        registry = "736971720766.dkr.ecr.us-east-1.amazonaws.com/final-project-repo/node"
    }

    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/Swapnil-H-Khot/node-js-app.git']]])
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
                sh 'docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 736971720766.dkr.ecr.us-east-1.amazonaws.com/final-project-repo/node '
                sh 'docker push 736971720766.dkr.ecr.us-east-1.amazonaws.com/final-project-repo/node'
            }
        }
    }

    stage('Docker Run') {
     steps{
         script {
             sshagent(credentials : ['aws_ec2']){

                sh 'ssh -o StrictHostKeyChecking=no -i ~/.ssh/sk_final_project_key.pem ubuntu@10.0.1.36'

             }
                //sh 'ssh -i ~/.ssh/sk_final_project_key.pem ubuntu@10.0.1.36'
                sh 'docker run -d -p 8081:8080 --rm --name node 736971720766.dkr.ecr.us-east-1.amazonaws.com/final-project-repo/node'
            }
      }
    }
    }
}
