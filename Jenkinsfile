pipeline {
    agent any

   tools {
       jdk 'jdk-17'
    }

    environment {
        DOCKER_HUB_USER = "amendhouibi22" 
        DOCKER_IMAGE_NAME = "ob-service"
        DOCKER_TAG = "latest"
        DOCKER_IMAGE = "${DOCKER_HUB_USER}/${DOCKER_IMAGE_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Test Docker Container (Basic)') {
            steps {
                sh "docker run --name test-container -d -p 8083:8081 ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
            post {
                always {
                    sh "docker stop ob-container || true"
                    sh "docker rm ob-container || true"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub', url: 'https://index.docker.io/v1/') {
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }
    }

    post {
        always {
            sh "docker system prune -f || true"
        }
    }
}
