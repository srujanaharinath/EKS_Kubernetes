# EKS_Kubernetes

Prerequisites: kubectl, eksctl, aws cli

aws cli: need to provide access key, password and region (you will get from ur account under security creds -> access key -> create one)

Creating the cluster through command line interface:
 -- kubectl create cluster --name demo-cluster --region es-west-1 --fragate

Fragate: Fargate is a serverless compute engine for containers that works with both Amazon Elastic Container Service (ECS) and Amazon Elastic Kubernetes Service (EKS)
you can even choose data plane, worker nodes or ec2 instances 

<img width="1470" alt="image" src="https://github.com/user-attachments/assets/3eee723f-c273-4cd0-b867-5c318ad8ed7d" />

Created deploy.yml and service.yml

aws eks update-kubeconfig --name demo-cluster --region eu-west-1

Create new fargate namespace:
eksctl create fargateprofile \
    --cluster demo-cluster \
    --region eu-west-1 \
    --name alb-sample-app \
    --namespace game     

<img width="1470" alt="image" src="https://github.com/user-attachments/assets/b31bc937-d7f2-4a82-87d2-a1a7a0e25a56" />

application load balancer - Ingress

need to write the code for deployment, service and ingress controller

kubectl get pods -n game
kubectl get pods -n game -w
kubectl get svc -n game
kubectl get ingress -n game

ingress controller -> ingress-2048 -> load balancer -> target groups, port (ingress resources)

ALB controller
IAM OIDC Provider
eksctl utils associate-iam-oidc-provider --cluster demo-cluster --approve

ALB controller -> pod -> Access AWS services (ALB) - create IAM policies and role

create the helm chart - that will create the actual controllers, it help to create service accounts

now we need to check atleast 2 replicas have been created and running fine







 

 
