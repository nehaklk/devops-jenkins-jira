# devops-jenkins-jira
# Jenkinsfile

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    git 'https://github.com/your/repository.git'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'pip install -r requirements.txt'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'python -m unittest discover tests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t your-image-name .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                        sh 'docker push your-image-name'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Your deployment steps go here
                    // Example: kubectl apply -f deployment.yaml
                }
            }
        }
    }

    post {
        success {
            slackSend(channel: '#your-slack-channel', color: 'good', message: 'Pipeline passed!')
        }
        failure {
            slackSend(channel: '#your-slack-channel', color: 'danger', message: 'Pipeline failed!')
        }
    }
}
