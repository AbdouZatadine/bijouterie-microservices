pipeline {
    agent { label 'tools' }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                bat "docker build -t bijouterie-frontend:${BUILD_NUMBER} frontend"
                bat "docker build -t bijouterie-backend:${BUILD_NUMBER} backend"
                bat "docker build -t bijouterie-mysql:${BUILD_NUMBER} database"
            }
        }
    }
}
