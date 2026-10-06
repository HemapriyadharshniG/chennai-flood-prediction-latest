pipeline {
    agent any

    environment {
        PATH = "/opt/homebrew/bin:/usr/local/bin:${env.PATH}"
        IMAGE_NAME = "chennai-flood-backend"
        IMAGE_TAG = "build-${BUILD_NUMBER}"
        PYTHONUNBUFFERED = "1"
    }

    stages {
        stage('Checkout') {
            steps {
                echo '=== Stage 1: Checkout Source Code ==='
                checkout scm
                sh 'git log -1 --stat'
            }
        }

        stage('Build') {
            steps {
                echo '=== Stage 2: Application Build & Dependency Setup ==='
                sh '''
                    if command -v python3.11 >/dev/null 2>&1; then
                        PYTHON_BIN="python3.11"
                    else
                        PYTHON_BIN="python3"
                    fi
                    $PYTHON_BIN -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r backend/requirements.txt
                '''
            }
        }

        stage('Test / Validate') {
            steps {
                echo '=== Stage 3: Automated Validation & Testing ==='
                sh '''
                    . venv/bin/activate
                    export PYTHONPATH="${WORKSPACE}/backend:${PYTHONPATH}"
                    pytest backend/tests/ -v
                '''
            }
        }

        stage('Docker Build') {
            steps {
                echo '=== Stage 4: Docker Image Build ==='
                sh """
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -t ${IMAGE_NAME}:latest ./backend
                    docker images | grep ${IMAGE_NAME}
                """
            }
        }
    }

    post {
        always {
            echo '=== Pipeline Execution Completed ==='
            cleanWs(deleteDirs: true, notFailBuild: true)
        }
        success {
            echo "CI Pipeline Succeeded! Docker image ${IMAGE_NAME}:${IMAGE_TAG} built and validated successfully."
        }
        failure {
            echo "CI Pipeline Failed! Check the console log for errors."
        }
    }
}
