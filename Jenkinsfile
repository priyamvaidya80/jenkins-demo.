pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code is checked out by Jenkins'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'git --version'
                sh 'ls -la'
            }
        }

        stage('Test') {
            steps {
                echo 'Running basic validation...'
                sh 'test -f README.md'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
