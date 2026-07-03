pipeline {
    agent any

    tools {
        nodejs "node"
    }

    environment {
        IMAGE_NAME = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        PORT       = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build') {
            steps { sh 'npm install' }
        }
        stage('Test') {
            steps { sh 'npm test' }
        }
        stage('Build Docker Image') {
            steps { sh "docker build -t ${IMAGE_NAME}:v1.0 ." }
        }
        stage('Deploy') {
            steps {
                sh """
                    docker ps -q  --filter name=${IMAGE_NAME} | xargs -r docker stop
                    docker ps -aq --filter name=${IMAGE_NAME} | xargs -r docker rm
                    docker run -d --name ${IMAGE_NAME} --expose ${PORT} -p ${PORT}:3000 ${IMAGE_NAME}:v1.0
                """
            }
        }
    }
}