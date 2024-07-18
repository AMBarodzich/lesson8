def dockerRun = "docker run -d -p 8081:8080 ambarodzich/docker-app:'$BUILD_NUMBER'"

pipeline {
    agent any
    
    tools {
        maven 'M3'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AMBarodzich/lesson8']])
            }
        }
        stage('build') {
            steps {
                sh 'mvn clean install && mvn clean package'
            }
        }
        stage('docker_build') {
            steps {
                sh 'docker build -t ambarodzich/docker-app:"$BUILD_NUMBER" .'
            }
        }
        stage('docker_push') {
            steps {
                withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                    sh 'docker login -u ambarodzich -p ${DockerHubPwd}'
                }
                sh 'docker push ambarodzich/docker-app:"$BUILD_NUMBER"'
            }
        }
        stage('deploy') {
            steps {
                sshagent(['jenkins']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.84.132 ${dockerRun}"
                }
            }
        }
    }
}
