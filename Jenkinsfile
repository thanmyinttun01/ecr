pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPOSITORY = 'tmt/repo'
        IMAGE_TAG = 'latest'
        AWS_ACCOUNT_ID = '896836667748'
        AWS_ACCESS_KEY_ID = credentials('AKIA5BT5BRFSOIBTX2BZ') // Replace with your Jenkins credential ID
        AWS_SECRET_ACCESS_KEY = credentials('13pQb0gvgNAf3qcPN3qlWUnRwes9KYoMzyBnwy2H') // Replace with your Jenkins credential ID
    }

    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${ECR_REPOSITORY}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login to ECR Public') {
            steps {
                script {
                    def loginCommand = sh(script: "aws ecr-public get-login-password --region ${AWS_REGION}", returnStdout: true).trim()
                    sh "echo ${loginCommand} | docker login --username AWS --password-stdin public.ecr.aws"
                }
            }
        }

        stage('Push Docker Image to ECR Public') {
            steps {
                script {
                    sh "docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} public.ecr.aws/${AWS_ACCOUNT_ID}/${ECR_REPOSITORY}:${IMAGE_TAG}"
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
