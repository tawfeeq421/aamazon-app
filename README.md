# 🚀 Amazon App - End-to-End DevOps Project

## 👨‍💻 Author

**Tawfeeq Ahmed**
*DevOps Engineer*

---

# 📌 Project Overview

This project demonstrates a complete **CI/CD pipeline** using:

* Jenkins for automation
* Docker for containerization
* Trivy for security scanning
* Terraform for infrastructure
* AWS EKS for Kubernetes deployment

---

# 🏗️ Architecture

```bash
Developer → GitHub → Jenkins → Docker → Trivy → DockerHub → EKS → Live Application
```

---

# 🛠️ Technologies Used

* React.js
* Docker
* Jenkins
* Terraform
* AWS EKS
* Kubernetes
* Nginx
* Trivy

---

# 📂 Project Structure

```bash
aamazon-app/
├── Dockerfile
├── Jenkinsfile
├── EKS/
├── JENKINS-TF/
├── nginx.conf
├── src/
├── public/
```

---

# ⚙️ Prerequisites

* AWS Account
* IAM User with Admin Access
* DockerHub Account

### Install Required Tools:

* AWS CLI
* Terraform
* kubectl
* Docker
* Jenkins

---

# ☁️ Step 1: Create EKS Cluster using Terraform

```bash
cd EKS
terraform init
terraform validate
terraform apply -auto-approve
```

---

# 🔗 Step 2: Configure kubectl

```bash
aws eks --region ap-south-1 update-kubeconfig --name <cluster-name>
kubectl get nodes
```

---

# 🖥️ Step 3: Setup Jenkins Server

```bash
cd JENKINS-TF
terraform init
terraform apply -auto-approve
```

Access Jenkins:

```
http://<EC2-IP>:8080
```

---

# 🔧 Step 4: Configure Jenkins

Install tools:

* Docker
* Trivy
* NodeJS
* Git

Add credentials:

* DockerHub → `docker-cred`

---

# 🐳 Step 5: Docker Build & Push

```bash
docker build -t <dockerhub-username>/amazon-app .
docker push <dockerhub-username>/amazon-app
```

---

# 🔐 Step 6: Trivy Security Scan

```bash
trivy image --severity HIGH,CRITICAL --exit-code 1 <image>
```

---

# 🔄 Step 7: Jenkins Pipeline Flow

1. Clean Workspace
2. Checkout Code
3. Build Application
4. Docker Build
5. Trivy Scan
6. Push Image
7. Deploy to EKS

---

# ☸️ Step 8: Kubernetes Deployment

```bash
kubectl apply -f namespace.yml
kubectl apply -f amazon-app-deployment.yml
kubectl apply -f amazon-app-service.yml
kubectl apply -f ingress.yml
```

---

# 🌐 Step 9: Access Application

```bash
kubectl get svc
```

Open:

```
http://<EXTERNAL-IP>
```

---

# ⚠️ Best Practices

* Multi-stage Docker builds
* Security scanning with Trivy
* Terraform for IaC
* Kubernetes orchestration
* CI/CD automation

---

# 🎯 Interview Explanation

> "This project implements a complete CI/CD pipeline using Jenkins, Docker, Terraform, and AWS EKS. It includes security scanning with Trivy and automated deployment to Kubernetes."

---

# 🚀 Future Enhancements

* Helm Charts
* ArgoCD (GitOps)
* Monitoring (Prometheus + Grafana)
* HTTPS with TLS

---

# 🙌 Conclusion

A complete **real-world DevOps project** covering CI/CD, security, containerization, and Kubernetes deployment.

---
