pipeline {
    agent any

    parameters {
        choice(name: 'EXECUTE_STAGE', 
               choices: ['Clone Repository', 'Build Docker Image'], 
               description: 'Choose the stage to execute.')
    }

    environment {
        DOCKER_REGISTRY = 'localhost:5000' // Podman 또는 Docker 로컬 레지스트리 주소
        GIT_REPO_URL = 'https://github.com/TheProMovers/TaskBox_test' // GitHub 리포지토리 URL
        GIT_BRANCH = 'main' // 사용할 Git 브랜치
        GIT_CREDENTIAL = 'github-organization-token' // Jenkins에 저장된 GitHub 자격 증명 ID
        IMAGE_NAME = 'taskbox-app' // Docker 이미지 이름
    }
  
    stages {
        stage('Clone Repository') {
            when {
                expression { params.EXECUTE_STAGE == 'Clone Repository' }
            }
            steps {
                git branch: "${GIT_BRANCH}", url: "${GIT_REPO_URL}", credentialsId: "${GIT_CREDENTIAL}"
            }
        }

        stage('Build Docker Image') {
            when {
                expression { params.EXECUTE_STAGE == 'Build Docker Image' }
            }
            steps {
                script {
                    sh """
                    docker build -t ${IMAGE_NAME}:latest .
                    """
                }
            }
        }
    }
}
