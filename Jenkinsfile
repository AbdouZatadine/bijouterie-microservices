pipeline {
    // ici on dit a jenkins de tout faire sur l'agent tools, c'est mon pc windows qui a docker
    agent { label 'tools' }

    stages {
        // etape 1 : on recupere le code du projet depuis mon depot github
        stage('Checkout') {
            steps {
                checkout scm // jenkins sait deja quel depot et quelle branche grace a la config du job
            }
        }

        // etape 2 : on construit les 3 images docker a partir des dossiers frontend, backend et database
        // le tag c'est le numero du build jenkins (BUILD_NUMBER), comme ca chaque version a son numero
        stage('Build') {
            steps {
                bat "docker build -t bijouterie-frontend:${BUILD_NUMBER} frontend"
                bat "docker build -t bijouterie-backend:${BUILD_NUMBER} backend"
                bat "docker build -t bijouterie-mysql:${BUILD_NUMBER} database" // l'image mysql vient du dossier database
            }
        }

        // etape 3 : on met le nom de mon compte docker hub sur chaque image
        // double tag : le numero du build (utilise dans les manifests k8s) et latest (la version la plus recente)
        stage('Tag') {
            steps {
                bat "docker tag bijouterie-frontend:${BUILD_NUMBER} zatadineabdou/bijouterie-frontend:${BUILD_NUMBER}"
                bat "docker tag bijouterie-frontend:${BUILD_NUMBER} zatadineabdou/bijouterie-frontend:latest"
                bat "docker tag bijouterie-backend:${BUILD_NUMBER} zatadineabdou/bijouterie-backend:${BUILD_NUMBER}"
                bat "docker tag bijouterie-backend:${BUILD_NUMBER} zatadineabdou/bijouterie-backend:latest"
                bat "docker tag bijouterie-mysql:${BUILD_NUMBER} zatadineabdou/bijouterie-mysql:${BUILD_NUMBER}"
                bat "docker tag bijouterie-mysql:${BUILD_NUMBER} zatadineabdou/bijouterie-mysql:latest"
            }
        }
        
        // etape 4 : on se connecte a docker hub avec le token du coffre-fort, puis on envoie les images
        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin'
                    bat "docker push zatadineabdou/bijouterie-frontend:${BUILD_NUMBER}"
                    bat "docker push zatadineabdou/bijouterie-frontend:latest"
                    bat "docker push zatadineabdou/bijouterie-backend:${BUILD_NUMBER}"
                    bat "docker push zatadineabdou/bijouterie-backend:latest"
                    bat "docker push zatadineabdou/bijouterie-mysql:${BUILD_NUMBER}"
                    bat "docker push zatadineabdou/bijouterie-mysql:latest"
                    bat 'docker logout'
                }
            }
        }
    }
}
