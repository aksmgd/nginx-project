pipeline {
    agent { label 'minikube-agent' }  

    environment {
        DOCKER_IMAGE = "aksmgd/nginx_dockerhub"  
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

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t $DOCKER_IMAGE:${BUILD_NUMBER} .
                """
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKER_IMAGE:${BUILD_NUMBER}
                        docker logout
                    """
                }
            }
        }

        stage('Deploy with Helm') {
            steps {
                sh """
                    helm upgrade --install nginx-app ./nginx-chart \
                        --set image.repository=$DOCKER_IMAGE \
                        --set image.tag=${BUILD_NUMBER} \
                        --wait --timeout 60s
                """
            }
        }
    }
}
