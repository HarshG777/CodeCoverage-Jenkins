pipeline {
    agent any

    environment {
        SONAR_SCANNER_HOME = 'C:\\Users\\lenovo\\Downloads\\sonar-scanner-cli-6.2.1.4610-windows-x64\\sonar-scanner-6.2.1.4610-windows-x64\\bin'
        PYTHON_HOME = "C:\\Users\\lenovo\\AppData\\Local\\Programs\\Python\\Python313;C:\\Users\\lenovo\\AppData\\Local\\Programs\\Python\\Python313\\Scripts"
        SONAR_TOKEN = credentials('sonarQub-token')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Virtualenv') {
            steps {
                bat '''
                set PATH=%PYTHON_HOME%;%PATH%
                python -m venv venv
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                set PATH=%PYTHON_HOME%;%PATH%
                venv\\Scripts\\activate
                python -m pip install --upgrade pip
                python -m pip install pytest pytest-cov requests
                '''
            }
        }

        stage('Run Tests with Coverage') {
            steps {
                bat '''
                set PATH=%PYTHON_HOME%;%PATH%
                venv\\Scripts\\activate && pytest --cov=. --cov-report xml'''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat '''
                    sonar-scanner.bat ^
                    -D"sonar.projectKey=CoverageReport-Jenkins" ^
                    -D"sonar.sources=." ^
                    -D"sonar.host.url=http://localhost:9000" ^
                    -D"sonar.login=%SONAR_TOKEN%"  // Use environment variable for token
                    -D"sonar.python.coverage.reportPaths=coverage.xml"
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Pipeline failed due to quality gate failure: ${qualityGate.status}"
                    }
                }
            }
        }
    }
}
