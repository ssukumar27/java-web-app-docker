pipeline{
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage('Git Checkout'){
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ssukumar27/java-web-app-docker.git']]])
            }
        }
        stage('maven build'){
            steps {
                sh 'mvn clean install -f pom.xml'
            }
        }
        stage('docker build image'){
            steps{
                script {
                    sh 'sudo docker build -t newimage /var/lib/jenkins/workspace/own'
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
                sshagent(['3.86.208.200']) {
                    sh 'scp -r -o StrictHostKeyChecking=no deployment.yml ubuntu@3.86.208.200:/home/ubuntu'
                    sh 'ssh ubuntu@3.86.208.200 sudo kubectl apply -f /home/ubuntu/deployment.yml'
                }
            }
        }
    }
}
