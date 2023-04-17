pipeline {
    agent any
    environment {
    registry = "https://hub.docker.com/repository/docker/youngwillsjoshua/voting-app"
    }

    stages {
       stage('Checkout Git') {
            steps {
               checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/youngwillsjoshua/voting-app.git']])
            }
        }
       
        
       stage('Build Result App') {
           steps {
               nodejs('NodeJs') {
                   sh 'npm install'
                   sh 'npm run'

               }
           }
       }
       
       stage('Build Voting App') {
           steps {
               sh 'pip install -r vote/requirements.txt'
               sh 'python -m py_compile vote/app.py'
           }
       }
       
       stage('Pull result Image') {
           steps {
               sh 'docker pull kodekloud/examplevotingapp_result:v1'
           }
       }
       
       stage('Pull vote image') {
           steps {
               sh 'docker pull kodekloud/examplevotingapp_vote:v1'
           }
       }
       
       stage('Pull worker image') {
           steps {
               sh 'docker pull kodekloud/examplevotingapp_worker:v1'
           }
       }
       
       stage('Kube Config') {
           steps {
              withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k38', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
               sh 'kubectl apply -f voting-app-deploy.yaml'
               sh 'kubectl apply -f voting-app-service.yaml'
               sh 'kubectl apply -f redis-deploy.yaml'
               sh 'kubectl apply -f redis-service.yaml'
               sh 'kubectl apply -f postgres-deploy.yaml'
               sh 'kubectl apply -f postgres-service.yaml'
               sh 'kubectl apply -f worker-app-deploy.yaml'
               sh 'kubectl apply -f result-app-deploy.yaml'
               sh 'kubectl apply -f result-app-service.yaml'
              }
           }
       }
    }
}    
