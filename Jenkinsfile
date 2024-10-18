pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "anilkumarsahu2623/ecom_project-ci"  // Replace with your Docker Hub repository
        DOCKER_CREDENTIALS_ID = "dockerhub_credentials" // DockerHub credentials ID in Jenkins
        GIT_REPO = "https://github.com/anilkumarsahu2623/Ecom_project.git" // Your GitHub repo URL
        // GIT_CREDENTIALS_ID = "github_credentials"  // Jenkins credentials ID for GitHub
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the GitHub repository using Git credentials
                git branch: 'main',
                    url: "${GIT_REPO}"
            }
        }

        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             // Build Docker image using the Dockerfile in the repository
        //             def image = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
        //         }
        //     }
        // }

        stage('Docker Image Build & Push') {
            steps {
                script {
                    // Push Docker image to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        // sh "docker push ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                        sh "docker build -t ecom-project-jenkins-ci ."
                        sh "docker tag ecom-project-jenkins-ci anilkumarsahu2623/ecom_project:latest "
                        sh "docker push anilkumarsahu2623/ecom_project:latest "
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker images after build
            sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
        }
    }
}
