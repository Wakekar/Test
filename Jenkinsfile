pipeline {
    agent any
    environment {
        DOCKER_USER = 'aniketwakekar'
        DOCKER_PASS = credentials('dockerhub-creds') // Jenkins credential
        IMAGE_NAME  = 'aniketwakekar/test-html'
        IMAGE_TAG   = '1.0'
    }
    stages {
        stage('Checkout Code') {
            steps {
                echo "🔹 Cloning repository..."
                git url: 'https://github.com/Wakekar/Test.git', branch: 'main'
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
                sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "🔹 Pushing Docker image to Docker Hub..."
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "🔹 Deploying to Kubernetes..."
                sh 'kubectl apply -f k8s-deployment.yaml'
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
            echo "Docker image: https://hub.docker.com/r/${DOCKER_USER}/test-html"
        }
        failure {
            echo "❌ Pipeline failed. Check logs for details."
        }
    }
}
