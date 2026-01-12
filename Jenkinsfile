pipeline {
    agent any

    environment {
        // Docker image info
        IMAGE_NAME = "aniketwakekar/test-html"
        IMAGE_TAG  = "1.0"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "🔹 Cloning repository..."
                git branch: 'main',
                    url: 'https://github.com/Wakekar/Test.git'
            }
        }

        stage('Show HTML Content') {
            steps {
                echo "🔹 Displaying HTML file content:"
                sh 'cat index.html'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🔹 Building Docker image: ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Docker Login') {
            steps {
                echo "🔹 Logging into Docker Hub..."
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds', // Your Jenkins credential ID
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "🔹 Pushing Docker image to Docker Hub..."
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
            echo "Docker image is available at: https://hub.docker.com/r/aniketwakekar/test-html"
        }
        failure {
            echo "❌ Pipeline failed. Check the logs above for errors."
        }
    }
}
