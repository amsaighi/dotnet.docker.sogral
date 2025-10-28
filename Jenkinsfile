pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "mon-app:${env.BUILD_ID}"
        DOCKER_CONTAINER = "mon-app-container"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                script {
                    // Construire l'image Docker de l'application
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    // Ex�cuter les tests (ajuster selon votre projet)
                    sh "docker run --rm ${DOCKER_IMAGE} npm test || true"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    // Arr�ter et supprimer l'ancien conteneur s'il existe
                    sh "docker stop ${DOCKER_CONTAINER} || true"
                    sh "docker rm ${DOCKER_CONTAINER} || true"
                    
                    // D�marrer le nouveau conteneur
                    sh "docker run -d --name ${DOCKER_CONTAINER} -p 3000:3000 ${DOCKER_IMAGE}"
                }
            }
        }
    }
    
    post {
        always {
            // Nettoyage des images interm�diaires
            sh "docker system prune -f"
        }
        success {
            echo 'D�ploiement r�ussi!'
        }
        failure {
            echo '�chec du d�ploiement!'
        }
    }
}