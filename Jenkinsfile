pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = ''
        IMAGE_NAME = 'mon-api-dotnet'
        CONTAINER_NAME = 'mon-api-container'
        APP_PORT = '5000'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build .NET') {
            steps {
                sh 'dotnet restore'
                sh 'dotnet build --configuration Release --no-restore'
                sh 'dotnet test --configuration Release --no-build --verbosity normal'
                sh 'dotnet publish --configuration Release --no-build --output ./publish'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${env.BUILD_ID} ."
                }
            }
        }
        
        stage('Deploy Container') {
            steps {
                script {
                    sh """
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                        docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:5000 ${IMAGE_NAME}:${env.BUILD_ID}
                    """
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed'
            cleanWs()
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}