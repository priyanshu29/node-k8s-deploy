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





pipeline {
  agent any
  environment {
    SONAR_TOKEN = credentials('sonar-token-id')
    DOCKER_HUB_USER = 'priyanshu0998'
    DOCKER_IMAGE = 'node-app'
    REGISTRY = 'docker.io'
  }

  stages {
    stage('Checkout Application Code') {
      steps {
        git branch: 'main', url: 'https://github.com/<your-user>/<node-repo>'
      }
    }

    stage('Code Analysis using SonarQube') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh 'sonar-scanner'
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $DOCKER_HUB_USER/$DOCKER_IMAGE:latest .'
      }
    }

    stage('Push into Image Registry') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'DOCKER_PWD', usernameVariable: 'DOCKER_USER')]) {
          sh '''
            echo "$DOCKER_PWD" | docker login -u "$DOCKER_USER" --password-stdin
            docker push $DOCKER_HUB_USER/$DOCKER_IMAGE:latest
          '''
        }
      }
    }

    stage('Deploy into Kubernetes Cluster using ArgoCD') {
      steps {
        sh '''
          git clone https://github.com/<your-user>/node-k8s-deploy.git
          cd node-k8s-deploy/deployment
          sed -i 's|priyanshu0998/node-app:.*|priyanshu0998/node-app:latest|' deployment.yaml
          git config user.email "you@example.com"
          git config user.name "Your Name"
          git commit -am "Updated image tag to latest"
          git push origin main
        '''
      }
    }
  }
}

