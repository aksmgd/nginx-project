pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nginx-app:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                // Use your actual GitHub repo URL and credentialsId if private
                git(
                    url: 'https://github.com/aksmgd/nginx-project.git',
                    branch: 'main',
                    credentialsId: 'github-creds'   // replace with your Jenkins credentials ID
                )
            }
        }

        stage('Build Docker Image in Minikube') {
            steps {
                // Build inside Minikube’s Docker environment
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
                // Print the service URL
                sh "minikube service nginx-service --url"
            }
        }
    }
}

