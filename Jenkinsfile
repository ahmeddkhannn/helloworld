pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-credentials' // The ID you assigned when storing the credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/ahmeddkhannn/helloworld.git', credentialsId: env.GIT_CREDENTIALS_ID
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
