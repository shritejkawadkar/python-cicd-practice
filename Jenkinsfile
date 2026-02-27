pipeline {
    agent any

    environment {
        IMAGE_NAME   = "shritej14/python-cicd-app"
        IMAGE_TAG    = "latest"
        DOCKER_CREDS = credentials('dockerhub-creds')
    }

    stages {

        stage('Test') {
            steps {
                sh '''
                  docker run --rm \
                    -v "$WORKSPACE:/app" \
                    python:3.11-slim \
                    sh -c "pip install -r /app/requirements.txt && pytest /app"
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                  echo "$DOCKER_CREDS_PSW" | docker login -u "$DOCKER_CREDS_USR" --password-stdin
                  docker push $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

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
}
