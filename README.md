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
---

# Docker Setup
Build and containarize the application
---

# Kubernetes Setup

Deployed application to kind k8s cluster

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



# Access Application Locally



# GitHub Actions CI/CD Pipeline


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
