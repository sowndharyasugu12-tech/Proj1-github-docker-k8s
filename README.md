# CI/CD Pipeline with Docker, Kubernetes, Kind, and GitHub Actions

## Project Overview

This project demonstrates an end-to-end DevOps CI/CD workflow using:

* Node.js application
* Docker containerization
* Kubernetes deployment
* Kind (Kubernetes in Docker)
* GitHub Actions pipeline
* DockerHub image registry

The goal of this project is to automate application build and Kubernetes deployment using GitHub Actions.

---

# Architecture

```text
Developer Pushes Code to GitHub
              ↓
      GitHub Actions Pipeline
              ↓
        Build Docker Image
              ↓
     Push Image to DockerHub
              ↓
   Create Kubernetes Cluster (Kind)
              ↓
     Deploy Application to K8s
```

---

# Technologies Used

| Technology     | Purpose                  |
| -------------- | ------------------------ |
| Node.js        | Application Runtime      |
| Docker         | Containerization         |
| Kubernetes     | Container Orchestration  |
| Kind           | Local Kubernetes Cluster |
| GitHub Actions | CI/CD Pipeline           |
| DockerHub      | Container Registry       |
| kubectl        | Kubernetes CLI           |

---

# Project Structure

```text
.
├── .github/
│   └── workflows/
│       └── deploy.yml
├── deployment.yaml
├── service.yaml
├── Dockerfile
├── package.json
├── app.js
└── README.md
```

---

# Application

Simple Node.js application exposed on port 3000.

Example:

```javascript
const express = require('express')
const app = express()

app.get('/', (req, res) => {
  res.send('CI/CD Pipeline Working Successfully!')
})

app.listen(3000, () => {
  console.log('Application running on port 3000')
})
```

---

# Docker Setup

## Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY . .

RUN npm install

EXPOSE 3000

CMD ["node", "app.js"]
```

## Build Docker Image

```bash
docker build -t myapp .
```

## Run Container

```bash
docker run -p 3000:3000 myapp
```

Application URL:

```text
http://localhost:3000
```

---

# Kubernetes Setup

## Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:latest
        imagePullPolicy: Never
        ports:
        - containerPort: 3000
```

---

## Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 3000
      nodePort: 30007
```

---

# Kubernetes Concepts Learned

## Port Explanation

| Port Type     | Purpose                                      |
| ------------- | -------------------------------------------- |
| containerPort | Port exposed by application inside container |
| targetPort    | Pod port where traffic is forwarded          |
| port          | Internal Kubernetes service port             |
| nodePort      | External port for browser access             |

Traffic Flow:

```text
Browser → NodePort → Service Port → TargetPort → Application
```

---

# Kind Cluster Setup

## Install Kind

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

## Create Cluster

```bash
kind create cluster --name kind
```

## Verify Cluster

```bash
kubectl get nodes
```

---

# Deploy Application to Kubernetes

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

## Verify Resources

```bash
kubectl get pods
kubectl get svc
kubectl get deployments
```

---

# Access Application Locally

## Using Port Forwarding

```bash
kubectl port-forward service/myapp-service 8080:80
```

Application URL:

```text
http://localhost:8080
```

---

# GitHub Actions CI/CD Pipeline

## Workflow File

```yaml
name: CI-CD Pipeline

on:
  push:
    branches:
      - master

jobs:
  build-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout master branch
      uses: actions/checkout@v3

    - name: Install kind
      run: |
        curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
        chmod +x ./kind
        sudo mv ./kind /usr/local/bin/kind

    - name: Install kubectl
      run: |
        curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x kubectl
        sudo mv kubectl /usr/local/bin/

    - name: Create kind cluster
      run: kind create cluster --name kind

    - name: Build Docker image
      run: docker build -t myapp:latest .

    - name: Load image into kind
      run: kind load docker-image myapp:latest --name kind

    - name: Deploy to Kubernetes
      run: kubectl apply -f deployment.yaml
```

---

# GitHub Secrets Used

| Secret          | Purpose                |
| --------------- | ---------------------- |
| DOCKER_USERNAME | DockerHub Username     |
| DOCKER_PASSWORD | DockerHub Access Token |

---

# Challenges Faced and Fixes

## 1. Kubernetes localhost:8080 Error

### Issue

```text
connection refused localhost:8080
```

### Cause

kubectl was not connected to any Kubernetes cluster.

### Fix

Created Kind cluster before deployment.

---

## 2. DockerHub Unauthorized Error

### Issue

```text
unauthorized: access token has insufficient scopes
```

### Cause

DockerHub token had only read access.

### Fix

Created new DockerHub access token with Read + Write permissions.

---

## 3. NodePort Not Accessible

### Cause

Kind networking limitations inside Docker.

### Fix

Used Kubernetes port-forwarding.

---

# Key Learnings

* Docker image creation and containerization
* Kubernetes deployments and services
* Port forwarding and networking concepts
* GitHub Actions CI/CD automation
* DockerHub authentication
* Kubernetes cluster management using Kind
* Debugging real-world CI/CD issues

---

# Future Improvements

* Implement Rolling Updates
* Add Readiness and Liveness Probes
* Use Kustomize for dev/prod environments
* Deploy to cloud Kubernetes (EKS/GKE/AKS)
* Add Ingress Controller
* Add Monitoring and Logging

---

# Conclusion

This project helped in understanding practical DevOps workflow implementation using Docker, Kubernetes, and GitHub Actions.

The pipeline successfully automates:

* Application Build
* Containerization
* Kubernetes Deployment
* CI/CD Execution

---

# Author

Sowndharya Sugumaran
