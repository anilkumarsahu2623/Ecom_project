pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "anilkumarsahu2623/ecom_project"  // Docker Hub repository
        DOCKER_CREDENTIALS_ID = "dockerhub_credentials" // DockerHub credentials ID in Jenkins
        GIT_REPO = "https://github.com/anilkumarsahu2623/Ecom_Project_space.git" // GitHub repo URL
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the GitHub repository
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image using the Dockerfile in the repository
                    def image = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Run Script in WSL') {
            steps {
                // Run the Python script using WSL
                bat 'wsl nohup python3 /mnt/c/ProgramData/Jenkins/workspace/Ecom_project_Pipeline-CI/app.py > /mnt/c/ProgramData/Jenkins/workspace/Ecom_project_Pipeline-CI/output.log 2>&1 &'
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push Docker image to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        sh "docker push ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker images after the build
            script {
                if (sh(script: "docker images -q ${DOCKER_IMAGE}:${env.BUILD_NUMBER}", returnStatus: true) == 0) {
                    sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
