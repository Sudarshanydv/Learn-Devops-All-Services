# DevOps – Complete Notes 
   
## 1. Introduction to DevOps          
           
DevOps is a set of **practices, tools, and culture** that combines **Development (Dev)** and **Operations (Ops)** to deliver software faster, reliably, and efficiently.
       
### Definition               
     
DevOps is an approach that improves collaboration between development and operations teams by automating the software delivery lifecycle.

### Objectives of DevOps 

* Faster software delivery
* Continuous integration and deployment
* Improved collaboration
* High availability and scalability
* Reduced failures and rollback time

---

## 2. DevOps Lifecycle

The DevOps lifecycle consists of the following stages:

1. **Plan** – Requirement gathering and planning
2. **Code** – Writing application code
3. **Build** – Compiling and building artifacts
4. **Test** – Automated and manual testing
5. **Release** – Preparing application for deployment
6. **Deploy** – Deploying application to servers/cloud
7. **Operate** – Monitoring and maintaining systems
8. **Monitor** – Logging, performance monitoring, alerts

---

## 3. CI/CD (Continuous Integration & Continuous Deployment)

### Continuous Integration (CI)

CI is a process where developers frequently merge code changes into a shared repository, followed by automated builds and tests.

**Benefits:**

* Early bug detection
* Improved code quality
* Faster development

### Continuous Deployment (CD)

CD automatically deploys tested code into production.

**Benefits:**

* Faster releases
* Reduced manual errors
* Reliable deployments

---

## 4. Version Control System (Git)

### Git

Git is a distributed version control system used to track changes in source code.

### Common Git Commands

```bash
git init
git clone <repo_url>
git status
git add .
git commit -m "message"
git push origin main
git pull
git branch
git checkout
```

---

## 5. Build Tools

Build tools are used to automate the process of compiling source code.

### Popular Build Tools

* Maven
* Gradle
* Ant

---

## 6. Configuration Management Tools

These tools help manage server configuration automatically.

### Examples

* Ansible
* Chef
* Puppet

### Ansible Features

* Agentless
* Uses YAML
* Simple and powerful

---

## 7. Containerization (Docker)

### Docker

Docker is a container platform that packages applications with dependencies.

### Advantages

* Lightweight
* Portable
* Consistent environments

### Common Docker Commands

```bash
docker --version
docker pull nginx
docker images
docker run -d -p 80:80 nginx
docker ps
docker stop <container_id>
```

---

## 8. Container Orchestration (Kubernetes)

Kubernetes is used to manage, scale, and deploy containers.

### Key Components

* Pod
* Node
* Cluster
* Service
* Deployment

---

## 9. Cloud Computing (AWS Focused)

### Cloud Service Models

* IaaS – Infrastructure as a Service
* PaaS – Platform as a Service
* SaaS – Software as a Service

### Common AWS Services

* EC2 – Virtual servers
* S3 – Object storage
* IAM – Identity management
* VPC – Virtual network
* CloudWatch – Monitoring

---

## 10. Monitoring & Logging

Monitoring ensures system health and performance.

### Tools

* Prometheus
* Grafana
* CloudWatch
* ELK Stack

---

## 11. DevOps Benefits

* Faster time to market
* Improved collaboration
* High reliability
* Continuous feedback
* Automation

---

## 12. Conclusion

DevOps plays a critical role in modern software development by enabling automation, collaboration, and continuous delivery. Using tools like Git, Jenkins, Docker, Kubernetes, and AWS, organizations can build scalable and reliable systems.

---

**End of DevOps Notes**
