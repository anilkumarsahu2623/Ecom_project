pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "anilkumarsahu2623/ecom_project"
        DOCKER_REGISTRY_CREDENTIALS = credentials('docker')
    }
    stages {
        stage('Checkout') {
            steps {
                git(
                    url: 'https://github.com/anilkumarsahu2623/Ecom_project.git',
                    branch: 'main',
                    credentialsId: 'docker'  // Ensure this is the correct Git credential ID
                )
            }
        }
        stage('Docker Login') {
            steps {
                sh '''
                    # Remove existing Docker credentials
                    rm -rf ~/.docker/config.json || true
                    security delete-generic-password -s "Docker Credentials" || true

                    # Verify Docker is running
                    docker info

                    # Login to Docker Hub
                    echo $DOCKER_REGISTRY_CREDENTIALS_PSW | docker login -u $DOCKER_REGISTRY_CREDENTIALS_USR --password-stdin || {
                        echo "Docker login failed, retrying after cleanup..."
                        docker logout
                        rm -rf ~/.docker/config.json || true
                        echo $DOCKER_REGISTRY_CREDENTIALS_PSW | docker login -u $DOCKER_REGISTRY_CREDENTIALS_USR --password-stdin
                    }
                '''
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh """
                        docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
                        docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    echo "Pushing Docker image to registry..."
                    sh """
                        docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                        docker push ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
    }
    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed! Check the logs for details."
        }
        always {
            sh '''
                # Cleanup
                docker logout || true
                rm -rf ~/.docker/config.json || true
                security delete-generic-password -s "Docker Credentials" || true
            '''
            // Clean up Docker images
            sh """
                docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER} || true
                docker rmi ${DOCKER_IMAGE}:latest || true
            """
        }
    }
}
