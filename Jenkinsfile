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
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    sh "docker run --rm ${DOCKER_IMAGE} npm test || true"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    sh "docker stop ${DOCKER_CONTAINER} || true"
                    sh "docker rm ${DOCKER_CONTAINER} || true"
                    
                    sh "docker run -d --name ${DOCKER_CONTAINER} -p 3000:3000 ${DOCKER_IMAGE}"
                }
            }
        }
    }
    
    post {
        always {
            sh "docker system prune -f"
        }
        success {
            echo 'Déploiement réussi!'
        }
        failure {
            echo 'Échec du déploiement!'
        }
    }
}
