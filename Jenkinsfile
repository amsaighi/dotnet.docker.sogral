pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                githubPRClosePublisher errorHandler: statusOnPublisherError('UNSTABLE'), statusVerifier: allowRunOnStatus('SUCCESS')
            }
        }
    }
}
