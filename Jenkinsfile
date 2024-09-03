pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/ahmeddkhannn/hello-world.git'
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
