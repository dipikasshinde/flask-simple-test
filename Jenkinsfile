pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = "288761767482"
        AWS_REGION = "us-east-1"  // Change to your region
        ECR_REPO_NAME = "flask-simple-test"
        IMAGE_TAG = "${BUILD_NUMBER}"
        AWS_CREDENTIALS = 'aws-credentials' // Jenkins credentials ID for AWS
    }

    stages {
        stage('SCM Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/dipikasshinde/flask-simple-test.git'
            }
        }

        stage('AWS ECR Login') {
            steps {
                script {
                    withCredentials([aws(credentialsId: "${AWS_CREDENTIALS}", accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh """
                            aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t ${ECR_REPO_NAME}:${IMAGE_TAG} .
                        docker tag ${ECR_REPO_NAME}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh """
                        docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
    }

    // post {
    //     always {
    //         script {
    //             sh """
    //                 docker rmi ${ECR_REPO_NAME}:${IMAGE_TAG}
    //                 docker rmi ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}:${IMAGE_TAG}
    //             """
    //             cleanWs()
    //         }
    //     }
    // }
}
