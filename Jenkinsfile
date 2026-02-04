pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nginx-app:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                
                git(
                    url: 'https://github.com/aksmgd/nginx-project.git',
                    branch: 'main',
                    credentialsId: 'github-creds'   
                )
            }
        }

        stage('Build Docker Image in Minikube') {
            steps {
                
                sh "eval \$(minikube docker-env) && docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Deploy with Helm') {
            steps {
                 sh ''' 
                    helm upgrade --install nginx-app ./nginx-chart \
                         --set image.repository=nginx-app \
                         --set image.tag=latest \
                         --set image.pullPolicy=IfNotPresent \
                         --wait --timeout 60s
                 '''               
            }
        }
        
        stage('Wait for Pod Ready') { 
            steps { 
                sh "kubectl rollout status deployment/nginx-app --timeout=60s" 
            } 
        }

        stage('Expose Service URL') {
            steps {
                
                sh "minikube service nginx-service --url"
            }
        }
    }
}

