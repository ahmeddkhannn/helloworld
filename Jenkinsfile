pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'github-credentials' // Jenkins credentials ID for GitHub access
        SONARQUBE_SERVER = 'SonarQube' // SonarQube server name as configured in Jenkins
        SONARQUBE_TOKEN = credentials('sonarqube-token') // Jenkins credentials ID for SonarQube token
        SSH_CREDENTIALS_ID = 'azure-ssh-credentials' // Jenkins credentials ID for SSH access to Azure VM
        AZURE_VM_IP = '172.176.219.92' // IP address of your Azure VM
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    // Clone the GitHub repository
                    echo 'Cloning repository...'
                    git branch: 'main', url: 'https://github.com/ahmeddkhannn/helloworld.git', credentialsId: env.GIT_CREDENTIALS_ID
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build step to execute the Python script
                    echo 'Building...'
                    bat 'python hello.py' // Use 'sh' if running on a Unix-based agent
                }
            }
        }

        stage('Dockerize') {
            steps {
                script {
                    // Build Docker image
                    echo 'Dockerizing...'
                    bat 'docker build -t hello-world .' // Use 'sh' if running on a Unix-based agent
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube analysis
                    echo 'Running SonarQube analysis...'
                    withSonarQubeEnv(env.SONARQUBE_SERVER) {
                        bat 'sonar-scanner -Dsonar.projectKey=my-project-key -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=${SONARQUBE_TOKEN}'
                        // Use 'sh' if running on a Unix-based agent
                    }
                }
            }
        }

        stage('Deploy to Azure VM') {
            steps {
                script {
                    // Deploy the application to the Azure VM
                    echo 'Deploying to Azure VM...'
                    sshagent(credentials: [env.SSH_CREDENTIALS_ID]) {
                        sh """
                        # Copy the Python script to the VM
                        scp -o StrictHostKeyChecking=no -i ${env.SSH_PRIVATE_KEY} hello.py ubuntu@${env.AZURE_VM_IP}:/home/ubuntu/

                        # SSH into the VM and run the Python script
                        ssh -o StrictHostKeyChecking=no -i ${env.SSH_PRIVATE_KEY} ubuntu@${env.AZURE_VM_IP} 'python /home/ubuntu/hello.py'
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            // Actions to be performed after the pipeline completes
            echo 'Pipeline completed.'
        }
    }
}
