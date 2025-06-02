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








# prometheus/prometheus.yml

global:
  scrape_interval: 15s
  evaluation_interval: 15s
  external_labels:
    monitor: 'node-monitor'

rule_files:
  - "alert.rules.yml"

scrape_configs:
  - job_name: 'node-app'
    static_configs:
      - targets: ['host.docker.internal:3001']

---
# prometheus/alert.rules.yml

groups:
  - name: example-alert
    rules:
      - alert: HighRequestLatency
        expr: http_request_duration_seconds_mean5m > 0.5
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "High request latency"

---
# grafana/provisioning/datasources/datasource.yml

datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true

---
# grafana/provisioning/dashboards/dashboards.yml

dashboardProviders:
  - name: 'default'
    orgId: 1
    folder: ''
    type: file
    disableDeletion: false
    updateIntervalSeconds: 10
    options:
      path: /var/lib/grafana/dashboards

---
# grafana/dashboards/node-app-dashboard.json

{
  "dashboard": {
    "id": null,
    "uid": null,
    "title": "Node.js App Metrics",
    "tags": [],
    "timezone": "browser",
    "panels": [
      {
        "type": "graph",
        "title": "HTTP Request Duration",
        "targets": [
          {
            "expr": "http_request_duration_seconds_mean5m",
            "legendFormat": "duration"
          }
        ],
        "datasource": "Prometheus"
      }
    ],
    "schemaVersion": 16,
    "version": 1,
    "refresh": "10s"
  }
}

---
# README.md

# Observability Monitoring

This setup uses Prometheus and Grafana to monitor a Node.js application running on Minikube in WSL.

## Components

- **Prometheus**: Metrics scraping and alerting
- **Grafana**: Visualization dashboards

## Setup

```bash
docker compose up -d
```

## Access

- Prometheus: http://localhost:9090
- Grafana: http://localhost:3000 (admin/admin)

## Dashboards & Alerts

- Check Prometheus targets tab for service discovery
- Grafana auto-loads the Node.js dashboard
- Prometheus alerts when HTTP request latency exceeds threshold

## Screenshots

Add these to the `screenshots/` folder:
- prometheus-targets.png
- grafana-dashboard.png
- alert-setup.png

## Notes

Ensure your Node.js app exposes metrics on port 3001 or adjust the Prometheus config accordingly.

