pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-credentials' // The ID you assigned when storing the credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/ahmeddkhannn/hello-world.git', credentialsId: env.GIT_CREDENTIALS_ID
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building...'
                    sh 'python hello.py'
                }
            }
        }

        stage('Dockerize') {
            steps {
                script {
                    echo 'Dockerizing...'
                    sh 'docker build -t hello-world .'
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
