pipeline {
    agent any

    stages {
        stage('Prepare /mnt/project') {
            steps {
                sh 'rm -rf /mnt/project && mkdir -p /mnt/project'
            }
        }

        stage('Clone Repository') {
            steps {
                sh 'git clone -b main https://github.com/Wakekar/Test.git /mnt/project'
            }
        }

        stage('Deploy to Apache') {
            steps {
                sh 'cp /mnt/project/index.html /var/www/html/ && systemctl restart httpd || systemctl restart apache2'
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed! Check the console output.'
        }
    }
}
