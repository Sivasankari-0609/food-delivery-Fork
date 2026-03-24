pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sivasankariss/food-backend"
        DOCKER_TAG = "latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/Sivasankari-0609/food-delivery-Fork.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                cd $WORKSPACE
                docker compose down -v || true
                docker compose up -d
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Build + Deploy SUCCESS'
        }
        failure {
            echo '❌ Pipeline FAILED'
        }
    }
}