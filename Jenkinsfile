pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-credentials'
        SONARQUBE_SERVER = 'SonarQube'
        SONARQUBE_TOKEN = credentials('sonarqube-token')
        //SSH_CREDENTIALS_ID = 'azure-ssh-credentials'
        //AZURE_VM_IP = '172.176.219.92'
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    echo 'Cloning repository...'
                    git branch: 'main', url: 'https://github.com/ahmeddkhannn/helloworld.git', credentialsId: env.GIT_CREDENTIALS_ID
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building...'
                    bat 'python hello.py' // Use 'sh' for Unix-based agents
                }
            }
        }

        stage('Dockerize') {
            steps {
                script {
                    echo 'Dockerizing...'
                    bat 'docker build -t hello-world .' // Use 'sh' for Unix-based agents
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    echo 'Running SonarQube analysis...'
                    withSonarQubeEnv(env.SONARQUBE_SERVER) {
                        bat 'sonar-scanner -Dsonar.projectKey=my-project-key -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=${SONARQUBE_TOKEN}' // Use 'sh' for Unix-based agents
                    }
                }
            }
        }

        /* stage('Deploy to Azure VM') {
            steps {
                script {
                    echo 'Deploying to Azure VM...'
                    sshagent(credentials: [env.SSH_CREDENTIALS_ID]) {
                        sh """
                        # Copy the Python script to the VM
                        scp -o StrictHostKeyChecking=no hello.py ubuntu@${env.AZURE_VM_IP}:/home/ubuntu/

                        # SSH into the VM and run the Python script
                        ssh -o StrictHostKeyChecking=no ubuntu@${env.AZURE_VM_IP} 'python /home/ubuntu/hello.py'
                        """
                    }
                }
            }
        }
    } */

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
