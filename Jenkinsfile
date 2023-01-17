pipeline {
    agent any
    environment {
        dockerImage = ''
    }
    stages {
        stage('pull the code') {
            steps {
               git branch: 'main', credentialsId: '92f27e9d-788a-4836-a645-3642abe33d1c', url: 'git@github.com:PrajawalSharma/nodejs.git'
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
              -Dsonar.projectKey=sonar \
              -Dsonar.sources=. \
              -Dsonar.host.url=http://20.169.251.252:9000 \
              -Dsonar.login=sqp_64c5f1c391bee41a7eb646f895cd86236568d1c8'''
            }
        }
        stage('Dcoker Build') {
            steps {
                sh 'docker build -t prajawalsharma/node:latest .'
            }
        }
        stage("push to registery"){
            steps{
                // This step should not normally be used in your script. Consult the inline help for details.
                sh "docker login -u prajawalsharma -p Docker"
                sh "docker push prajawalsharma/node:latest"
            }
        }
        stage('Ansible install docker'){
            steps{
                 ansiblePlaybook credentialsId: 'ansible-0key', disableHostKeyChecking: true, inventory: 'ansible/dev.inv', playbook: 'ansible/install-docker.yml'
            }
        }
        stage('Start the docker container'){
            steps{
                 ansiblePlaybook credentialsId: 'ansible-0key', disableHostKeyChecking: true, inventory: 'ansible/dev.inv', playbook: 'ansible/install-image.yml'
            }
        }
    }
}