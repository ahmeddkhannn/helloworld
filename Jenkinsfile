pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-credentials' // The ID you assigned when storing the credentials
        SONARQUBE_SERVER = 'SonarQube' // The name of your SonarQube server configured in Jenkins
        SONARQUBE_TOKEN = credentials('sonarqube-token') // The credentials ID for your SonarQube token
        TUNNEL_URL = "https://new-parts-report.loca.lt" // To store the LocalTunnel URL dynamically
    }

    stages {
        stage('Start LocalTunnel') {
            steps {
                script {
                    echo 'Starting LocalTunnel...'
                    // Run the LocalTunnel command and capture the URL it returns
                    TUNNEL_URL = bat(script: 'lt --port 8080', returnStdout: true).trim()
                    echo "LocalTunnel URL: ${TUNNEL_URL}"
                }
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/ahmeddkhannn/helloworld.git', credentialsId: env.GIT_CREDENTIALS_ID
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building...'
                    bat 'python hello.py'
                }
            }
        }

        stage('Dockerize') {
            steps {
                script {
                    echo 'Dockerizing...'
                    bat 'docker build -t hello-world .'
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
            script {
                echo 'Pipeline completed.'
                echo "You can access your Jenkins at: ${TUNNEL_URL}"
            }
        }
    }
}
