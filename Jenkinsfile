pipeline {

    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ECR_REPO = "aws-ecs-jenkins-cicd-project"
        ACCOUNT_ID = "YOUR_ACCOUNT_ID"
    }

    stages {

        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${ECR_REPO} .
                '''
            }
        }

        stage('Login To ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region ${AWS_REGION} \
                | docker login \
                --username AWS \
                --password-stdin \
                ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker tag ${ECR_REPO}:latest \
                ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest

                docker push \
                ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest
                '''
            }
        }

        stage('Deploy ECS') {
            steps {
                sh '''
                aws ecs update-service \
                --cluster devops-cluster \
                --service devops-service \
                --force-new-deployment
                '''
            }
        }
    }
}
