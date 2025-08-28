pipeline {
    agent any

    environment {
        DOCKERHUB = credentials('dockerhub-credentials')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building Docker image..."
                    docker build . -t node-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                    echo "Tagging image for DockerHub..."
                    docker tag node-app:${BUILD_NUMBER} ${DOCKERHUB_USR}/node-app:${BUILD_NUMBER}
                '''
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh '''
                    echo "Logging in to DockerHub..."
                    echo "${DOCKERHUB_PSW}" | docker login -u "${DOCKERHUB_USR}" --password-stdin
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh '''
                    echo "Pushing image to DockerHub..."
                    docker push ${DOCKERHUB_USR}/node-app:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Docker image pushed successfully: ${DOCKERHUB_USR}/node-app:${BUILD_NUMBER}"
        }
        failure {
            echo "❌ Build failed!"
        }
    }
}