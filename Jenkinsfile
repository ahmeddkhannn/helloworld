pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-credentials'
        SONARQUBE_SERVER = 'SonarQube'
        SONARQUBE_TOKEN = credentials('sonarqube-token') // Ensures SONARQUBE_TOKEN is retrieved correctly
        DOCKER_IMAGE_NAME = 'python-sample-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/uber/Python-Sample-Application.git', credentialsId: env.GIT_CREDENTIALS_ID
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    echo 'Installing dependencies...'
                    sh 'pip install -r requirements.txt'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    echo 'Running SonarQube analysis...'
                    withSonarQubeEnv(env.SONARQUBE_SERVER) {
                        sh 'sonar-scanner -Dsonar.projectKey=python-sample-app -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=${SONARQUBE_TOKEN}'
                    }
                }
            }
        }

        stage('Dockerize') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t ${DOCKER_IMAGE_NAME} .'
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    echo 'Running application...'
                    sh 'python app.py'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
