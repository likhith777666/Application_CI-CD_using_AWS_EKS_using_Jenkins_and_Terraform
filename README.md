# Project: End-to-End DevOps CI/CD Pipeline with AWS EKS, Jenkins & ArgoCD

This project demonstrates a complete production-style DevOps workflow where a three-tier application (Frontend, Backend, Database) is automatically built, analyzed, containerized, and deployed into Kubernetes using GitOps principles.

The entire system is designed to reflect real-world cloud architecture, combining Infrastructure as Code, CI/CD automation, and Kubernetes-based deployment on AWS.

            ┌──────────────┐
            │  Developer   │
            └──────┬───────┘
                   │
                   ▼
            ┌──────────────┐
            │   GitHub     │
            └──────┬───────┘
                   │
                   ▼
            ┌──────────────┐
            │   Jenkins    │
            │ (CI Pipeline)│
            └──────┬───────┘
                   │
        ┌──────────┼──────────┐
        ▼                     ▼
 ┌──────────────┐     ┌──────────────┐
 │ SonarQube    │     │   Docker     │
 │ Code Quality │     │ Build Image  │
 └──────────────┘     └──────┬───────┘
                             ▼
                     ┌──────────────┐
                     │Docker reg    │
                     └──────┬───────┘
                            ▼
                     ┌──────────────┐
                     │   ArgoCD     │
                     │ (GitOps CD)  │
                     └──────┬───────┘
                            ▼
                     ┌──────────────┐
                     │   AWS EKS    │
                     │ Kubernetes   │
                     └──────┬───────┘
                            ▼
               ┌────────────────────────┐
               │ AWS ALB (Ingress)      │
               └─────────┬──────────────┘
                         ▼
                      Users 🌍

# Infrastructure (Terraform)

The AWS infrastructure is provisioned using Terraform in a separate repository:
https://github.com/likhith777666/Creating_AWS_EKS_Cluster_using_terraform_with_jenkins.git
Includes:
VPC (Public & Private Subnets)
Internet Gateway & NAT Gateway
Route Tables & Associations
Security Groups
EKS Cluster & Node Groups
IAM Roles (Cluster, Node, OIDC)

⚙️ CI/CD Workflow
# Continuous Integration (Jenkins)
Source code is pushed to GitHub
Jenkins pipeline is triggered automatically
Performs:
Build
SonarQube Analysis
Dependency Check
Docker Image Build
Push to AWS ECR
Updates Kubernetes manifests in GitHub

# Continuous Deployment (ArgoCD - GitOps)
ArgoCD monitors Git repository
Detects manifest changes
Automatically syncs with EKS cluster
Enables:
Auto deployment
Self-healing
Rollback capability

# Kubernetes Deployment (EKS)

Deployed components:

🔹 Backend (Node.js)
REST APIs (/api/tasks)
Health endpoints:
/healthz
/ready
/started
🔹 Frontend (React)
Connects to backend via Ingress
🔹 Database
MongoDB deployed inside cluster

# Networking
AWS Load Balancer Controller
Converts Kubernetes Ingress → AWS ALB
Handles external traffic routing

# Security & IAM
Implemented secure access using:

IAM Roles
EKS Cluster Role
Node Group Role
OIDC (IRSA for pods)
OIDC (IRSA)
Enables pods to securely access AWS services
Avoids using node-level credentials

# Monitoring & Observability
Prometheus
Collects cluster metrics
Grafana
Visual dashboards
Real-time monitoring

# Challenges & Debugging

During implementation, I resolved several real-world issues:
Terraform implementation for addons, IAM policys, IAM roles fixed using documentation.
Fixed IAM permission errors for ALB controller
Debugged Kubernetes service selector mismatch
Resolved ArgoCD sync failures due to incorrect paths
Fixed Ingress routing issues
Debugged frontend-backend communication errors
Handled EKS authentication and kubeconfig issues

# Tech Stack
AWS (EC2, EKS, S3- to store state files of terraform,IAM, VPC, ALB)
Terraform
Jenkins
Docker
Kubernetes
ArgoCD
SonarQube
Prometheus & Grafana
Node.js & React

## How to Run

For detailed setup instructions, follow the complete guide:

👉 **[View Full Setup Documentation](https://github.com/likhith777666/Three_tier_app_CI-CD_using_AWS_EKS_using_Jenkins_and_Terraform/blob/main/doc/how-to-run.md)**
