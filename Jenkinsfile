pipeline {
    agent any
    environment {
        PYTHON_PATH = 'C:\\Users\\LENOVO\\AppData\\Local\\Programs\\Python\\Python313;C:\\Users\\LENOVO\\AppData\\Local\\Programs\\Python\\Python313\\Scripts'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Coverage Installation') {
            steps {
                bat '''
                set PATH=%PYTHON_PATH%;%PATH%
                pip show coverage
                '''
            }
        }

        stage('Run Unit Tests and Generate Coverage') {
            steps {
                bat '''
                set PATH=%PYTHON_PATH%;%PATH%
                echo "Running tests with coverage..."
                coverage run --source=. test.py
                coverage xml -o coverage.xml
                if exist coverage.xml (
                    echo "Coverage report generated successfully."
                ) else (
                    echo "Error: Coverage report not found!"
                    exit /b 1
                )
                '''
            }
        }

        stage('Ensure Correct Working Directory') {
            steps {
                bat '''
                set PATH=%PYTHON_PATH%;%PATH%
                echo "Current working directory: %cd%"
                dir
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonarQub-token') // Accessing the SonarQube token stored in Jenkins credentials
            }
            steps {
                bat '''
                set PATH=%PYTHON_PATH%;%PATH%
                sonar-scanner -Dsonar.projectKey=CodeCoveragePipeline ^
                              -Dsonar.projectName=CodeCoveragePipeline ^
                              -Dsonar.sources=. ^
                              -Dsonar.python.coverage.reportPaths=coverage.xml ^
                              -Dsonar.host.url=http://localhost:9000 ^
                              -Dsonar.token=%SONAR_TOKEN%
                '''
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            echo 'This runs regardless of the result.'
        }
    }
}
