pipeline {
    agent any

    environment {
        IMAGE_NAME = "hotel-frontend"
        REGISTRY   = "Sunithriyansh"
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${REGISTRY}/${IMAGE_NAME}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry("https://index.docker.io/v1/", "dockerhub-credentials") {
                        docker.image("${REGISTRY}/${IMAGE_NAME}:${env.BUILD_NUMBER}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'k8s-credentials']) {
                    sh "kubectl apply -f k8s/"
                }
            }
        }
    }

    post {
        success { cleanWs() }
    }
}
