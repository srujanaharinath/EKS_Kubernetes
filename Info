## Prerequisites

* **AWS CLI** installed and configured with appropriate AWS credentials (Access Key ID, Secret Access Key, and default region).
* **kubectl** installed and configured.
* **eksctl** installed for easy EKS cluster management.
* **Helm** installed for package management in Kubernetes.
* AWS account with permissions to create IAM roles, policies, EKS clusters, and related resources.
* An active **VPC** with subnets configured (can be created via `eksctl` or AWS Console).
* Basic knowledge of Kubernetes concepts: Pods, Services, Deployments, Ingress.

---

## Important Commands

### Cluster and Fargate Profile

* Create EKS cluster with Fargate support
  `eksctl create cluster --name <cluster-name> --region <region> --fargate`

* Create a Fargate profile for a namespace
  `eksctl create fargateprofile --cluster <cluster-name> --namespace <namespace> --name <profile-name>`

* Update kubeconfig to access cluster
  `aws eks update-kubeconfig --name <cluster-name> --region <region>`

---

### IAM Setup for ALB Controller

* Create IAM policy for ALB Controller
  `aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json`

* Create IAM service account with policy attached
  `eksctl create iamserviceaccount --cluster <cluster-name> --namespace kube-system --name aws-load-balancer-controller --attach-policy-arn arn:aws:iam::<account-id>:policy/AWSLoadBalancerControllerIAMPolicy --approve`

---

### Deploy ALB Controller with Helm

* Add EKS Helm repo
  `helm repo add eks https://aws.github.io/eks-charts`

* Update Helm repositories
  `helm repo update`

* Install AWS Load Balancer Controller

  ```
  helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
    -n kube-system \
    --set clusterName=<cluster-name> \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller \
    --set region=<region> \
    --set vpcId=<vpc-id>
  ```

---

### Kubernetes Commands

* Apply Deployment, Service, and Ingress manifests
  `kubectl apply -f <filename>.yml`

* Get pods in a namespace
  `kubectl get pods -n <namespace>`

* Get services in a namespace
  `kubectl get svc -n <namespace>`

* Get ingress resources
  `kubectl get ingress -n <namespace>`

* Check deployment status
  `kubectl get deploy -n <namespace>`
