def dockerRun = "docker run -p 8080:8080 -d ambarodzich/docker-app:'${BUILD_NUMBER}'"

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AMBarodzich/lesson8']])
            }
        }
        stage('Build') {
            steps {
                sh "mvn clean install && mvn clean package"
            }
        }
        stage('Build_Docker') {
            steps {
                sh 'docker build -t ambarodzich/docker-app:"${BUILD_NUMBER}" .'
            }
        }
        stage('Push_Docker') {
            steps {
                withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                    sh "docker login -u ambarodzich -p ${DockerHubPwd} "
                } 
                sh 'docker push ambarodzich/docker-app:"${BUILD_NUMBER}"'
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['ubuntu']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@54.167.8.20 ${dockerRun}"
                }
            }
        }
    }
}
