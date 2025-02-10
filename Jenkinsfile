pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'localhost:5000' // Local Docker Registry
        IMAGE_NAME = 'frontend' // Docker 이미지 이름
        KUBECONFIG_PATH = '/path/to/kubeconfig' // Kubernetes 인증 파일 경로
    }

    stages {
        stage('Clone Repository') {
            steps {
                // GitHub에서 코드 클론
                git branch: 'main', url: 'https://github.com/TheProMovers/TaskBox_test.git'
            }
        }

        stage('Install Dependencies & Build Frontend') {
            steps {
                // 프론트엔드 의존성 설치 및 빌드
                script {
                    sh """
                    cd frontend
                    npm ci --silent
                    npm run build
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Docker 이미지 빌드
                script {
                    sh """
                    docker build -t ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest -f frontend/Dockerfile ./frontend
                    """
                }
            }
        }

        stage('Push to Local Registry') {
            steps {
                // 로컬 Docker Registry에 이미지 푸시
                script {
                    sh """
                    docker push ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Update Kubernetes Deployment') {
            steps {
                // deployment.yaml 업데이트
                script {
                    sh """
                    sed -i 's|image: .*|image: ${DOCKER_REGISTRY}/${IMAGE_NAME}:latest|' k8s/frontend/deployment.yaml
                    """
                }
            }
        }

        stage('Apply Kubernetes Deployment') {
            steps {
                // Kubernetes에 배포
                script {
                    sh """
                    export KUBECONFIG=${KUBECONFIG_PATH}
                    kubectl apply -f k8s/frontend/deployment.yaml
                    """
                }
            }
        }
    }
}
