pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'raghu593'
        BACKEND_IMAGE  = "${DOCKERHUB_USER}/dd-backend"
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/dd-frontend"
        DEPLOY_DIR     = "/home/ubuntu/dd-app"   
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('backend') {
                    sh """
                      echo "Building backend image..."
                      docker build -t ${BACKEND_IMAGE}:latest .
                    """
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('frontend') {
                    sh """
                      echo "Building frontend image..."
                      docker build -t ${FRONTEND_IMAGE}:latest .
                    """
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push ${BACKEND_IMAGE}:latest
                      docker push ${FRONTEND_IMAGE}:latest
                      docker logout
                    """
                }
            }
        }

        stage('Deploy to VM (same machine)') {
            steps {
                sh """
                  echo "Deploying using docker compose..."
                  cd ${DEPLOY_DIR}
                  docker compose pull
                  docker compose up -d
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed. Check logs.'
        }
    }
}
