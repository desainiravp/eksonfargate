# 🚀 EKS with Fargate Setup Guide

---
**Step 1: Create EKS Management Host in AWS**

1. **Launch a new Ubuntu EC2 instance**
   - Instance type: `t2.micro`
   - OS: Ubuntu 20.04+ recommended

2. **Connect to the EC2 instance**
```bash
ssh -i <your-key.pem> ubuntu@<ec2-public-ip>
```

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```
3.Install AWS CLI latest version using below commands
```bash
sudo apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```
4.Install eksctl using below commands
```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

**Step -2 :Create IAM role & attach to EKS Management Host**
Create New Role using IAM service ( Select Usecase - ec2 )

Add below permissions for the role

Administrator - acces
Enter Role Name (eks-deploy)

Attach created role to EKS Management Host (Select EC2 => Click on Security => Modify IAM Role => attach IAM role we have created)


**Step - 3 : Create EKS Cluster using eksctl**
```bash
eksctl create cluster \
  --name my-fargate-cluster \
  --region us-east-1 \
  --without-nodegroup
```
**Step - 4 : Create fargate prfile**
```bash
eksctl create fargateprofile \
  --cluster my-fargate-cluster \
  --region us-east-1 \
  --name fargate-default \
  --namespace default
```

**Step -5 : For visibility of Pods on Cluster**
Select arn
Add policy EksClusterAdmin

Step -6 : Deploy Sample Application
```bash
kubectl run nginx-fargate --image=nginx --restart=Never --namespace default
```
**Step -7 : cleanup Cluster**
```bash
eksctl delete cluster --name my-fargate-cluster --region us-east-1
```
