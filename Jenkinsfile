pipeline {
    agent any

    environment {
        IMAGE_NAME = "bhola3013/jenkins-python-docker"
        CONTAINER_NAME = "flask-test-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                  docker rm -f $CONTAINER_NAME || true
                  docker run -d -p 5001:5000 --name $CONTAINER_NAME $IMAGE_NAME:latest
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
                      docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Python app built, tested, and pushed to Docker Hub"
        }
        always {
            sh 'docker rm -f $CONTAINER_NAME || true'
        }
    }
}
