pipeline {
    agent any

    environment {
        // Define your AWS and Docker parameters
        AWS_REGION = 'ap-southeast-1' // e.g., us-east-1
        ECR_REPOSITORY = 'tmt/repo' // e.g., my-app
        IMAGE_TAG = 'latest' // or use a variable for versioning
        AWS_ACCOUNT_ID = '896836667748' // e.g., 123456789012
    }

    stages {
        stage('Clone repository') {
            steps {
                // Checkout the SCM repository
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh "docker build -t ${ECR_REPOSITORY}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login to ECR Public') {
            steps {
                script {
                    // Login to ECR Public
                    def loginCommand = sh(script: "aws ecr-public get-login-password --region ${AWS_REGION}", returnStdout: true).trim()
                    sh "echo ${loginCommand} | docker login --username AWS --password-stdin public.ecr.aws"
                }
            }
        }

        stage('Push Docker Image to ECR Public') {
            steps {
                script {
                    // Tag the image for ECR Public
                    sh "docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} public.ecr.aws/${AWS_ACCOUNT_ID}/${ECR_REPOSITORY}:${IMAGE_TAG}"
                    
                    // Push the image to ECR Public
                    sh "docker push public.ecr.aws/${AWS_ACCOUNT_ID}/${ECR_REPOSITORY}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo 'Docker image pushed to ECR Public successfully!'
        }
        failure {
            echo 'Failed to push Docker image to ECR Public.'
        }
    }
}
