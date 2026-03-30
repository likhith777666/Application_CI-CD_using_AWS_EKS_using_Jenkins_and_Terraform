
#  How to Run This Project (Step-by-Step Guide)

This guide explains how to set up the complete infrastructure and deploy the application using **Terraform, Jenkins, EKS, ArgoCD, and Kubernetes**.

---

## 🔹 Step 1: Create Jenkins Server (EC2)

1. Launch an EC2 instance:

   * OS: Ubuntu
   * Instance Type: `t2.2xlarge`
   * Storage: 30 GB
   * Security Group: Open ports

     * `8080` → Jenkins
     * `9090` → SonarQube
   * IAM Role: Attach required permissions (e.g., `AA-access`)

2. Install Jenkins using user data or manually.

3. Access Jenkins:

```text
http://<EC2-Public-IP>:8080
```

4. Unlock Jenkins:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## 🔹 Step 2: Configure Jenkins

Install required plugins:

* AWS Credentials
* Pipeline AWS Steps
* Docker & Docker Pipeline
* SonarQube Scanner
* NodeJS
* OWASP Dependency Check

### Configure Tools:

* Terraform (manual path setup)
* NodeJS
* Sonar Scanner
* Docker

### Add Credentials:

* AWS Access Key & Secret → `aws-cred`
* GitHub Token → `GITHUB_APP`
* SonarQube Token → `sonar-token`
* AWS Account ID → `ACCOUNT_ID`

---

## 🔹 Step 3: Create AWS Infrastructure using Terraform

Use the Terraform repository:

👉 [https://github.com/likhith777666/Creating_AWS_EKS_Cluster_using_terraform_with_jenkins.git](https://github.com/likhith777666/Creating_AWS_EKS_Cluster_using_terraform_with_jenkins.git)

Run Terraform via Jenkins pipeline to create:

* VPC (Public & Private Subnets)
* Internet Gateway
* NAT Gateway
* Route Tables
* Security Groups
* EKS Cluster
* Node Groups
* IAM Roles

---

## 🔹 Step 4: Create Jump Server (EKS Access Node)

1. Launch another EC2 instance:

   * OS: Ubuntu
   * Instance Type: `t2.micro`
   * Subnet: **Public subnet of EKS VPC**
   * IAM Role: `AmazonSSMManagedInstanceCore`

2. Install:

   * AWS CLI
   * kubectl
   * eksctl
   * helm

3. Configure AWS:

```bash
aws configure
```

4. Connect to EKS cluster:

```bash
aws eks update-kubeconfig --name <cluster-name>
```

---

## 🔹 Step 5: Configure Network Access

* Allow **jump server private IP** in EKS-related security groups
* This ensures secure communication with the cluster

---

## 🔹 Step 6: Install AWS Load Balancer Controller

1. Download IAM policy:

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```

2. Create IAM policy:

```bash
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

3. Create IAM Role + Service Account (IRSA)

4. Install controller using Helm:

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=<cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```

---

## 🔹 Step 7: Install ArgoCD

1. Create namespace:

```bash
kubectl create namespace argocd
```

2. Apply ArgoCD manifests:

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

3. Expose ArgoCD:

```bash
kubectl edit svc argocd-server -n argocd
```

Change:

```yaml
type: LoadBalancer
```

4. Get password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
```

---

## 🔹 Step 8: Configure SonarQube

1. Access SonarQube:

```text
http://<EC2-IP>:9000
```

2. Generate token:

* Go to → Users → Security → Tokens

3. Configure Jenkins with SonarQube server and token

---

## 🔹 Step 9: Create ECR Repositories

Create repositories for:

* Frontend
* Backend

Add credentials in Jenkins:

* `ECR_REPO1` (frontend)
* `ECR_REPO2` (backend)

---

## 🔹 Step 10: Create Jenkins Pipelines

Create pipelines for:

* Frontend
* Backend

Pipeline stages:

* Code checkout
* Build
* SonarQube analysis
* Docker build
* Push to ECR
* Update Kubernetes manifests

---

## 🔹 Step 11: Configure ArgoCD Applications

1. Connect GitHub repo in ArgoCD

2. Create applications:

* Database (MongoDB + backend + ingress if grouped)
* Frontend

3. Enable:

* Auto-sync
* Self-heal

---

## 🔹 Step 12: Deploy Application (Ingress + ALB)

* Kubernetes Ingress is created
* AWS Load Balancer Controller detects it
* Automatically provisions **ALB**
* Routes traffic to services

---

## 🔹 Step 13: Access Application

```text
http://<ALB-DNS>
```

Optional:

* Map custom domain using Route53

---

## 🔹 Step 14: Monitoring Setup

### Install Prometheus:

```bash
helm install prometheus prometheus-community/prometheus
```

Expose service:

```bash
kubectl edit svc prometheus-server
```

Set:

```yaml
type: LoadBalancer
```

---

### Install Grafana:

```bash
helm install grafana grafana/grafana
```

Get password:

```bash
kubectl get secret grafana -o jsonpath="{.data.admin-password}" | base64 --decode
```

---

### Configure Dashboard:

* Add Prometheus as datasource
* Import dashboards (e.g., 6417, 17375)

---

## 🔹 Step 15: Verify

* Jenkins pipeline success ✅
* ArgoCD synced ✅
* Pods running ✅
* ALB created ✅
* Application accessible ✅

---

# 🎯 Final Outcome

You will have:

* Fully automated CI/CD pipeline
* Infrastructure as Code using Terraform
* Kubernetes deployment on AWS EKS
* GitOps workflow using ArgoCD
* Monitoring with Prometheus & Grafana

---


