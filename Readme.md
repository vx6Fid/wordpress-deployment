# WordPress Deployment on Kubernetes with CI/CD & Monitoring
This project implements a production-style deployment of WordPress on Kubernetes using **Helm**, automated rolling upgrades via **GitHub Actions (self-hosted runner)**, and cluster observability using **Prometheus & Grafana**.

The entire system is designed to demonstrate real DevOps workflows including declarative deployments, automated releases, and metrics-driven insights.

## **Architecture Overview**
* **Kubernetes (Minikube)** for orchestration
* **Helm (Bitnami WordPress chart)** for declarative application deployment
* **GitHub Actions** (self-hosted runner) for CI/CD automation
* **Ingress (NGINX)** for application routing
* **Persistent Volume Claims** for WordPress & MariaDB storage
* **Prometheus Operator + Grafana** for monitoring & dashboards

**CI/CD Flow:**
Git push → GitHub Actions → Helm upgrade → Kubernetes rolling update

## **Repository Structure**
```
.
├── values.yaml                 # Helm configuration for WordPress deployment
├── helm-release-name.txt       # Helm release identifier
├── namespace.txt               # Kubernetes namespace reference
└── .github/
    └── workflows/
        └── deploy.yaml         # GitHub Actions CI/CD pipeline
```

## **Deployment Steps**
### **1. Add Helm repository**
```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```
### **2. Deploy WordPress**
```
helm install wp bitnami/wordpress -n wordpress -f values.yaml
```
### **3. Expose via Ingress**
Configure host in your etc/hosts:
```
wordpress.test
```
Then:
```
minikube tunnel
```

## **CI/CD Pipeline**
Located in `.github/workflows/deploy.yaml`.
Pipeline operations:
1. Checkout repository
2. Validate Kubernetes access
3. Add/update Helm repo
4. Apply changes via Helm upgrade:
```
helm upgrade wp bitnami/wordpress -n wordpress -f values.yaml
```
### **Trigger:**
Any push to the `main` branch automatically updates the running application on the cluster.


## **Monitoring Setup**
This project uses **kube-prometheus-stack** for system and pod-level metrics.
Install:

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring
```

Grafana is exposed via:

```
kubectl port-forward svc/monitoring-grafana 3000:80 -n monitoring
```

Default dashboards provide:
* Node & pod metrics
* Cluster health
* Workload performance

A custom WordPress dashboard (CPU, memory, restarts, pod status) is included.

## **Key Features**
* Automated, versioned deployments using Helm
* GitOps-style CI/CD with self-hosted GitHub runner
* Zero-downtime updates with Kubernetes rollouts
* Persistent storage for WordPress & MySQL/MariaDB
* Full observability via Prometheus + Grafana
* Custom application health dashboard

## **Requirements**
* Minikube (with ingress + metrics-server enabled)
* Docker
* Helm 3
* kubectl
* GitHub Actions self-hosted runner
* Prometheus & Grafana installed via Helm
