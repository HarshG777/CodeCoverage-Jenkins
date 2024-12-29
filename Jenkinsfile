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

        stage('Install Dependencies') {
            steps {
                
                bat '''
                set PATH=%PYTHON_HOME%;%PATH%
                python -m pip install pytest pytest-cov requests
                '''
            }
        }

        stage('Run Tests with Coverage') {
            steps {
                bat 'pytest --cov=. --cov-report xml'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat '''sonar-scanner.bat sonar-scanner.bat ^
                  -D"sonar.projectKey=CoverageReport-Jenkins" -D"sonar.sources=." ^
                  -D"sonar.host.url=http://localhost:9000" -D"sonar.token=sqp_6d94c361f3baef676f9fa1e88450c378d7b7fd08"'''
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
