pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '208805232757'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"

        BACKEND_IMAGE = "${ECR_REGISTRY}/rag-bot-backend"
        FRONTEND_IMAGE = "${ECR_REGISTRY}/rag-bot-frontend"

        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                sh '''
                    docker build \
                        -t ${BACKEND_IMAGE}:${IMAGE_TAG} \
                        ./backend
                '''
            }
        }

        stage('Build Frontend') {
            steps {
                sh '''
                    docker build \
                        -t ${FRONTEND_IMAGE}:${IMAGE_TAG} \
                        ./frontend
                '''
            }
        }

        stage('ECR Login') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'jenkins-aws-ecr']
                ]) {
                    sh '''
                        aws ecr get-login-password \
                            --region ${AWS_REGION} | \
                        docker login \
                            --username AWS \
                            --password-stdin ${ECR_REGISTRY}
                    '''
                }
            }
        }

        stage('Push Backend') {
            steps {
                sh '''
                    docker push ${BACKEND_IMAGE}:${IMAGE_TAG}
                '''
            }
        }

        stage('Push Frontend') {
            steps {
                sh '''
                    docker push ${FRONTEND_IMAGE}:${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        success {
            echo 'RAG Bot CI pipeline completed successfully!'
        }

        failure {
            echo 'RAG Bot CI pipeline failed.'
        }
    }
}
