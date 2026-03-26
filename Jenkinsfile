pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        PYTHONUNBUFFERED = "1"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Verify Project Structure') {
            steps {
                sh '''
                test -f producer/csv_replayer.py
                test -f spark/stream_features.py
                test -f spark/batch_rule_engine.py
                test -f spark/rule_utils.py
                test -f sql/init.sql
                test -f sql/generate_alerts.sql
                test -f requirements-dev.txt
                test -f tests/test_producer.py
                test -f tests/test_rule_utils.py
                '''
            }
        }

        stage('Create Virtual Environment') {
            steps {
                sh '''
                python -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements-dev.txt
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh '''
                . venv/bin/activate
                pytest tests --junitxml=pytest-results.xml
                '''
            }
        }

        stage('Validate Python Syntax') {
            steps {
                sh '''
                . venv/bin/activate
                python -m py_compile producer/csv_replayer.py
                python -m py_compile spark/stream_features.py
                python -m py_compile spark/batch_rule_engine.py
                python -m py_compile spark/rule_utils.py
                '''
            }
        }

        stage('Validate Docker Compose') {
            steps {
                sh '''
                docker-compose config
                '''
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'pytest-results.xml'
        }
        success {
            echo 'Jenkins pipeline completed successfully.'
        }
        failure {
            echo 'Jenkins pipeline failed. Check logs.'
        }
    }
}