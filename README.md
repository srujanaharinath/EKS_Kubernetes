# EKS_Kubernetes

Thanks for sharing this summary. You're outlining a typical AWS EKS + Fargate + ALB Ingress Controller deployment pipeline using `kubectl`, `eksctl`, and `helm`.

To help structure it more clearly and ensure accuracy, here's a cleaned-up and actionable version of your notes:

---

## ✅ **Prerequisites**

1. **AWS CLI** – authenticated with an access key.
2. **kubectl** – Kubernetes CLI.
3. **eksctl** – CLI for EKS cluster management.
4. **helm** – Kubernetes package manager.

---

## ✅ **Step-by-Step Setup Guide**

### 🔐 1. Configure AWS CLI

```bash
aws configure
```

Provide:

* **Access Key ID**
* **Secret Access Key**
* **Region** (e.g., `eu-west-1`)

---

### ☁️ 2. Create EKS Cluster with Fargate Profile

```bash
eksctl create cluster \
  --name demo-cluster \
  --region eu-west-1 \
  --fargate
```

> ✅ `--fargate` makes the data plane serverless.

---

### 📁 3. Create Namespace and Fargate Profile

```bash
eksctl create fargateprofile \
  --cluster demo-cluster \
  --region eu-west-1 \
  --name alb-sample-app \
  --namespace game
```

---

### 📦 4. Update kubeconfig

```bash
aws eks update-kubeconfig \
  --name demo-cluster \
  --region eu-west-1
```

---

### 📜 5. Apply Kubernetes Manifests

**`deploy.yml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: game-app
  namespace: game
spec:
  replicas: 2
  selector:
    matchLabels:
      app: game
  template:
    metadata:
      labels:
        app: game
    spec:
      containers:
      - name: game
        image: <your-app-image>
        ports:
        - containerPort: 80
```

**`service.yml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: game-service
  namespace: game
spec:
  selector:
    app: game
  ports:
    - port: 80
      targetPort: 80
  type: NodePort
```

Apply them:

```bash
kubectl apply -f deploy.yml
kubectl apply -f service.yml
```

---

### 🚪 6. Install ALB Ingress Controller

#### a. Associate IAM OIDC Provider

```bash
eksctl utils associate-iam-oidc-provider \
  --cluster demo-cluster \
  --approve
```

#### b. Create IAM Policy

Download the IAM policy for ALB controller:

```bash
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```

Create the policy:

```bash
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam-policy.json
```

#### c. Create Service Account

```bash
eksctl create iamserviceaccount \
  --cluster demo-cluster \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

#### d. Install ALB Controller via Helm

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=eu-west-1 \
  --set vpcId=<your-vpc-id>
```

---

### 🔁 7. Create Ingress Resource

**`ingress.yml`**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: game-ingress
  namespace: game
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: game-service
            port:
              number: 80
```

Apply it:

```bash
kubectl apply -f ingress.yml
```

---

### 🔍 8. Monitor Resources

```bash
kubectl get pods -n game -w
kubectl get svc -n game
kubectl get ingress -n game
```

Check that **2 replicas** of the app are running and the **Ingress** is assigned a **LoadBalancer DNS** name.

---

### 🌐 9. Access Application

Use the DNS name from `kubectl get ingress -n game` to access the app in your browser.

---

Let me know if you need the `Helm` chart template for packaging everything (`Chart.yaml`, `values.yaml`, etc.) or want to automate this with a script.




 

 
