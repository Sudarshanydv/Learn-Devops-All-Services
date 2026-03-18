# 🐳 Docker - Complete Guide

## 📌 Introduction
Docker is a containerization platform used to build, ship, and run applications in isolated environments called containers. It helps developers package applications with all dependencies, ensuring consistency across different environments.
  
---    
     
## 🎯 Why Docker?     
- Eliminates "works on my machine" problem 
- Lightweight compared to virtual machines     
- Fast deployment and scaling  
- Easy to manage dependencies 
- Improves DevOps workflow  

---

## 🧱 Docker Architecture  

### 1. Docker Client   
- Command line interface (CLI)
- Used to interact with Docker  
 
### 2. Docker Daemon 
- Runs in background 
- Manages containers, images, networks  
  
### 3. Docker Images
- Read-only templates used to create containers
- Example: Ubuntu, Nginx 
 
### 4. Docker Containers
- Running instances of images
- Lightweight and portable 

### 5. Docker Registry 
- Stores Docker images
- Example: Docker Hub

---

## ⚙️ Key Docker Concepts

### 🔹 Image
Blueprint of application 

### 🔹 Container
Running instance of image

### 🔹 Dockerfile
Script with instructions to build image

### 🔹 Volume
Used for persistent storage

### 🔹 Network
Allows containers to communicate

---

## 🛠️ Docker Installation (Linux Example)
 
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

---

## 📦 Basic Docker Commands

```bash
# Check version
docker --version

# Pull image
docker pull nginx

# Run container
docker run -d -p 80:80 nginx

# List containers
docker ps

# Stop container
docker stop <container_id>

# Remove container
docker rm <container_id>

# Remove image
docker rmi <image_id>
```

---

## 📝 Dockerfile Example

```Dockerfile
# Base image
FROM node:14

# Set working directory
WORKDIR /app

# Copy files
COPY . .

# Install dependencies
RUN npm install

# Run application
CMD ["npm", "start"]
```

---

## 🚀 Build and Run Image

```bash
# Build image
docker build -t myapp .

# Run container
docker run -d -p 3000:3000 myapp
```

---

## 📂 Docker Volumes

```bash
# Create volume
docker volume create myvolume

# Use volume
docker run -v myvolume:/data nginx
```

---

## 🌐 Docker Networking

```bash
# Create network
docker network create mynetwork

# Run container in network
docker run --network=mynetwork nginx
```

---

## 🔄 Docker Compose

Used to manage multi-container applications.

### docker-compose.yml

```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"

  app:
    build: .
    ports:
      - "3000:3000"
```

### Run Compose

```bash
docker-compose up -d
```

---

## ✅ Advantages of Docker
- Fast and lightweight
- Portable across environments
- Easy scaling
- Efficient resource usage

---

## ❌ Disadvantages of Docker
- Less secure than VMs
- Requires proper configuration
- Learning curve for beginners

---

## 📌 Use Cases
- CI/CD pipelines
- Microservices architecture
- Cloud deployments
- Dev/Test environments

---

## 🧾 Conclusion
Docker simplifies application deployment by packaging code and dependencies into containers. It is an essential tool for DevOps engineers and modern software development.
