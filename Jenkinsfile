pipeline {
    agent any
    
    environment {
        IMAGE_NAME = 'mon-api-dotnet'
        CONTAINER_NAME = 'mon-api-container'
        APP_PORT = '5000'
    }
    
    stages {
        stage('Build in Docker Container') {
            agent {
                docker {
                    image 'mcr.microsoft.com/dotnet/sdk:8.0'
                    args '--user root:root'
                    reuseNode true
                }
            }
            steps {
                checkout scm
                sh 'dotnet restore'
                sh 'dotnet build --configuration Release --no-restore'
                sh 'dotnet test --configuration Release --no-build --verbosity normal'
                sh 'dotnet publish --configuration Release --no-build --output ./publish'
            }
        }
        
        stage('Build and Deploy Docker Image') {
            agent any
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${env.BUILD_ID} ."
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    sh "docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:5000 ${IMAGE_NAME}:${env.BUILD_ID}"
                }
            }
        }
    }
}