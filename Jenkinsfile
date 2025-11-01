pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '108322181673'
        ECR_REPOSITORY = 'webapp-cicd'
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Janaaki-N/WebApp_CI_CD.git'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    // Authenticate Docker with ECR
                    sh """
                    aws ecr get-login-password --region ${AWS_REGION} \
                    | docker login --username AWS --password-stdin ${ECR_URI}
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                    docker build -t ${ECR_REPOSITORY}:${IMAGE_TAG} .
                    docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${ECR_URI}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh "docker push ${ECR_URI}:${IMAGE_TAG}"
                }
            }
        }

        stage('Clean up local images') {
            steps {
                sh "docker rmi ${ECR_URI}:${IMAGE_TAG} || true"
                sh "docker rmi ${ECR_REPOSITORY}:${IMAGE_TAG} || true"
            }
        }
    }

    post {
        success {
            echo "✅ Build and Push completed successfully!"
        }
        failure {
            echo "❌ Build or Push failed. Check the logs."
        }
    }
}
