# Kuberneties StudentsApp Project -

---

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

---

## 2. RDS (Realtional Database Service)

. Create RDS 

. Full configuration

. MariaDB

. Templates -- Sandbox

. Master username -- EX. (linux)

. Master password --  EX. (redhat123)

. VPC security group -- EX. (launch-wizard-1)

. Create Database

---

## 3. EC2 (Elastic Compute Cloud)

. Launch instance

. Instance type -- EX. (c7i.xlarge)

. Security groups -- EX. (launch-wizard-1)

. Storage -- EX. (20GB)

. Launch instance

---


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
```sh
aws configure
```

#### Access key ID
#### Secret access key 

### 8. Log In Into EKS cluster
```sh
aws eks update-kubeconfig --name (**EKS Cluster Name**)
```
Ex. aws eks update-kubeconfig --name cluster

### 9. Mysql-client Database 
```sh
mysql -h (endpoint) -u (username) -p
```
```sh
Enter password (password)
```
##### RDS Database Endpoint copy & paste
##### Example: mysql -h database-1.ca9eie2mihs7.us-east-1.rds.amazonaws.com -u linux -p
##### Example: redhat123

```sh
CREATE DATABASE student_db;
```
```sh
GRANT ALL PRIVILEGES ON springbackend.* TO 'username'@'localhost' IDENTIFIED BY 'password';
```
```sh
USE student_db;
```
```sh
CREATE TABLE `students` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  `course` varchar(255) DEFAULT NULL,
  `student_class` varchar(255) DEFAULT NULL,
  `percentage` double DEFAULT NULL,
  `branch` varchar(255) DEFAULT NULL,
  `mobile_number` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=80 DEFAULT CHARSET=latin1 COLLATE=latin1_swedish_ci;
```
```sh
show databases;
```
```sh
exit;
```

### 10. Clone the GitHub Repository
```sh
git clone <GitHub_Repository_Link>
```
##### Example: git clone https://github.com/username/student-registration.git

### 11. Backend

```sh
cd <GitHub_Repository_Name>/backend

```
```sh
cp src/main/resources/application.properties .
```
#### Write application.properties =
```sh
nano application.properties
```
```sh
    server.port=8080
    spring.datasource.url=jdbc:mariadb://(database endpoint paste):3306/student_db 
    spring.datasource.username=(database username)
    spring.datasource.password=(database password)
    spring.jpa.hibernate.ddl-auto=update
    spring.jpa.show-sql=true
# Then ctrl s+x
```
#### Write Backend Dockerfile =
```sh
nano dockerfile
```
```sh
    FROM maven:3.8.3-openjdk-17
    COPY . /opt
    WORKDIR /opt
    RUN rm -rf src/main/resources/application.properties
    RUN cp -rf application.properties src/main/resources
    RUN mvn clean package
    WORKDIR /opt/target
    EXPOSE 8080
    CMD ["java","-jar","student-registration-backend-0.0.1-SNAPSHOT.jar"]
```
#### Docker Build
```sh
docker build . -t backend:v1
```
#### Docker Tag (DockerHub)
```sh
docker tag backend:v1 (docker hub id)/backend:v1
```
##### Ex. docker tag backend:v1 sahil0117/backend:v1

#### Docker images check
```sh
docker images
```
#### Docker Login
```sh
docker login -u (docker hub id)
```
##### Ex. docker login -u sahil0117

#### docker push
```sh
docker push (dockerhub id)/backend:v1
```
##### Ex. docker push sahil0117/backend:v1

#### Write Backend Pod Yamlfile
```sh
nano backend-pod.yaml
```
```sh
apiVersion: v1
kind: Pod
metadata:
  name: backend-pod
  labels:
    app: backend
spec:
  containers:
    - name: backend
      image: sahil0117/backend:v1
      ports:
        - containerPort: 8080
```
#### Apply backend-pod yamlfile
```sh
kubectl apply -f backend-pod.yaml
```
#### Write Backend Service Yamlfile
```sh
nano backend-svc.yaml
```
```sh
apiVersion: v1
kind: Service
metadata:
  name: backend-svc
spec:
  type: LoadBalancer
  selector:
    app: backend
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```
#### Apply backend-svc yamlfile
```sh
kubectl apply -f backend-svc.yaml
```
#### Check Pods
```sh
kubectl get pods
```
#### Check Svc
```sh
kubectl get svc
```
## 11. Frontend
```sh
cd ../frontend/
```
#### Write env file
```sh
nano .env
```
```sh
VITE_API_URL = "http://(backend-svc = LoadBalancer = IP Paste):8080/api"
```
##### Ex. VITE_API_URL = "http://aa597014bd33d4ef1a2d43710fac6fc3-1747006653.us-east-1.elb.amazonaws.com:8080/api"

#### Write Frontend Dockerfile =
```sh
nano dockerfile
```
```sh
    FROM node:25-alpine3.21
    COPY . /opt
    WORKDIR /opt
    RUN apk update 
    RUN apk add apache2
    RUN npm install
    RUN npm run build
    RUN cp -rf dist/* /var/www/localhost/htdocs/
    EXPOSE 80
    CMD ["httpd","-D","FOREGROUND"]
```
#### Docker Build
```sh
docker build . -t frontend:v1
```
#### Write Frontend Deploy yamlfile
```sh
nano frontend-deploy.yaml
```
```sh
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deploy
spec: 
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      name: frontend
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: sahil0117/frontend:v1
          ports:
            - containerPort: 80
```
#### Apply Frontend Deploy yamlfile
```sh
kubectl apply -f frontend-deploy.yaml
```
#### Check Pods
```sh
kubectl get pods
```
#### Write Frontend Service Yamlfile
```sh
nano frontend-svc.yaml
```
```sh
apiVersion: v1
kind: Service
metadata:
  name: frontend-svc
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
#### Apply frontend-svc yamlfile
```sh
kubectl apply -f frontend-svc.yaml
```
#### Check Svc
```sh
kubectl get svc
```
