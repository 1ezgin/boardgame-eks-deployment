Project Structure

boardgame-eks-deployment/
├── kubernetes/           # K8s manifests (Deployment, Service, Ingress)
├── eks-setup/            # Cluster-as-Code configuration (eksctl)
├── alb-controller/       # AWS Load Balancer Controller setup & policy
└── README.md             # Documentation


Tech Stack

Orchestration: Kubernetes (EKS v1.32)

IaC: eksctl (Cluster-as-Code)

Networking: Ingress, Service, AWS Load Balancer Controller

Package Management: Helm (used for controller installation)

Cloud: AWS (EC2, VPC, IAM, Fargate, ALB)

Deployment Steps

1. Cluster Provisioning

The cluster configuration is defined in eks-setup/cluster-config.yaml. Provisioning is done via:

eksctl create cluster -f eks-setup/cluster-config.yaml


2. Security Configuration (OIDC)

To enable the controller to manage AWS resources, an OIDC trust relationship is established:

eksctl utils associate-iam-oidc-provider --cluster demo-cluster --approve


3. AWS Load Balancer Controller Installation

Installed via Helm using a custom values.yaml for VPC integration:

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system -f alb-controller/helm-values.yaml


4. Application Deployment

The 2048 game manifests are split into logical layers (Deployment, Service, Ingress):

kubectl apply -f kubernetes/


Why is this solution Robust & Stable?

Multi-AZ Deployment: Pods are distributed across three AWS data centers to prevent downtime.

No Hardcoded Secrets: Utilizing IAM Roles for Service Accounts (IRSA) eliminates the risk of credential leakage.

Declarative Infrastructure: Everything is defined as code (IaC), allowing the cluster to be recreated from scratch in minutes.

Zero-Maintenance Nodes: Fargate handles OS patching and resource scaling, letting engineers focus on the application.
