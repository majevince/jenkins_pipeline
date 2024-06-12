pipeline {
    agent any
    
    environment {
        // Define environment variables
        DOCKER_IMAGE = 'nginx:latest'
        SONARQUBE_SCANNER_HOME = tool 'sonarqube_test'
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
                SONARQUBE_URL = 'http://192.168.56.104:9090/'
                SONARQUBE_PROJECT_KEY = 'test'
                SONARQUBE_LOGIN = credentials('sonarque_credential')
                // SONARQUBE_LOGIN = credentials('sqp_5c271a04c75c3f2deccf70d4e5cb2f85e4f8b64e')
            }
            steps {
                script {
                    // Run SonarQube scan
                    withSonarQubeEnv('sonarqube_test') {
                        sh """
                        // ${env.SONARQUBE_SCANNER_HOME}/bin/sonar-scanner \
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
