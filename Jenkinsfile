
pipeline {
    agent any

    environment {
        EC2_PUBLIC_IP = '98.93.38.223'
        DOCKER_IMAGE  = 'priyamvaidya062/priyamvaidya062my-web-app'
        SSH_CRED_ID   = 'ec2-ssh-key'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t "$DOCKER_IMAGE" .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        set +x
                        printf '%s' "$DOCKER_PASS" |
                          docker login -u "$DOCKER_USER" \
                            --password-stdin
                        docker push "$DOCKER_IMAGE"
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(credentials: [SSH_CRED_ID]) {
                    sh '''
                        set -eu

                        ssh -o StrictHostKeyChecking=accept-new \
                          ubuntu@"$EC2_PUBLIC_IP" \
                          "docker pull '$DOCKER_IMAGE' &&
                           (docker stop my-running-app || true) &&
                           (docker rm my-running-app || true) &&
                           docker run -d --restart unless-stopped \
                             --name my-running-app \
                             -p 8080:80 '$DOCKER_IMAGE'"
                    '''
                }
            }
        }
    }

    post {
        always {
            sh '''
                docker image rm "$DOCKER_IMAGE" || true
            '''
        }
    }
}
