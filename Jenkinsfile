pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-credentials'
        SONARQUBE_SERVER = 'SonarQube'
        SONARQUBE_TOKEN = credentials('sonarqube-token') // Ensures SONARQUBE_TOKEN is retrieved correctly
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
                    bat 'pip install -r requirements.txt'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building...'
                    bat 'python app.py'
                }
            }
        }

        stage('Dockerize') {
            steps {
                script {
                    echo 'Dockerizing...'
                    bat 'docker build -t python-sample-app .'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    echo 'Running SonarQube analysis...'
                    withSonarQubeEnv(env.SONARQUBE_SERVER) {
                        bat 'sonar-scanner -Dsonar.projectKey=my-project-key -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=${SONARQUBE_TOKEN}'
                    }
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
