# node-k8s-deploy


# 🚀 Node.js Kubernetes Deployment with ArgoCD

This repository contains the Kubernetes manifests for deploying a Node.js application to a Kubernetes cluster using GitOps powered by **ArgoCD**.

---

## 📦 What’s Inside

This repo contains:

- `deployment/`
  - `kustomization.yaml` - Kustomize config for GitOps
  - `deployment.yaml` - Kubernetes Deployment
  - `service.yaml` - Kubernetes Service

---

## 🌐 Tech Stack

- **Node.js App** (Dockerized)
- **Docker Hub** (Container Registry)
- **Kubernetes** (via Minikube)
- **ArgoCD** (GitOps Deployment)
- **Kustomize** (Kubernetes manifest management)

---

## 🚀 How it Works

1. **CI Pipeline in Jenkins** builds and pushes the Docker image to Docker Hub.
2. Jenkins (or manual push) updates the manifests in this repo.
3. **ArgoCD** watches this repo and syncs the changes to your Kubernetes cluster.

---

## 🗂️ Directory Structure

node-k8s-deploy  
  deployment  
    -deployment.yaml  
    -service.yaml  
    -kustomization.yaml  



---

## 📦 Example: Kustomization File

apiVersion: kustomize.config.k8s.io/v1beta1  
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml

📡 ArgoCD Setup Reference
To deploy this app via ArgoCD UI:

Application Name: node-app

Project: default

Repo URL: https://github.com/priyanshu29/node-k8s-deploy.git

Revision: main

Path: deployment

Directory Type: Kustomize

Cluster: https://kubernetes.default.svc

Namespace: default

📌 Notes
Be sure the image in deployment.yaml is updated with the latest Docker Hub tag.

Sync policy can be set to Manual or Automatic in ArgoCD.

This repo is only for deployment manifests. The source code for the Node.js app lives elsewhere.

🧑‍💻 Author  
Priyanshu Tiwari



{
  "dashboard": {
    "id": null,
    "uid": null,
    "title": "Node.js App - Full Metrics",
    "tags": ["nodejs", "observability"],
    "timezone": "browser",
    "schemaVersion": 30,
    "version": 1,
    "refresh": "10s",
    "panels": [
      {
        "type": "stat",
        "title": "Total Requests",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "node_app_requests_total", "format": "time_series" }],
        "gridPos": { "x": 0, "y": 0, "w": 6, "h": 4 }
      },
      {
        "type": "stat",
        "title": "CPU Time (User)",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "process_cpu_user_seconds_total", "format": "time_series" }],
        "gridPos": { "x": 6, "y": 0, "w": 6, "h": 4 }
      },
      {
        "type": "stat",
        "title": "CPU Time (System)",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "process_cpu_system_seconds_total", "format": "time_series" }],
        "gridPos": { "x": 0, "y": 4, "w": 6, "h": 4 }
      },
      {
        "type": "stat",
        "title": "Resident Memory (MB)",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "process_resident_memory_bytes / 1024 / 1024", "format": "time_series" }],
        "gridPos": { "x": 6, "y": 4, "w": 6, "h": 4 }
      },
      {
        "type": "stat",
        "title": "Virtual Memory (MB)",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "process_virtual_memory_bytes / 1024 / 1024", "format": "time_series" }],
        "gridPos": { "x": 0, "y": 8, "w": 6, "h": 4 }
      },
      {
        "type": "stat",
        "title": "Open File Descriptors",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "process_open_fds", "format": "time_series" }],
        "gridPos": { "x": 6, "y": 8, "w": 6, "h": 4 }
      },
      {
        "type": "stat",
        "title": "Heap Used (MB)",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "nodejs_heap_size_used_bytes / 1024 / 1024", "format": "time_series" }],
        "gridPos": { "x": 0, "y": 12, "w": 6, "h": 4 }
      },
      {
        "type": "stat",
        "title": "Heap Total (MB)",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "nodejs_heap_size_total_bytes / 1024 / 1024", "format": "time_series" }],
        "gridPos": { "x": 6, "y": 12, "w": 6, "h": 4 }
      },
      {
        "type": "stat",
        "title": "Event Loop Lag (p99)",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "nodejs_eventloop_lag_p99_seconds * 1000", "format": "time_series" }],
        "gridPos": { "x": 0, "y": 16, "w": 6, "h": 4 },
        "description": "ms"
      },
      {
        "type": "stat",
        "title": "GC Duration (Total Seconds)",
        "datasource": "prometheus-1",
        "targets": [{ "expr": "nodejs_gc_duration_seconds_sum", "format": "time_series" }],
        "gridPos": { "x": 6, "y": 16, "w": 6, "h": 4 }
      }
    ]
  },
  "overwrite": true
}





