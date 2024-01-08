# 1. Create Namespace

- 환경의 논리적인 영역을 구분하기 위해 namespace 를 생성합니다.

```bash
kubectl apply -f deployment/namespace-staging.yaml
```

# 2. Create PV & PVC

- 실제 물리적인 스토리지의 연결을 위해 pv, pvc 를 생성합니다.

```bash
kubectl get nodes

mkdir -p /tmp/pv
chmod 777

kubectl apply -f deployment/pv.yaml
kubectl apply -f deployment/pvc.yaml
```

# 3. Install MySQL

- MySQL 을 kubernetes 환경에 deploy 합니다.
- MySQL 의 Port 접근을 허용하기 위해 service 를 생성합니다.

```bash
kubectl apply -f deployment/mysql/deployment.yaml
kubectl apply -f deployment/mysql/service.yaml
```
