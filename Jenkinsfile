pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_URI = '108322181673.dkr.ecr.us-east-1.amazonaws.com/webapp-cicd'
        IMAGE_NAME = 'webapp-cicd'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Janaaki-N/WebApp_CI_CD.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                docker build -t %IMAGE_NAME% .
                """
            }
        }

        // 🔍 Added Stage: Check AWS CLI
        stage('Check AWS CLI') {
            steps {
                bat """
                echo Checking AWS CLI installation...
                where aws
                aws --version
                """
            }
        }

        stage('Login to AWS ECR') {
            steps {
                bat """
                echo Logging in to AWS ECR...
                "C:\\Program Files\\Amazon\\AWSCLIV2\\aws.exe" ecr get-login-password --region %AWS_REGION% ^
                | docker login --username AWS --password-stdin %ECR_URI%
                """
            }
        }

        stage('Tag Docker Image') {
            steps {
                bat """
                docker tag %IMAGE_NAME%:latest %ECR_URI%:latest
                """
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                bat """
                docker push %ECR_URI%:latest
                """
            }
        }
    }

    post {
        success {
            echo '✅ Docker image successfully pushed to AWS ECR!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs for details.'
        }
    }
}
