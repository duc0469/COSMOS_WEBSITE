pipeline {
    agent any

    environment {
        DOCKER_IMAGE_BACKEND = "cosmos-backend"
        DOCKER_IMAGE_FRONTEND = "cosmos-frontend"
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/KFdc15/COSMOS_WEBSITE.git', branch: 'main'
            }
        }

        stage('Backend - Install & Test') {
            steps {
                dir('backend') {
                    sh 'python -m venv venv'
                    sh './venv/Scripts/activate && pip install -r requirements.txt'
                    sh './venv/Scripts/activate && python manage.py test'
                }
            }
        }

        stage('Frontend - Install & Build') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_HOST_URL = 'http://localhost:9000'
                SONAR_SCANNER_OPTS = "-Dsonar.projectKey=cosmos_project"
            }
            steps {
                dir('backend') {
                    withSonarQubeEnv('My SonarQube') {
                        sh './venv/Scripts/activate && sonar-scanner'
                    }
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t cosmos-backend ./backend'
                sh 'docker build -t cosmos-frontend ./frontend'
            }
        }

        // Optional stage: Push to DockerHub, Deploy, etc.
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
