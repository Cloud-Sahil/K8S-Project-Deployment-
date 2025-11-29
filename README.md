# Kuberneties Project -

## 1. EKS Cluster (Elastic Kubernetes Service)

. Create EKS Cluster

. Create IAM Role

. Security Group

. Create EKS Cluster

. Cluster Select

. Compute

. Add Node

. Create IAM Role

. Add Policy (AmazonEKSWorkerNodeMinimalPolicy)

. Add EC2 Instance Type

. Create

## 2. RDS (Realtional Database Service)

. Create RDS 

. Full configuration

. MariaDB

. Templates -- Sandbox

. Master username -- EX. (linux)

. Master password --  EX. (redhat123)

. VPC security group -- EX. (launch-wizard-1)

. Create Database

## 3. EC2 (Elastic Compute Cloud)

. Launch instance

. Instance type -- EX. (c7i.xlarge)

. Security groups -- EX. (launch-wizard-1)

. Storage -- EX. (20GB)

. Launch instance

## 4. Commands =

### 1. Switch to root user
```sh
sudo -i
```


### 2. Update the instance
```sh
apt update
```
### 3. Docker install 
```sh
apt install docker.io 
```
### 4. Mysql install 
```sh
apt install mysql-client -y
```
### 5. Install kubectl 
#### Download the latest release with the command
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

#### Validate the binary
```sh
 curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```

#### Validate the kubectl binary against the checksum file 
```sh
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

#### Install kubectl
```sh
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

#### Note: If you do not have root access on the target system, you can still install kubectl to the ~/.local/bin directory:
```sh
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
```
```sh
kubectl version --client
```

### 6. Install AWS CLI on Ubuntu

#### Download the aws cli bundle using below command
```sh
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

### 7. Configure AWS CLI
#### Access key ID
#### Secret access key 
