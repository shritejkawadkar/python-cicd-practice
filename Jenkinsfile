pipeline {
    agent any

    environment {
        IMAGE_NAME   = "shritej14/python-cicd-app"
        IMAGE_TAG    = "latest"
        DOCKER_CREDS = credentials('dockerhub-creds')
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                  python3 -m venv venv
                  . venv/bin/activate
                  pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                  . venv/bin/activate
                  pytest
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                  docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
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

    post {
        success {
            echo '✅ CI/CD pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed – check logs'
        }
    }
}
