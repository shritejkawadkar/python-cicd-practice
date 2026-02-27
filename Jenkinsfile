pipeline {
    agent any

    environment {
        IMAGE_NAME   = "shritej14/python-cicd-app"
        IMAGE_TAG    = "latest"
        DOCKER_CREDS = credentials('dockerhub-creds')
    }

    stages {

        /* =====================
           CI: Run Tests
           ===================== */
        stage('Test') {
            agent {
                docker {
                    image 'python:3.11-slim'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  python -m venv venv
                  . venv/bin/activate
                  pip install --upgrade pip
                  pip install -r requirements.txt
                  pytest
                '''
            }
        }

        /* =====================
           Build Docker Image
           ===================== */
        stage('Docker Build') {
            steps {
                sh '''
                  docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        /* =====================
           Push Image to Docker Hub
           ===================== */
        stage('Docker Push') {
            steps {
                sh '''
                  echo "$DOCKER_CREDS_PSW" | docker login -u "$DOCKER_CREDS_USR" --password-stdin
                  docker push $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        /* =====================
           Deploy Application
           ===================== */
        stage('Deploy') {
            steps {
                sh '''
                  docker rm -f python-app || true
                  docker pull $IMAGE_NAME:$IMAGE_TAG
                  docker run -d \
                    --name python-app \
                    -p 8081:5000 \
                    $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}
