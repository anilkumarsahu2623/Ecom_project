pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "anilkumarsahu2623/ecom_project"
        DOCKER_CREDENTIALS_ID = "dockerhub_credentials"  // DockerHub credentials ID in Jenkins
        GIT_CREDENTIALS_ID = "git_credentials"          // Git credentials ID in Jenkins
    }
    stages {
        stage('Checkout') {
            steps {
                git(
                    url: 'https://github.com/anilkumarsahu2623/Ecom_project.git',
                    branch: 'main',
                    credentialsId: "${GIT_CREDENTIALS_ID}"  // GitHub credentials ID
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

        stage('Docker Login') {
            steps {
                script {
                    echo "Logging in to Docker Hub..."
                    sh """
                        echo $DOCKER_REGISTRY_CREDENTIALS_PSW | docker login -u $DOCKER_REGISTRY_CREDENTIALS_USR --password-stdin
                    """
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    echo "Pushing Docker image to Docker Hub..."
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
            echo "Pipeline failed. Please check the logs."
        }
        always {
            sh '''
                # Cleanup: Logout of Docker and clean up the local image cache
                docker logout || true
                docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER} || true
                docker rmi ${DOCKER_IMAGE}:latest || true
            '''
        }
    }
}
