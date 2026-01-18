pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nginx-app:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/arunmgd/nginx-project.git'
            }
        }

        stage('Build Docker Image in Minikube') {
            steps {
                sh "eval \$(minikube docker-env) && docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Deploy with Helm') {
            steps {
                sh "helm upgrade --install nginx-app ./nginx-chart --set image.repository=nginx-app --set image.tag=latest"
            }
        }

        stage('Expose Service URL') {
            steps {
                sh "minikube service nginx-service --url"
            }
        }
    }
}

