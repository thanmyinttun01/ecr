pipeline {
    agent any

    environment {
        // Define your AWS and Docker parameters
        AWS_REGION = 'ap-southeast-1' // e.g., us-east-1
        ECR_REPOSITORY = 'tmt-repo' // e.g., my-app
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

        stage('Login to ECR') {
            steps {
                script {
                    // Login to ECR
                    def loginCommand = sh(script: "aws ecr get-login-password --region ${AWS_REGION}", returnStdout: true).trim()
                    sh "echo ${loginCommand} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
                }
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                script {
                    // Tag the image
                    sh "docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}"
                    
                    // Push the image to ECR
                    sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo 'Docker image pushed to ECR successfully!'
        }
        failure {
            echo 'Failed to push Docker image to ECR.'
        }
    }
}
