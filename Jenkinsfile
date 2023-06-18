pipeline {
    agent ec2slave
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
    stage("Build frontend app"){
      steps{
             
              dir("${env.WORKSPACE}/frontend"){
              withDockerRegistry([ credentialsId: "docker-hub-credentials", url: "" ]) {
              sh "pwd"
              sh "docker build -t mohit1412/frontend:latest ."
           
              sh "docker push mohit1412/frontend:latest"
                }
              }
           
      }
    }
    
    stage("Build backend app"){
      steps{
        dir("${env.WORKSPACE}/backend"){
             withDockerRegistry([ credentialsId: "docker-hub-credentials", url: "" ]) {
           sh "pwd"
           sh "docker build -t mohit1412/backend:latest ."
           sh "docker push mohit1412/backend:latest"
           } 
        }
      }
    }
    stage("Update application using compose"){
      steps{
        
           
           sh "docker-compose -f docker-compose.yml down"
           sh "sed -i 's+build: ./frontend+image: mohit1412/frontend:latest+g' docker-compose.yml"
           sh "cat docker-compose.yml"
           sh "sed -i 's+build: ./backend+image: mohit1412/backend:latest+g' docker-compose.yml"
           sh "cat docker-compose.yml"
           sh "docker-compose -f docker-compose.yml up -d"
           
           
       
         
      }
    }
    }

    
}
