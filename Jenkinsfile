pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '208805232757'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"

        BACKEND_IMAGE = "${ECR_REGISTRY}/rag-bot-backend"
        FRONTEND_IMAGE = "${ECR_REGISTRY}/rag-bot-frontend"

        IMAGE_TAG = "${BUILD_NUMBER}"
        SKIP_PIPELINE = 'false'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // Jenkins may be triggered by its own deployment-manifest commit.
        // Skip that run to avoid building and committing in a loop.
        stage('Check Commit') {
            steps {
                script {
                    def lastCommitMessage = sh(
                        script: 'git log -1 --pretty=%s',
                        returnStdout: true
                    ).trim()

                    env.SKIP_PIPELINE = lastCommitMessage.startsWith(
                        'chore: update deployment images to build '
                    ) ? 'true' : 'false'

                    echo "Skip build stages: ${env.SKIP_PIPELINE}"
                }
            }
        }

        stage('Build, Push, and Update Deployment') {
            when {
                expression {
                    return env.SKIP_PIPELINE != 'true'
                }
            }

            stages {
                stage('SonarQube Analysis') {
                    steps {
                        script {
                            def scannerHome = tool 'SonarScanner'

                            withSonarQubeEnv('SonarQube') {
                                sh """
                                    ${scannerHome}/bin/sonar-scanner \
                                      -Dsonar.projectKey=rag-bot-fastapi \
                                      -Dsonar.projectName='RAG Bot FastAPI' \
                                      -Dsonar.sources=backend,frontend \
                                      '-Dsonar.exclusions=**/__pycache__/**,**/.venv/**,**/venv/**,**/*.pyc'
                                """
                            }
                        }
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

                stage('Push Images') {
                    steps {
                        sh '''
                            docker push ${BACKEND_IMAGE}:${IMAGE_TAG}
                            docker push ${FRONTEND_IMAGE}:${IMAGE_TAG}
                        '''
                    }
                }

                stage('Update Kubernetes Manifest') {
                    steps {
                        sh '''
                            sed -i -E \
                              "s|(rag-bot-backend:)[^[:space:]]+|\\1${IMAGE_TAG}|" \
                              k8s/deployment.yaml

                            sed -i -E \
                              "s|(rag-bot-frontend:)[^[:space:]]+|\\1${IMAGE_TAG}|" \
                              k8s/deployment.yaml

                            git config user.name "Jenkins"
                            git config user.email "jenkins@localhost"
                            git add k8s/deployment.yaml

                            if ! git diff --cached --quiet; then
                                git commit -m "chore: update deployment images to build ${IMAGE_TAG}"
                            fi
                        '''

                        withCredentials([
                            gitUsernamePassword(
                                credentialsId: 'github-write',
                                gitToolName: 'Default'
                            )
                        ]) {
                            sh 'git push origin HEAD:main'
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            script {
                if (env.SKIP_PIPELINE == 'true') {
                    echo 'Skipped: this commit only updates deployment image tags.'
                } else {
                    echo 'RAG Bot pipeline completed successfully. Argo CD will sync the updated manifests.'
                }
            }
        }

        failure {
            echo 'RAG Bot pipeline failed. Check the failed stage and Console Output.'
        }
    }
}
