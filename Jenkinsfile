pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-app-image"
        IMAGE_TAG = "latest"
        REPO_URL = "https://github.com/theenathayalan97/dockerImage.git"
        BRANCH = "master"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

    }

    post {
        success {
            echo "Docker image built successfully!"
        }
        failure {
            echo "Build failed!"
        }
    }
}