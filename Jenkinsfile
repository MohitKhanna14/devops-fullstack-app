pipeline {
    agent any
    options {
            buildDiscarder(logRotator(numToKeepStr: '2'))
            disableConcurrentBuilds()
            timeout(time: 1, unit: 'HOURS')
    }
    environment {
            AWS_DEFAULT_REGION = 'us-east-1'
            CONTAINER_NAME     = 'nodeapp' 
    }
    stages {
      stage('Git Checkout') {
        steps {
          checkout scm
        }
      }
        stage('Build Docker Image') {
            steps {
                 dir("${env.WORKSPACE}"){
                sh "eval \$(aws ecr get-login --no-include-email --region '$AWS_DEFAULT_REGION') && sleep 2"
                sh "docker build . -t 462273782981.dkr.ecr.us-east-1.amazonaws.com/nodeappmk:\${BUILD_NUMBER}"
                sh "docker push 462273782981.dkr.ecr.us-east-1.amazonaws.com/nodeappmk:\${BUILD_NUMBER}"
                 }
            }
        }
       stage('Deploy it in app instance') {
            steps {
                script {
                    
                    
                    SSH =  'ssh -tt -i /home/ubuntu/demo.pem ubuntu@172.31.1.53 -o StrictHostKeyChecking=no'
                     sh """
                     $SSH ' eval \$(aws ecr get-login --no-include-email --region us-east-1) && sleep 2; 
                     
                     #!/bin/bash
                     if [ \$(docker ps | grep -c nodeapp) -eq 0 ] 
                     then
                      docker pull 462273782981.dkr.ecr.us-east-1.amazonaws.com/nodeappmk:${BUILD_NUMBER}; 
                      docker run -td -p 8080:8080 --name ${CONTAINER_NAME} 462273782981.dkr.ecr.us-east-1.amazonaws.com/nodeappmk:${BUILD_NUMBER}
                     else
                      docker stop \$(docker ps -aqf "name=${CONTAINER_NAME}")
                      docker rm \$(docker ps -aqf "name=${CONTAINER_NAME}")
                      docker pull 462273782981.dkr.ecr.us-east-1.amazonaws.com/nodeappmk:${BUILD_NUMBER}; 
                      docker run -td -p 8080:8080 --name ${CONTAINER_NAME} 462273782981.dkr.ecr.us-east-1.amazonaws.com/nodeappmk:${BUILD_NUMBER}
                     fi
                     
                     
                     ' 
                     """
                     
                      }
                }
            }
    }

    post {
        always {
           
            deleteDir()
            sh "docker rmi 462273782981.dkr.ecr.us-east-1.amazonaws.com/nodeappmk:\${BUILD_NUMBER}"
            }
        }
}
