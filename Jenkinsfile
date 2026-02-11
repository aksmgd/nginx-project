pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "dockerhubusername/nginx-app"   // replace with your DockerHub repo
    }

    stages {
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
