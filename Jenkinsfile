def dockerRun = "docker run -d -p 8080:8080 ambarodzich/docker-app:'${BUILD_NUMBER}'"

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AMBarodzich/lesson8']])
            }
        }
        stage('Build_App') {
            steps {
                sh 'mvn clean install && mvn clean package'
            }
        }
        stage('Build_Image') {
            steps {
                sh 'docker build -t ambarodzich/docker-app:"${BUILD_NUMBER}" .'
            }
        }
        stage('Push_Image') {
            steps {
                withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                    sh "docker login -u ambarodzich -p ${DockerHubPwd}"
                }
                sh 'docker push ambarodzich/docker-app:"${BUILD_NUMBER}"'
            }
        }
        stage('Deploy') {
            steps {
                sshagent(credentials: ['sshagent'], executable: '') {
                    sh """
                        docker context create remote-target --docker "host=ssh://ubuntu@54.90.254.179" || true
                        IMAGE_TAG=${BUILD_NUMBER} docker context remote-target compose up -d --remove-orphans    
                    """
                }
            }
        }
    }
}
