pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t pulse .'
            }
        }

        stage('Stop Old Container') {
            steps {
                bat '''
                docker stop pulse-container || exit 0
                docker rm pulse-container || exit 0
                '''
            }
        }

        stage('Run New Container') {
            steps {
                bat 'docker run -d -p 8000:8000 --name pulse-container pulse'
            }
        }
    }
}