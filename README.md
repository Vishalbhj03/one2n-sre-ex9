# Exercise 9 — One-Click GitOps Deployments Using ArgoCD

``` bash
## Objective
Set up ArgoCD to automatically deploy the student-stack (API + DB) whenever new
code is merged and CI successfully updates the image tag in Helm values.yaml.

## Project Structure
charts/student-stack/            # Helm chart for student-api + student-db
│── Chart.yaml
│── values.yaml
│── values-secret.yaml
│── templates/

dependent_services/argocd/
│── install/                     # ArgoCD CRDs + core installation (declarative)
│── repo-creds.yaml              # Git repo access credentials (placeholders only)
│── projects.yaml                # ArgoCD project
│── applications/                # ArgoCD app manifests for API + DB
│── node-selector-patches/       # Schedule ArgoCD on dependent_services node

.github/workflows/
│── update-image-tag.yml         # GitHub Action to update Helm image tag


## Run ArgoCD on dependent_services node
kubectl label node <NODE_NAME> node-role.kubernetes.io/dependent-services=true
kubectl apply -f dependent_services/argocd/node-selector-patches/

##  Apply Declarative ArgoCD Configuration
kubectl apply -f dependent_services/argocd/repo-creds.yaml
kubectl apply -f dependent_services/argocd/projects.yaml
kubectl apply -f dependent_services/argocd/applications/

##  GitOps Deployment Flow
1) Developer merges code  
2) CI builds + pushes Docker image  
3) update-image-tag.yml updates Helm values.yaml with new tag  
4) ArgoCD auto-sync detects commit and deploys the new version  

##  Source of Truth
ArgoCD syncs directly from:
- charts/student-stack/Chart.yaml
- charts/student-stack/values.yaml

All workloads, configs, and secrets are Kubernetes-manifest-driven and Git-managed.


