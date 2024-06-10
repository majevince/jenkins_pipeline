pipeline {
    agent any
    
    environment {
        // Define environment variables
        DOCKER_IMAGE = 'your-django-app:latest'
        SONARQUBE_SCANNER_HOME = tool 'SonarQube Scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            environment {
                // SonarQube environment variables
                SONARQUBE_URL = 'http://192.168.56.104:9090'
                SONARQUBE_PROJECT_KEY = 'test'
                SONARQUBE_LOGIN = credentials('sonarque_credential')
            }
            steps {
                script {
                    // Run SonarQube scan
                    withSonarQubeEnv('SonarQube') {
                        sh """
                        ${env.SONARQUBE_SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=${SONARQUBE_PROJECT_KEY} \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=${SONARQUBE_URL} \
                        -Dsonar.login=${SONARQUBE_LOGIN}
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                sh 'docker build -t ${DOCKER_IMAGE} .'
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push Docker image to a registry
                    docker.withRegistry('https://hub.docker.com/majevince', 'docker-credentials-id') {
                        sh 'docker push ${DOCKER_IMAGE}'
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace
            cleanWs()
        }
    }
}