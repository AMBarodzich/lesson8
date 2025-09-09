def dockerRun = "docker run -d -p 8080:8080 ambarodzich/docker-app:'${BUILD_NUMBER}'"

pipeline {
    agent any

    stages {
        stage('Manual Approval') {
                steps {
                    input message: 'Proceed to Production Deployment?',
                          ok: 'Approve'
                }
        }
    }
}
