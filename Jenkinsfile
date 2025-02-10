pipeline {
    agent any
    environment {
        REGISTRY_URL = "localhost:5000" // Docker Registry URL
        FRONTEND_IMAGE = "${REGISTRY_URL}/taskbox_frontend:latest"
    }
    stages {
        stage('Frontend Build') {
            steps {
                echo 'Building Frontend...'
                dir('frontend') { // frontend 디렉토리로 이동
                    sh '''
                    npm install
                    npm run build
                    docker build -t $FRONTEND_IMAGE .
                    docker push $FRONTEND_IMAGE
                    '''
                }
            }
        }
        stage('Backend Build') {
            steps {
                script {
                    echo 'Building Backend Services...'
                    // Groovy 스크립트 내에서 배열 선언
                    def backendServices = ['board-service', 'file-service', 'todo-service']
                    backendServices.each { service ->
                        dir("backend/${service}") { // backend 내부 각 서비스로 이동
                            sh '''
                            docker build -t ${REGISTRY_URL}/${service}:latest .
                            docker push ${REGISTRY_URL}/${service}:latest
                            '''
                        }
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'Build and Push Completed Successfully!'
        }
        failure {
            echo 'Build or Push Failed.'
        }
    }
}
