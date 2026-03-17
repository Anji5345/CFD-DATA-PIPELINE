pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Repo Files') {
            steps {
                sh '''
                pwd
                ls -R
                '''
            }
        }

        stage('Pipeline Ready') {
            steps {
                echo 'Jenkins is connected to the repo and pipeline is working.'
            }
        }
    }

    post {
        success {
            echo 'Pipeline SUCCESS'
        }
        failure {
            echo 'Pipeline FAILED'
        }
    }
}