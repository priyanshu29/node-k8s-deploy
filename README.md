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








# Assignment Documentation: User Guide for Assignments 1 to 4

---

## Assignment 1: SSH User Provisioning Script

### Objective

Develop a Bash/Python script to automate adding a new user with sudo access and an SSH public key.

### Steps:

1. **Script:** Written in Bash, it:

   * Adds a new user
   * Grants sudo access
   * Sets up SSH key authentication

```bash
#!/bin/bash
USERNAME=$1
SSH_KEY=$2

sudo useradd -m -s /bin/bash "$USERNAME"
sudo mkdir -p /home/$USERNAME/.ssh
echo "$SSH_KEY" | sudo tee /home/$USERNAME/.ssh/authorized_keys
sudo chmod 600 /home/$USERNAME/.ssh/authorized_keys
sudo chmod 700 /home/$USERNAME/.ssh
sudo chown -R $USERNAME:$USERNAME /home/$USERNAME/.ssh
sudo usermod -aG sudo $USERNAME
```

### Usage:

```bash
./create_user.sh newuser "ssh-rsa AAAA..."
```

---

## Assignment 2: Infrastructure Provisioning using Terraform Modules

### Objective

Provision a single EC2 instance with required resources using Terraform modules.

### Directory Structure:

```
terraform-infra/
├── main.tf
├── variables.tf
├── outputs.tf
└── modules/
    ├── compute/
    ├── networking/
    ├── storage/
    └── database/
```

### Commands:

```bash
terraform init
terraform plan
terraform apply
```

Resources created:

* VPC
* Subnet
* Internet Gateway
* Route Table
* Security Group
* EC2 instance

---

## Assignment 3: CI/CD Pipeline using Jenkins and GitOps

### Objective

Set up a CI/CD pipeline using Jenkins to deploy a Node.js app to Kubernetes via ArgoCD.

### Tools Used:

* Jenkins (UI at `localhost:8000`)
* SonarQube (UI at `localhost:9000`)
* Docker
* ArgoCD (UI at `localhost:8081`)

### Jenkinsfile:

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') { steps { git 'https://github.com/user/node-app-cicd.git' } }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonar') {
                    sh 'sonar-scanner'
                }
            }
        }
        stage('Build & Push Docker Image') {
            steps {
                sh 'docker build -t priyanshu0998/node-app .'
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u priyanshu0998 --password-stdin'
                    sh 'docker push priyanshu0998/node-app'
                }
            }
        }
        stage('Deploy via ArgoCD') {
            steps {
                sh 'git clone https://github.com/user/node-k8s-deploy.git'
                sh 'sed -i "s|image: .*|image: priyanshu0998/node-app|" node-k8s-deploy/deployment.yaml'
                sh 'cd node-k8s-deploy && git commit -am "update image" && git push'
            }
        }
    }
}
```

---

## Assignment 4: Observability using Prometheus and Grafana

### Objective

Monitor Node.js application running on Kubernetes using Prometheus, Grafana, and Alertmanager.

### Services (docker-compose):

* Prometheus
* Grafana
* Alertmanager

### Folder Structure:

```
monitoring/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml
├── grafana/
│   ├── grafana.ini
│   └── provisioning/
│       ├── datasources/
│       └── dashboards/
├── alertmanager/
│   ├── config-template.yml
│   ├── config.yml (generated)
│   └── entrypoint.sh
```

### Configurations:

* **SMTP (Grafana Alerts):** Configured in `grafana.ini`
* **Alert Rules (Grafana UI):**

  * CPU Usage
  * Memory Usage
  * Total Requests
  * Event Loop Lag
  * Service Up/Down

### Bonus: Creating Alerts in Stat Panels

Since stat panels don't support alerts, use "Time Series" panel type for alerts.

### Sample Prometheus Queries:

* CPU Usage:

```promql
rate(process_cpu_user_seconds_total[1m]) * 100
```

* Memory Usage:

```promql
process_resident_memory_bytes / 1024 / 1024
```

* Event Loop Lag:

```promql
nodejs_eventloop_lag_seconds
```

* Total Requests:

```promql
http_requests_total
```

* Service Up:

```promql
up{job="node-app"} == 0
```

---

## Accessing Logs and Metrics

### Prometheus UI:

1. Open browser → Visit `http://localhost:9090`
2. Use query bar to execute Prometheus queries (e.g., `rate(http_requests_total[1m])`)

### Grafana UI:

1. Visit `http://localhost:3000`
2. Dashboard → Node-app → Main Dashboard of Node.js Metrics
3. Panels show CPU, memory, requests, event loop lag

### Logs:

* Use `docker logs <container>`
* Application logs via `kubectl logs -l app=node-app`

---

## Exported Grafana Dashboard & Alerts

* Dashboard: *Node-app/Main Dashboard of Node.js Metrics*
* Folder: *Node-app*
* Alert rules created via UI (Grafana 9.x+): Configured for:

  * CPU above 70% for 1 min
  * Memory usage above 200MB for 1 min
  * Total requests rate spike
  * Event loop lag above 1s
  * Service down (up == 0)

All alerts use the default evaluation group and Gmail SMTP notification.

---

## Conclusion

This completes the 4 assignments with:

* Automated user provisioning
* Modular Terraform infra setup
* Full Jenkins-based CI/CD with ArgoCD GitOps
* Prometheus/Grafana-based observability with alerts

Let me know if you want PDF export, screenshots, or dashboard JSONs included.


