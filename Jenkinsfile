pipeline {
    agent any
    environment {
        IMAGE_NAME = 'myapp'
        IMAGE_TAG = "v${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                echo "Building from branch: ${env.GIT_BRANCH}"
                echo "Commit: ${env.GIT_COMMIT}"
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
                sh 'docker images | grep myapp'
            }
        }
        stage('Test Image') {
            steps {
                sh 'docker run -d --name myapp-test -p 8083:80 ${IMAGE_NAME}:${IMAGE_TAG}'
                sh 'sleep 3'
                sh 'curl -s http://localhost:8083'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker stop myapp-prod || true'
                sh 'docker rm myapp-prod || true'
                sh 'docker run -d --name myapp-prod -p 8082:80 ${IMAGE_NAME}:${IMAGE_TAG}'
                sh 'docker ps'
            }
        }
    }
    post {
        always {
            sh 'docker stop myapp-test || true'
            sh 'docker rm myapp-test || true'
        }
    }
}
