pipeline {
    agent any

    environment {
        DOCKER_USER = 'amsaighi'
        DOCKER_REGISTRY = 'docker.io'
    }

    stages {
        stage('Hello') {
            steps {
                echo 'message test'
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    echo "Docker build"
                    docker build -t amsaighi/sogral-docker-test-app . 
                '''
            }
        }
        
        stage('Login') {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_PAT', variable: 'DOCKER_PAT')]) {
                    sh '''
                        echo "Docker login"
                        echo "$DOCKER_PAT" | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }
        
        stage('Push') {
            steps {
                sh '''
                    echo "Docker push"
                    docker push amsaighi/sogral-docker-test-app
                '''
            }
        }
    }
}
