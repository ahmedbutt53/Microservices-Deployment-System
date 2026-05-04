pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials-id')
        BACKEND_IMAGE = "ahmaddbutt/microservices-backend"
        FRONTEND_IMAGE = "ahmaddbutt/microservices-frontend"
        TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                script {
                    sh "docker build -t ${BACKEND_IMAGE}:${TAG} ./backend-app"
                }
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    sh "docker build -t ${FRONTEND_IMAGE}:${TAG} ./frontend-app"
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "docker push ${BACKEND_IMAGE}:${TAG}"
                sh "docker push ${FRONTEND_IMAGE}:${TAG}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl apply -f kubernetes/"
            }
        }
    }

    post {
        always {
            sh "docker logout"
        }
        success {
            echo "Successfully built, pushed and deployed!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
