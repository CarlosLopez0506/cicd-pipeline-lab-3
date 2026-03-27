pipeline {
    agent any

    tools {
        nodejs 'node'
    }

    environment {
        IMAGE_NAME = "node${BRANCH_NAME}:v1.0"
        CONTAINER_NAME = "node${BRANCH_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x scripts/build.sh && scripts/build.sh'
            }
        }

        stage('Test') {
            steps {
                sh 'chmod +x scripts/test.sh && scripts/test.sh'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def port = (BRANCH_NAME == 'main') ? '3000' : '3001'

                    // Stop and remove existing container if running
                    sh "docker rm -f ${CONTAINER_NAME} || true"

                    // Run the new container
                    sh "docker run -d --name ${CONTAINER_NAME} -p ${port}:3000 ${IMAGE_NAME}"

                    echo "App deployed at http://localhost:${port}"
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline succeeded for branch: ${BRANCH_NAME}"
        }
        failure {
            echo "Pipeline failed for branch: ${BRANCH_NAME}"
        }
    }
}
