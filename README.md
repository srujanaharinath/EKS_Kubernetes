📘 Project Description

This project sets up a containerized web application on Amazon EKS using Fargate for serverless compute, and exposes it to the internet using an AWS Application Load Balancer (ALB) via Ingress. The infrastructure is provisioned and managed using eksctl, kubectl, helm, and the aws CLI.

Key components:
1. Amazon EKS: Manages the Kubernetes control plane.
2. Fargate: Runs pods without provisioning EC2 worker nodes.
3. ALB Ingress Controller: Automatically provisions an Application Load Balancer for incoming HTTP(S) traffic.
4. Helm: Used to install the AWS ALB Controller.

Workflow:

                    ┌────────────────────┐
                    │  End User / Client │
                    └────────┬───────────┘
                             │ HTTP request
                             ▼
                    ┌────────────────────┐
                    │ Application Load   │
                    │ Balancer (Ingress) │
                    └────────┬───────────┘
                             │ Routes traffic
                      ┌──────▼──────┐
                      │  Ingress    │
                      │  Resource   │
                      └──────┬──────┘
                             │ Loads balances requests
                      ┌──────▼──────┐
                      │  Service    │
                      └──────┬──────┘
                             │ Routes to pods
                  ┌──────────▼──────────┐
                  │   Fargate Pods      │
                  │ (game-app, replicas)│
                  └─────────────────────┘


