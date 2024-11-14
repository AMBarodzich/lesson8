def dockerRun = "docker run -p 8081:8080 -d ambarodzich/docker-app:'$BUILD_NUMBER'"

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
        stage('Docker_build') {
            steps {
                sh 'docker build -t ambarodzich/docker-app:"$BUILD_NUMBER" .'
            }
        }
        stage('Docker_push') {
            steps {
                withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                    sh "docker login -u ambarodzich -p ${DockerHubPwd}"
                }
                sh 'docker push ambarodzich/docker-app:"$BUILD_NUMBER"'
            }
        }
        stage('Deploy') {
            steps {
                sshagent(['jenkins']) {
                   sh "ssh -o StrictHostKeyChecking=no ubuntu@52.203.230.123 ${dockerRun}"
                }
            }
        }
    }
}
