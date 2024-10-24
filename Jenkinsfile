pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "anilkumarsahu2623/ecom_project"
        DOCKER_CREDENTIALS_ID = "docker"  // DockerHub credentials ID in Jenkins
        tools {
  git 'Git'  // Name of the Git installation as configured in Global Tool Configuration
}
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
                # Optional: Clean Docker images
                docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER} || true
                docker rmi ${DOCKER_IMAGE}:latest || true
            '''
        }
    }
}
