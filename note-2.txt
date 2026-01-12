pipeline {
    agent any

    environment {
        PROJECT_DIR = "/mnt/project"
        TOMCAT_ROOT = "/mnt/apache-tomcat-11.0.15/webapps/ROOT"
        TOMCAT_BIN  = "/mnt/apache-tomcat-11.0.15/bin"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Wakekar/Test.git'
            }
        }

        stage('Prepare Workspace') {
            steps {
                sh """
                    # Clean previous workspace
                    rm -rf ${PROJECT_DIR}
                    mkdir -p ${PROJECT_DIR}
                """
            }
        }

        stage('Clone Repository') {
            steps {
                sh """
                    git clone -b main https://github.com/Wakekar/Test.git ${PROJECT_DIR}
                """
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh """
                    # Remove old ROOT folder
                    rm -rf ${TOMCAT_ROOT}
                    mkdir -p ${TOMCAT_ROOT}/WEB-INF

                    # Copy updated index.html
                    cp ${PROJECT_DIR}/index.html ${TOMCAT_ROOT}/

                    # Create minimal web.xml for Tomcat 11+
                    cat > ${TOMCAT_ROOT}/WEB-INF/web.xml <<EOF
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
         https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
    <display-name>ROOT</display-name>
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
    </welcome-file-list>
</web-app>
EOF

                    # Fix permissions
                    chmod -R 755 ${TOMCAT_ROOT}
                    chmod 644 ${TOMCAT_ROOT}/index.html
                    chmod 644 ${TOMCAT_ROOT}/WEB-INF/web.xml

                    # Restart Tomcat
                    ${TOMCAT_BIN}/shutdown.sh || true
                    ${TOMCAT_BIN}/startup.sh
                """
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully! Open http://<your-ip>:8080/"
        }
        failure {
            echo "Deployment failed! Check console output."
        }
    }
}
