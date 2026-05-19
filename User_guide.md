# Deploying a Kubernetes Cluster on AWS EKS

This project demonstrates the step-by-step process of creating a Kubernetes cluster on AWS using Amazon EKS (Elastic Kubernetes Service) and deploying a sample NGINX pod.

---

# Tech Stack

- AWS EKS
- Kubernetes
- AWS CLI
- kubectl
- eksctl

---

# Architecture Overview

```text
Local Machine
     |
     v
AWS CLI + kubectl + eksctl
     |
     v
Amazon EKS Control Plane
     |
     v
EC2 Worker Nodes
     |
     v
Kubernetes Pods & Services
```

---

# Step 1: Create AWS Account

Create an AWS account from:

https://aws.amazon.com/

After successful signup:

- Login to AWS Console
- Avoid using the root user for regular activities
- Use IAM users for administrative access

---

# Step 2: Create IAM User for EKS (Create Access Keys)

Navigate to:

```text
AWS Console → IAM → Users → Create User
```

## Create IAM User

Example:

```text
Username: eks-admin-user
```

## Attach Permissions

For learning/demo purposes, attach:

```text
AdministratorAccess
```

## Create Access Keys

Navigate to:

```text
IAM User → Security Credentials → Create Access Key
```

Save:

- Access Key ID
- Secret Access Key

These credentials will be used to configure AWS CLI.

---

# Step 3: Install Prerequisites

Install the following tools on your local machine.

---

## Install AWS CLI

Verify installation:

```bash
aws --version
```

Download:

https://aws.amazon.com/cli/

---

## Install kubectl

Verify installation:

```bash
kubectl version --client
```

Download:

https://kubernetes.io/docs/tasks/tools/

---

## Install eksctl

Verify installation:

```bash
eksctl version
```

Download:

https://eksctl.io/installation/

---

# Step 4: Configure AWS CLI

Run:

```bash
aws configure
```

Provide:

```text
AWS Access Key ID
AWS Secret Access Key
Default region: ap-south-1
Output format: json
```

Verify authentication:

```bash
aws sts get-caller-identity
```

Expected output:

```json
{
  "UserId": "XXXXXXXXXXXX",
  "Account": "XXXXXXXXXXXX",
  "Arn": "arn:aws:iam::XXXXXXXXXXXX:user/eks-admin-user"
}
```

---

# Step 5: Create EKS Cluster

Create the Kubernetes cluster using eksctl.

```bash
eksctl create cluster \
  --name demo-eks-cluster \
  --region ap-south-1 \
  --nodegroup-name demo-nodes \
  --node-type t3.medium \
  --nodes 2
```

This command automatically provisions:

- EKS Control Plane
- VPC
- Subnets
- Security Groups
- IAM Roles
- EC2 Worker Nodes

Cluster creation may take 15–25 minutes.

---

# Step 6: Verify Cluster Using kubectl Commands

Verify worker nodes:

```bash
kubectl get nodes
```

Expected output:

```text
NAME                              STATUS   ROLES    AGE   VERSION
ip-192-168-xx-xx.ec2.internal     Ready    <none>   5m    v1.xx.x
```

Check cluster information:

```bash
kubectl cluster-info
```

Check namespaces:

```bash
kubectl get ns
```

Check system pods:

```bash
kubectl get pods -A
```

---

# Step 7: Deploy Sample NGINX Pod

Deploy NGINX pod:

```bash
kubectl run nginx \
  --image=nginx \
  --port=80
```

Verify pod creation:

```bash
kubectl get pods
```

Describe pod:

```bash
kubectl describe pod nginx
```

Expected status:

```text
STATUS: Running
```

---

# Step 8: Debug Issues and Expose Pod with Service Port

---

## Check Pod Logs

```bash
kubectl logs nginx
```

---

## Describe Pod for Troubleshooting

```bash
kubectl describe pod nginx
```

---

## Verify Events

```bash
kubectl get events
```

---

## Common Issues

| Issue | Possible Reason |
|---|---|
| Pod Pending | Insufficient resources |
| ImagePullBackOff | Image download issue |
| CrashLoopBackOff | Container startup failure |
| Node NotReady | Worker node issue |

---

## Expose Pod Using LoadBalancer Service

```bash
kubectl expose pod nginx \
  --type=LoadBalancer \
  --port=80
```

Verify service:

```bash
kubectl get svc
```

Expected output:

```text
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP
nginx        LoadBalancer   10.xx.xx.xx     a1b2c3.amazonaws.com
```

The `EXTERNAL-IP` is created by AWS Elastic Load Balancer (ELB).

Open the external URL in a browser to access the NGINX application.

---

# Cleanup

To avoid unnecessary AWS charges, delete the cluster after testing.

```bash
eksctl delete cluster \
  --name demo-eks-cluster \
  --region ap-south-1
```

---

# Key Learnings

- AWS EKS Cluster Creation
- IAM User & Access Key Management
- AWS CLI Configuration
- Kubernetes Cluster Verification
- Pod Deployment on EKS
- Kubernetes Debugging
- AWS LoadBalancer Integration

---

# References

- https://docs.aws.amazon.com/eks/
- https://eksctl.io/
- https://kubernetes.io/docs/

---

# Author

Amit Bikram  
DevOps Engineer | Kubernetes Enthusiast
