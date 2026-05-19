Following the steps in User_guide.md

# Step 3: Install Prerequisites

Install the following tools on your local machine.
- AWS CLI
- eksctl
- kubectl

For windows system open PowerShell or command Prompt 
Use the following commands

- For AWS CLI Install
  --------------------
```bash
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
```
- For eksctl & kubectl
  ------------------- 
Download:
```bash
curl.exe -O https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_windows_amd64.zip
```
```bash
curl.exe -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.35.3/2026-04-08/bin/windows/amd64/kubectl.exe 
```

Install
- Unzip eksctl_windows_amd64.zip manually
- Create a new folder under c:\ named tool(you can have your own folder)
- Place kubectl.exe and eksctl.exe in tool folder
- By pressing windows search bar open (Edit the system Environment Variable)
  - Select and open 'environment variables'
  - From System variable section select 'Path' and press edit
  - Add a new line with 'c:\tool\ 
  - Press ok to save and exit

Check if all three is installed
```bash
eksctl version
```
```bash
kubectl version --client
```
```bash
aws --version
```
---

# Step 4: Configure AWS CLI

Run:
```bash
aws configure
```
Provide:

AWS Access Key ID  ( Check the downloaded .csv file, which you have downloaded during access key creation in AWS console)
AWS Secret Access Key
Default region: ap-south-1
Output format: json

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
# Step 5: Create EKS Cluster

Create the Kubernetes cluster using eksctl.
```bash
eksctl create cluster --name demo-eks-cluster --region ap-south-1 --nodegroup-name demo-nodes --node-type t3.medium --nodes 2
```
Note: If you are using free version of AWS, then use t3.micro instead of t3.medium (But you may not able to deploy any new pod because of resource limitation). For now go with t3.micro

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
kubectl run nginx  --image=nginx --port=80
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

Note: In case pod is in pending state delete delete metrics-server deployment (Just for your testing)
      # kubectl delete deployment metrics-server -n kube-system
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
kubectl expose pod nginx --type=LoadBalancer --port=80
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

# Cleanup - Important

To avoid unnecessary AWS charges, delete the cluster after testing.
```bash
eksctl delete cluster --name demo-eks-cluster --region ap-south-1
```

---

# Author
@amitbikram011

Amit Bikram  
DevOps Engineer | Kubernetes Enthusiast
