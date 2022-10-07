pipeline {
    agent any
    
    tools {
        maven 'Maven'
    }
    
    stages {
        stage('checkout'){
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ssukumar27/java-web-app-docker.git']]])
            }
        }    
        stage('build') {
            steps {
                sh 'mvn clean install -f pom.xml'
            }    
        }
        stage('build docker image') {
            steps {
                script {
                    sh 'sudo docker build -t newimage /var/lib/jenkins/workspace/declare'
                    sh 'sudo docker tag newimage ssukumar27/newimage:latest'
                    sh 'sudo docker tag newimage ssukumar27/newimage:${BUILD_NUMBER}'
                }
            }
        }
        stage('login & push to docker'){
            steps {
                withCredentials([string(credentialsId: 'docker_hub', variable: 'docker_hub')]) {
                    sh 'sudo docker login -u ssukumar27 -p ${docker_hub}'
                }
                sh 'sudo docker image push ssukumar27/newimage:latest'
                sh 'sudo docker image push ssukumar27/newimage:${BUILD_NUMBER}'
            }
        }
        stage('Deploy on Kubernetes') {
            steps {
                sh 'sudo kubectl apply -f /var/lib/jenkins/workspace/declare/deployment.yml'
                sh 'sudo kubectl rollout restart deployment myapp1'
            }
        }
    }
}
