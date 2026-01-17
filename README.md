# Docker Swarm Web Application Deployment on AWS

## Project Overview
This project demonstrates deploying a static web application using Docker Swarm on AWS EC2 instances.
The setup includes one Manager node and two Worker nodes, orchestrated using Docker Swarm, with the Docker image stored in Amazon ECR.

---

## Architecture
- 1 × Docker Swarm Manager EC2
- 2 × Docker Swarm Worker EC2
- NGINX-based Docker container
- Amazon ECR for Docker image storage
- HTTP access via port 80

---

## PHASE 1: EC2 Instance Setup
<img width="1912" height="823" alt="image" src="https://github.com/user-attachments/assets/7e2896ba-3dc0-4f49-a039-6f094a8c5478" />


### Instances Launched
- One Manager Instance
- Two Worker Node Instances

### Security Group
- Allow HTTP (Port 80) for all EC2 instances

### Why Docker Swarm?
- Container orchestration
- High availability
- Load distribution across worker nodes

---

## PHASE 2: Docker Installation (All Nodes)

sudo yum update -y  
sudo yum install docker -y  
sudo systemctl start docker  
sudo systemctl enable docker  
sudo usermod -aG docker ec2-user  

Logout and login again.

---

## PHASE 3: Create Web Application (Manager Node)

mkdir docker-web  
cd docker-web  
nano index.html  

Paste the HTML web page content.

---

## PHASE 4: Create Dockerfile

nano Dockerfile  

FROM nginx:latest  
COPY index.html /usr/share/nginx/html/index.html  

---

## PHASE 5: Build & Test Docker Image

docker build -t docker-web:v1 .  
docker images  
docker run -d -p 80:80 docker-web:v1  

Open EC2 public IP in browser to verify.
<img width="1915" height="946" alt="image" src="https://github.com/user-attachments/assets/74055323-1fcb-4787-8be5-b066922a82fd" />

---

## PHASE 6: Create Amazon ECR Repository

- Repository Name: docker-web
- Image Tag Mutability: Mutable
- Encryption: Default

Login to ECR:

aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-south-1.amazonaws.com

---

## PHASE 7: AWS CLI Verification

aws sts get-caller-identity

IAM Role attached:
AmazonEC2ContainerRegistryFullAccess

---

## PHASE 8: Push Image to ECR

docker tag docker-web:v1 <account-id>.dkr.ecr.ap-south-1.amazonaws.com/docker-web:v1  
docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/docker-web:v1  

---

## PHASE 9: Docker Swarm Initialization

### Manager Node

docker swarm init  
docker node ls  

### Worker Nodes

docker swarm join --token <SWARM-TOKEN> <MANAGER-PRIVATE-IP>:2377  

Verify nodes from manager.

---



---

## Technologies Used
- AWS EC2
- Docker
- Docker Swarm
- Amazon ECR
- NGINX
- Linux (Amazon Linux)

---


