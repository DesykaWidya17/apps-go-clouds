pipeline {
    agent any

    environment {
        IMAGE_NAME = "apps-go-cloud"
        CONTAINER_NAME = "apps-go-cloud-container"
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials')
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📦 Cloning repository..."
                checkout scm
            }
        }

        stage('Build Docker image') {
            steps {
                echo "🐳 Building Docker image..."
                bat "docker build -t %IMAGE_NAME% ."
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo "🔐 Logging in to Docker Hub..."
                bat "echo %DOCKER_CREDENTIALS_PSW% | docker login -u %DOCKER_CREDENTIALS_USR% --password-stdin"
            }
        }

        stage('Push Docker image') {
            steps {
                echo "📤 Pushing image to Docker Hub..."
                bat "docker tag %IMAGE_NAME% %DOCKER_CREDENTIALS_USR%/%IMAGE_NAME%:latest"
                bat "docker push %DOCKER_CREDENTIALS_USR%/%IMAGE_NAME%:latest"
            }
        }

        stage('Run Container') {
            steps {
                echo "🚀 Running container..."
                bat "docker stop %CONTAINER_NAME% || exit 0"
                bat "docker rm %CONTAINER_NAME% || exit 0"
                bat "docker run -d --name %CONTAINER_NAME% -p 8080:8080 %DOCKER_CREDENTIALS_USR%/%IMAGE_NAME%:latest"
            }
        }
    }

    post {
        always {
            script {
                node {
                    echo "🧹 Cleaning up..."
                    bat "docker system prune -af"
                }
            }
        }

        failure {
            echo "❌ Pipeline failed. Check logs above."
        }

        success {
            echo "✅ Deployment successful!"
        }
    }
}
