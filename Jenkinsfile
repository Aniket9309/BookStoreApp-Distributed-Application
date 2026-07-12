pipeline {
    agent { label 'slave' }

    environment {
        AWS_REGION    = 'ap-south-1'
        ECR_REGISTRY  = '198452821908.dkr.ecr.ap-south-1.amazonaws.com'
        SERVICE_NAME  = 'bookstore-eureka-discovery-service'
        IMAGE_TAG     = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build (Multi-stage)') {
            steps {
                sh """
                    docker build --build-arg SERVICE=${SERVICE_NAME} \
                    -f ${SERVICE_NAME}/Dockerfile \
                    -t ${SERVICE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Push to ECR') {
            steps {
                sh """
                    aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}
                    docker tag ${SERVICE_NAME}:${IMAGE_TAG} ${ECR_REGISTRY}/${SERVICE_NAME}:${IMAGE_TAG}
                    docker tag ${SERVICE_NAME}:${IMAGE_TAG} ${ECR_REGISTRY}/${SERVICE_NAME}:latest
                    docker push ${ECR_REGISTRY}/${SERVICE_NAME}:${IMAGE_TAG}
                    docker push ${ECR_REGISTRY}/${SERVICE_NAME}:latest
                """
            }
        }
    }

    post {
        success {
            echo "✅ ${SERVICE_NAME} build & push successful! Tag: ${IMAGE_TAG}"
        }
        failure {
            echo "❌ Build failed for ${SERVICE_NAME}"
        }
    }
}
