pipeline {
    agent any
    stages {
        stage('Check Environment') {
            steps {
                echo "🔍 Checking Docker and Go environment..."
                bat '''
                    echo ---- Docker Version ----
                    docker --version || echo Docker NOT FOUND
                    echo ---- Go Version ----
                    go version || echo Go NOT FOUND
                '''
            }
        }
    }
}
