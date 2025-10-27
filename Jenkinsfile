pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-credentials')
        IMAGE_NAME = "wizzidevs/go-fiber-app"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📥 Checking out source code..."
                deleteDir()
                git branch: 'master', url: 'https://github.com/DesykaWidya17/apps-go-clouds.git'
            }
        }

        stage('Build & Test (Go)') {
            steps {
                echo "🐹 Building and testing Go app..."
                bat '''
                    go version
                    go mod tidy
                    go build -o main.exe .
                    go test ./...
                '''
            }
        }

        stage('Docker Build & Test') {
            steps {
                echo "🐳 Building Docker image..."
                bat 'docker build -t %IMAGE_NAME% .'
                echo "🔍 Testing container output..."
                bat '''
                    docker run --name test-container -d -p 8000:8000 %IMAGE_NAME%
                    timeout /t 5
                    curl http://localhost:8000
                    docker stop test-container
                    docker rm test-container
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "📤 Pushing Docker image to Docker Hub..."
                bat '''
                    docker login -u %DOCKER_HUB_CREDENTIALS_USR% -p %DOCKER_HUB_CREDENTIALS_PSW%
                    docker tag %IMAGE_NAME% %IMAGE_NAME%:latest
                    docker push %IMAGE_NAME%:latest
                '''
            }
        }
    }

    post {
        always {
            echo "🧹 Cleaning up..."
            bat 'docker system prune -af'
        }
        failure {
            echo "❌ Pipeline failed. Check logs above."
        }
        success {
            echo "✅ Pipeline completed successfully!"
        }
    }
}
