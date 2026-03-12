pipeline {
    agent any

    environment {
        // This automatically creates $DOCKERHUB_CREDENTIALS_USR and $DOCKERHUB_CREDENTIALS_PSW
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials') 
        IMAGE_NAME = 'node-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                // The logs showed your tests run during 'docker build' (Step 5/7). 
                // That is actually a great practice!
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo 'Pushing image to DockerHub...'
                // CHANGED: Use triple DOUBLE quotes (""") so ${IMAGE_NAME} expands correctly
                sh """
                    echo \$DOCKERHUB_CREDENTIALS_PSW | docker login -u \$DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} \$DOCKERHUB_CREDENTIALS_USR/${IMAGE_NAME}:${IMAGE_TAG}
                    docker push \$DOCKERHUB_CREDENTIALS_USR/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                    docker compose down || true
                    docker compose up -d
                '''
            }
        }
    }

    post {
        always {
            echo 'Logging out from DockerHub...'
            sh 'docker logout'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
