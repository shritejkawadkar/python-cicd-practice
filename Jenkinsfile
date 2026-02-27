pipeline {
    agent any

    stages {

        stage('PROVE WHAT CONTAINER SEES') {
            steps {
                sh '''
                  echo "===== HOST SIDE ====="
                  pwd
                  ls -la
                  echo "====================="

                  docker run --rm \
                    -v "$WORKSPACE:/app" \
                    python:3.11-slim \
                    sh -c "
                      echo '===== INSIDE CONTAINER =====';
                      pwd;
                      ls -la /;
                      echo '--- /app contents ---';
                      ls -la /app || true;
                      echo '--- cat requirements.txt ---';
                      cat /app/requirements.txt || echo 'FILE NOT FOUND';
                      echo '============================'
                    "
                '''
            }
        }
    }
}
