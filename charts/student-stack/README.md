# Student Stack – Helm Deployment (Minikube)

A minimal Helm setup to deploy:
- **student-api** (Spring Boot REST API)
- **student-db** (MySQL 8 + PVC)
- **Kubernetes Secret** for DB credentials

## Build & Load API Image
eval $(minikube -p minikube docker-env)
docker build -t student-api:latest /path/to/student-api
eval $(minikube -p minikube docker-env -u)

## Install Chart
helm upgrade --install student-stack . -n student-api --create-namespace

## Check Status
kubectl get pods,svc,deploy,pvc -n student-api -o wide

## Access API
# NodePort
NODE_IP=$(minikube ip)
NODE_PORT=$(kubectl get svc -n student-api student-api-service -o jsonpath="{.spec.ports[0].nodePort}")
echo "http://$NODE_IP:$NODE_PORT"

# Or Port-Forward
kubectl port-forward -n student-api svc/student-api-service 8080:8087
# Visit: http://localhost:8080

## Uninstall
helm uninstall student-stack -n student-api
kubectl delete ns student-api
