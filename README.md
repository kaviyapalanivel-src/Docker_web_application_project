# Docker Swarm Web Application Deployment on AWS

## Project Overview
This project demonstrates deploying a static web application using **Docker Swarm** on **AWS EC2** instances.  
The setup includes one **Manager node** and two **Worker nodes**, orchestrated using Docker Swarm, with the Docker image stored in **Amazon ECR**.

---

## Architecture
- 1 × Docker Swarm Manager EC2
- 2 × Docker Swarm Worker EC2
- NGINX-based Docker container
- Amazon ECR for Docker image storage
- HTTP access via port 80

---

## PHASE 1: EC2 Instance Setup
<img width="1911" height="831" alt="image" src="https://github.com/user-attachments/assets/e59ebde7-6d5f-448a-9ded-171a531c00a6" />
### Instances Launched
- 1 Manager Instance
- 2 Worker Node Instances

### Security Group
- Allow **HTTP (Port 80)** from anywhere
- Allow **Docker Swarm ports** internally (optional best practice)

### Why Docker Swarm?
- Container orchestration
- High availability
- Load distribution across worker nodes

---

## PHASE 2: Docker Installation (All Nodes)

```bash
sudo yum update -y
sudo yum install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user
exit
Login again after exit.

## PHASE 3: Create Web Application (Manager Node)
bash
Copy code
mkdir docker-web
cd docker-web
nano index.html
Paste the full HTML code (Space Lab web page).

## PHASE 4: Create Dockerfile
bash
Copy code
nano Dockerfile
Dockerfile
Copy code
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
PHASE 5: Build & Test Docker Image
bash
Copy code
docker build -t docker-web:v1 .
docker images
docker run -d -p 80:80 docker-web:v1
docker ps
➡ Copy EC2 Public IP and open in browser

<img width="1915" height="946" alt="image" src="https://github.com/user-attachments/assets/92e3ebc7-fbf8-456d-9534-b8a9c0b021f3" />
✔ Web page loads successfully

## PHASE 6: Create Amazon ECR Repository
Repository name: docker-web

Image tag mutability: Mutable

Encryption: Default

Login to ECR:

bash
Copy code
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-south-1.amazonaws.com
## PHASE 7: AWS CLI Verification
bash
Copy code
aws sts get-caller-identity
IAM Role attached to EC2:

AmazonEC2ContainerRegistryFullAccess

## PHASE 8: Push Image to ECR
bash
Copy code
docker tag docker-web:v1 <account-id>.dkr.ecr.ap-south-1.amazonaws.com/docker-web:v1
docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/docker-web:v1
PHASE 9: Docker Swarm Initialization
Manager Node
bash
Copy code
docker swarm init
docker node ls
Worker Nodes
bash
Copy code
docker swarm join --token <SWARM-TOKEN> <MANAGER-PRIVATE-IP>:2377
Verify from manager:

bash
Copy code
docker node ls
Result
Web application container runs successfully

Docker Swarm cluster active

Image stored securely in Amazon ECR

Technologies Used
AWS EC2

Docker & Docker Swarm

Amazon ECR

NGINX

Linux (Amazon Linux)
