pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '108322181673'
        REPO_NAME = 'cicd-pipeline'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout from GitHub') {
            steps {
                git branch: 'main', credentialsId: 'github-creds', url: 'https://github.com/Janaaki-N/WebApp_CI_CD.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $REPO_NAME:$IMAGE_TAG .'
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withAWS(credentials: 'aws-creds', region: "$AWS_REGION") {
                    script {
                        sh '''
                        aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                        '''
                    }
                }
            }
        }

        stage('Tag & Push to ECR') {
            steps {
                script {
                    sh '''
                    docker tag $REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPO_NAME:$IMAGE_TAG
                    docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPO_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy (Manual Step Placeholder)') {
            steps {
                echo 'Container image pushed to ECR. Next, deploy this image to ECS or EC2.'
            }
        }
    }

    post {
        success {
            echo '✅ Build and ECR push successful!'
        }
        failure {
            echo '❌ Build or ECR push failed. Check Jenkins logs.'
        }
    }
}

