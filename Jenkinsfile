pipeline {
    agent any

    environment {
        IMAGE = "parthipan86/invoice-app"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE}:latest")
                }
            }
        }

        stage('DockerHub Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    bat 'echo %PASS% | docker login -u %USER% --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.image("${IMAGE}:latest").push()
                }
            }
        }

        stage('Deploy Container') {
            steps {
                bat '''
                docker stop invoice-container || exit 0
                docker rm invoice-container || exit 0
                docker run -d -p 3000:80 --name invoice-container %IMAGE%:latest
                '''
            }
        }
    }
}