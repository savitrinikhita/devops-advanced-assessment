pipeline {
    agent any
    environment {
        dockerImage = ''
    }
    tools {nodejs "NODE JS"}
    stages {
        stage('pull the code') {
            steps {
               git branch: 'main', credentialsId: 'git-key', url: 'git@github.com:savitrinikhita/devops-advanced-assessment.git'
            }
        }
        stage('Build and Install') {

            steps {
                script{
                sh 'npm install'
                sh 'npm test'
            }
            }
        }
        stage('Sonar'){
            steps {
                
                sh '''
              sonar-scanner \
  -Dsonar.projectKey=devops-advanced \
  -Dsonar.sources=. \
  -Dsonar.host.url=http://172.173.255.207:9000 \
  -Dsonar.login=sqp_859202052292585fb3d0a59667d07bc5428e1bb6'''
            }
        }
         stage('Docker Build') {
             steps {
                 sh 'docker build -t 180040469/devops/nodejs:latest .'
             }
         }
         stage("push to registery"){
             steps{
                 script {
                    sh "docker login -u 180030469 -p Docker"
                sh "docker push 180030469/devops/nodejs:latest"
                   
                       }
                     }
                 }
             
        
         stage('Ansible install docker'){
             steps{
                  ansiblePlaybook credentialsId: 'git-token', disableHostKeyChecking: true, inventory: 'ansible/dev.inv', playbook: 'ansible/install-docker.yml'
             }
         }
         stage('Start the docker container'){
             steps{
                  ansiblePlaybook credentialsId: 'git-token', disableHostKeyChecking: true, inventory: 'ansible/dev.inv', playbook: 'ansible/install-image.yml'
             }
         }
    }
}
