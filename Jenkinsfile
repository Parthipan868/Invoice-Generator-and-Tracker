pipeline {
    agent any

    environment {
        IMAGE = "parthipan86/invoice-app"
    }

    stages {

        stage('Clone Repo') {
            steps {
                git 'https://github.com/Parthipan868/Invoice-Generator-and-Tracker.git'
            }
        }

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
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
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
                sh '''
                docker stop invoice-container || true
                docker rm invoice-container || true
                docker run -d -p 3000:80 --name invoice-container ${IMAGE}:latest
                '''
            }
        }
    }
}