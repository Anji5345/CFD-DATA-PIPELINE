pipeline {
    agent any

    environment {
        COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Validate Docker Compose') {
            steps {
                sh '''
                docker compose -f ${COMPOSE_FILE} config
                '''
            }
        }

        stage('Start Infra') {
            steps {
                sh '''
                docker compose -f ${COMPOSE_FILE} up -d
                '''
            }
        }

        stage('Wait for Services') {
            steps {
                sh 'sleep 30'
            }
        }

        stage('Initialize Database') {
            steps {
                sh '''
                docker exec de2-postgres psql -U banking -d bankingdb -f /sql/init.sql
                '''
            }
        }

        stage('Smoke Check') {
            steps {
                sh '''
                docker exec de2-postgres psql -U banking -d bankingdb -c "SELECT 1;"
                '''
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