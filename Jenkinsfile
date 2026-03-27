pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sivasankariss/food-backend"
        DOCKER_TAG = "latest"
    }

    stages {

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    docker run --rm \
                    -e SONAR_HOST_URL=http://host.docker.internal:9000 \
                    -e SONAR_LOGIN=$SONAR_AUTH_TOKEN \
                    -v "$WORKSPACE:/usr/src" \
                    sonarsource/sonar-scanner-cli \
                    -Dsonar.projectKey=food-delivery \
                    -Dsonar.sources=.
                    '''
                }
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
                docker rm -f food-mongo || true
                docker-compose down || true
                docker-compose up -d
                '''
            }
        }
    }
}