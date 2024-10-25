pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "anilkumarsahu2623/ecom_project"
        DOCKER_REGISTRY_CREDENTIALS = credentials('docker')
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: "https://github.com/anilkumarsahu2623/Ecom_project.git"
            }
        }
        
        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry(DOCKER_IMAGE, DOCKER_REGISTRY_CREDENTIALS) {
                        echo 'Successfully logged in to Docker registry'
                    }
                }
            }
        }
        
        stage('Docker Build') {
            steps {
                script {
                    try {
                        echo "Building Docker image..."
                        sh """
                            docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
                            docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
                        """
                    } catch (Exception e) {
                        echo "Error during Docker build: ${e.getMessage()}"
                        throw e
                    }
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    try {
                        echo "Pushing Docker image to registry..."
                        sh """
                            docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                            docker push ${DOCKER_IMAGE}:latest
                        """
                    } catch (Exception e) {
                        echo "Error during Docker push: ${e.getMessage()}"
                        throw e
                    }
                }
            }
        }
    }
    
    // post {
    //     success {
    //         echo "Pipeline executed successfully!"
    //     }
    //     failure {
    //         echo "Pipeline failed! Check the logs for details."
    //     }
    //     always {
    //         sh '''
    //             # Cleanup
    //             docker logout || true
    //             rm -rf ~/.docker/config.json || true
    //             security delete-generic-password -s "Docker Credentials" || true
    //         '''
            
    //         // Clean up Docker images
    //         sh """
    //             docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER} || true
    //             docker rmi ${DOCKER_IMAGE}:latest || true
    //         """
    //     }
    // }
}
