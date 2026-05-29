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

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Push Image') {
            steps {
                bat 'docker tag pulse saishankarreddy/pulse:latest'
                bat 'docker push saishankarreddy/pulse:latest'
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

        stage('Deploy From Docker Hub') {
            steps {
                bat '''
                docker pull saishankarreddy/pulse:latest
                docker run -d -p 8000:8000 --name pulse-container saishankarreddy/pulse:latest
                '''
            }
        }

        stage('Check Kubernetes Connection') {
            steps {
                withEnv(['KUBECONFIG=C:\\Users\\DELL\\.kube\\config']) {
                    bat 'kubectl config current-context'
                    bat 'kubectl get nodes'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withEnv(['KUBECONFIG=C:\\Users\\DELL\\.kube\\config']) {
                    bat 'kubectl apply -f k8s'
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withEnv(['KUBECONFIG=C:\\Users\\DELL\\.kube\\config']) {
                    bat 'kubectl get deployments'
                    bat 'kubectl get pods'
                    bat 'kubectl get svc'
                }
            }
        }
    }
}