pipeline {
    agent any
    
    environment {
          // ⚙️ CONFIGURATION - À ADAPTER À VOTRE PROJET
        DOCKER_REGISTRY = ''  // Laissez vide pour Docker local
        IMAGE_NAME = 'mon-api-dotnet'  // Remplacez par le nom de votre app
        DOCKER_HOST = 'unix:///var/run/docker.sock'
        CONTAINER_NAME = 'mon-api-container'  // Nom du conteneur
        APP_PORT = '5000'  // Port d'exposition
        // 🎯 FIN DE CONFIGURATION
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Restore NuGet Packages') {
            steps {
                sh 'dotnet restore'
            }
        }
        
        stage('Build .NET Application') {
            steps {
                sh 'dotnet build --configuration Release --no-restore'
            }
        }
        
        stage('Run Tests') {
            steps {
                sh 'dotnet test --configuration Release --no-build --verbosity normal'
            }
        }
        
        stage('Publish .NET Application') {
            steps {
                sh 'dotnet publish --configuration Release --no-build --output ./publish'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    // Arrêter et supprimer le conteneur existant s'il existe
                    sh 'docker stop ${IMAGE_NAME} || true'
                    sh 'docker rm ${IMAGE_NAME} || true'
                    
                    // Lancer le nouveau conteneur
                    docker.image("${IMAGE_NAME}:${env.BUILD_ID}").run(
                        "--name ${IMAGE_NAME} -p 8080:80 -d"
                    )
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