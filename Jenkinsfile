pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t bhola3013/jenkins-python-docker:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker ps -a | grep flask-test-container && docker rm -f flask-test-container || true
                docker run -d \
                  --restart unless-stopped \
                  -p 5001:5000 \
                  --name flask-test-container \
                  bhola3013/jenkins-python-docker:latest
                '''
            }
        }

        stage('Test Application') {
            steps {
                sh '''
                sleep 5
                curl http://localhost:5001
                '''
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    docker login -u $DOCKER_USER -p $DOCKER_PASS
                    docker push bhola3013/jenkins-python-docker:latest
                    '''
                }
            }
        }
    }
}
