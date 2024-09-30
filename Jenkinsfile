pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-southeast-1' // Your AWS region
        ECR_REPOSITORY = 'tmt-repo' // Your ECR repository name
        IMAGE_TAG = 'latest' // Docker image tag
        AWS_ACCOUNT_ID = '896836667748' // Your AWS account ID
        // Use Jenkins credentials for AWS
      
    }

    stages {
        stage('Clone repository') {
            steps {
                checkout scm // Checkout the source code
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

        stage('Tag Docker Image') {
            steps {
                script {
                    // Tag the image for ECR
                    sh "docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}"
                }
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                script {
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
