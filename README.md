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


version: '3.8'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - ./prometheus/alert.rules.yml:/etc/prometheus/alert.rules.yml
    ports:
      - "9090:9090"
    network_mode: "host"
    # networks:
    #   - docker_cicd_net
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=admin
    network_mode: "host"
    # ports:
    #   - "3000:3000"
    volumes:
      - ./grafana/provisioning/datasources:/etc/grafana/provisioning/datasources
      - ./grafana/provisioning/dashboards:/etc/grafana/provisioning/dashboards
      - ./grafana/dashboards:/var/lib/grafana/dashboards
      - grafana-data:/var/lib/grafana
    depends_on:
      - prometheus
volumes:
  grafana-data:
    # networks:
    #   - docker_cicd_net

networks:
  docker_cicd_net:
    external: true
=====================================

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
      - targets: ['192.168.49.2:30080']
=========================================


groups:
  - name: Node.js Alerts
    rules:
      - alert: HighMemoryUsage
        expr: process_resident_memory_bytes > 100000000  # ~100MB
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "High memory usage on Node.js app"
          description: "Node.js memory is above 100MB for more than 1 minute."

      - alert: HighCPUUsage
        expr: rate(process_cpu_seconds_total[1m]) > 0.5
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage on Node.js app"
          description: "Node.js CPU usage > 50% for more than 1 minute."
		  
=============================================================================




