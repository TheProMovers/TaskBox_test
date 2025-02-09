# 로컬 환경
## 1. 모든 서비스 빌드 및 실행
docker-compose up --build

## 2. 백그라운드에서 실행하려면
docker-compose up -d --build

## 3. 로그 확인
docker-compose logs -f

## 4. 특정 서비스의 로그만 확인
docker-compose logs -f gateway-service

## 5. 서비스 중지
docker-compose down

## 6. 볼륨까지 모두 삭제하고 중지
docker-compose down -v



# K8S 배포 순서

## 1. 스토리지 설정
kubectl apply -f k8s/nfs/persistent-volume.yaml # NFS 볼륨 설정

## 2.ConfigMap 설정 (환경 변수 및 설정) 
kubectl apply -f k8s/configmap.yaml # 공통 설정 적용

## 3. 데이터베이스 배포 (다른 서비스들이 의존하는 MongoDB)
kubectl apply -f k8s/mongodb/deployment.yaml # MongoDB 배포

## 4. 백엔드 서비스 배포 (순서대로)
kubectl apply -f k8s/gateway-service/deployment.yaml # API Gateway 서비스 배포
kubectl apply -f k8s/todo-service/deployment.yaml # Todo 서비스 배포
kubectl apply -f k8s/board-service/deployment.yaml # Board 서비스 배포
kubectl apply -f k8s/file-service/deployment.yaml # File 서비스 배포

## 5. 프론트엔드 배포
kubectl apply -f k8s/frontend/deployment.yaml # Frontend 배포

## 6. Ingress 설정 (외부 접근 라우팅)
kubectl apply -f k8s/ingress.yaml # Ingress 규칙 적용

## 7. 배포 확인
kubectl get all # 모든 리소스 상태 확인

kubectl get pods          # 파드 상태
kubectl get services      # 서비스 상태
kubectl get pv,pvc        # 스토리지 상태
kubectl get configmaps    # 설정 상태
kubectl get ingress       # 인그레스 상태

## 8. 로그 확인
kubectl logs -f deployment/frontend
kubectl logs -f deployment/gateway-service
kubectl logs -f deployment/todo-service
kubectl logs -f deployment/board-service
kubectl logs -f deployment/file-service

### 이 순서로 배포하는 이유
- 스토리지와 ConfigMap은 다른 서비스들이 의존하므로 먼저 배포
- MongoDB는 백엔드 서비스들이 의존하므로 먼저 배포
- Gateway 서비스를 먼저 배포하여 라우팅 준비
- 백엔드 서비스들 배포
- 프로로트엔드는 백엔드 API가 준비된 후 배포
- 마지막으로 Ingress로 외부 접근 설정
