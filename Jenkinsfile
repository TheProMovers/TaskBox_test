pipeline {
    agent any // Jenkins가 사용할 에이전트 지정
    environment {
        REGISTRY_URL = "localhost:5000" // Docker Registry URL
        FRONTEND_IMAGE = "${REGISTRY_URL}/taskbox_frontend:latest"
        BACKEND_SERVICES = ['board-service', 'file-service', 'todo-service']
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
                    BACKEND_SERVICES.each { service ->
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
