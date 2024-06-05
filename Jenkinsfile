pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "waounde221/face-attendance-app"
        K8S_NAMESPACE = "default"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/cwaounde/projet-fil-rouge.git'
            }
        }
        
        stage('Build Docker Images') {
            steps {
                script {
                    docker.build("${env.DOCKER_IMAGE}:backend", "./backend")
                    docker.build("${env.DOCKER_IMAGE}:frontend", "./frontend/face-attendance-web-app-front")
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        docker.image("${env.DOCKER_IMAGE}:backend").push()
                        docker.image("${env.DOCKER_IMAGE}:frontend").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh '''
                    kubectl apply -f k8s/backend-deployment.yaml
                    kubectl apply -f k8s/frontend-deployment.yaml
                    '''
                }
            }
        }
    }
}
