# UNIT-V: Advanced DevOps - Docker Compose, Kubernetes & Configuration Management

---

## 📚 Table of Contents
1. [Docker Compose](#docker-compose)
2. [Deploying to Azure Container Instances](#azure-container-instances)
3. [Kubernetes Installation & Deployment](#kubernetes)
4. [Configuration Management Tools](#configuration-management)
5. [DevOps Best Practices](#best-practices)

---

# 🐳 1. Getting Started with Docker Compose {#docker-compose}

## 1.1 What is Docker Compose?

**Definition:**
Docker Compose is a **declarative tool** for defining and managing **multi-container Docker applications** through a single YAML configuration file.

**Key Characteristics:**
- Orchestrates multiple containers as a single application
- Uses YAML syntax for configuration (`docker-compose.yml`)
- Simplifies complex deployment workflows
- Manages dependencies between services
- Provides networking and volume management

**Why Docker Compose?**
- **Problem:** Running multiple containers manually is complex and error-prone
- **Solution:** Define all services in one file, start with one command
- **Use Cases:** 
  - Web app + Database + Cache
  - Microservices architecture
  - Development environments
  - Testing and CI/CD pipelines

---

## 1.2 Core Concepts

### Services
- **Definition:** A service is a container definition in docker-compose
- Each service runs one image
- Can specify build context, ports, volumes, environment variables
- Can scale services independently

### Networks
- Docker Compose automatically creates a network for all services
- Services can communicate using service names as hostnames
- Can define custom networks for isolation

### Volumes
- Persistent data storage
- Shared between containers
- Survives container restarts

---

## 1.3 Docker Compose File Structure

**Complete Example:**

```yaml
version: '3.8'  # Compose file version

services:
  # Frontend Service
  web:
    build: ./frontend           # Build from Dockerfile
    container_name: web-app
    ports:
      - "80:80"                 # Host:Container
      - "443:443"
    environment:
      - NODE_ENV=production
      - API_URL=http://api:3000
    depends_on:
      - api                     # Start api first
    volumes:
      - ./logs:/var/log/nginx
    networks:
      - frontend-network
    restart: always             # Auto-restart on failure

  # Backend API Service
  api:
    build: ./backend
    container_name: api-server
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=database
      - DB_PORT=5432
      - DB_USER=admin
      - DB_PASSWORD=secret123
    depends_on:
      - database
    volumes:
      - ./api-data:/app/data
    networks:
      - frontend-network
      - backend-network
    restart: unless-stopped

  # Database Service
  database:
    image: postgres:14-alpine
    container_name: postgres-db
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret123
      POSTGRES_DB: myapp
    volumes:
      - db-data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - backend-network
    restart: always
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 30s
      timeout: 10s
      retries: 5

  # Cache Service
  redis:
    image: redis:7-alpine
    container_name: redis-cache
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - backend-network
    restart: always
    command: redis-server --appendonly yes

# Named volumes
volumes:
  db-data:
    driver: local
  redis-data:
    driver: local

# Custom networks
networks:
  frontend-network:
    driver: bridge
  backend-network:
    driver: bridge
```

---

## 1.4 Essential Docker Compose Commands

### Basic Operations
```bash
# Start all services (detached mode)
docker compose up -d

# Start with rebuild
docker compose up --build -d

# View running services
docker compose ps

# View logs
docker compose logs -f          # Follow logs
docker compose logs web         # Specific service
docker compose logs --tail=100  # Last 100 lines

# Stop all services
docker compose stop

# Stop and remove containers, networks
docker compose down

# Stop and remove everything (including volumes)
docker compose down -v

# Restart specific service
docker compose restart web

# Scale a service
docker compose up -d --scale web=3

# Execute command in running container
docker compose exec web bash
docker compose exec database psql -U admin

# View resource usage
docker compose top
```

### Advanced Commands
```bash
# Validate compose file
docker compose config

# Pull all images
docker compose pull

# Build all services
docker compose build

# Build specific service
docker compose build web

# Build without cache
docker compose build --no-cache

# View service ports
docker compose port web 80

# Pause/Unpause services
docker compose pause
docker compose unpause

# Remove stopped containers
docker compose rm

# View events
docker compose events
```

---

## 1.5 Real-World Example: Full-Stack Application

**Project Structure:**
```
myapp/
├── docker-compose.yml
├── frontend/
│   ├── Dockerfile
│   └── src/
├── backend/
│   ├── Dockerfile
│   └── app/
├── database/
│   └── init.sql
└── nginx/
    └── nginx.conf
```

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - static-content:/usr/share/nginx/html
    depends_on:
      - frontend
      - backend
    networks:
      - app-network

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      args:
        NODE_VERSION: 18
    environment:
      - REACT_APP_API_URL=http://backend:5000
    volumes:
      - static-content:/app/build
    networks:
      - app-network

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/appdb
      - REDIS_URL=redis://cache:6379
      - SECRET_KEY=${SECRET_KEY}
    depends_on:
      db:
        condition: service_healthy
      cache:
        condition: service_started
    networks:
      - app-network
    restart: on-failure
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M

  db:
    image: postgres:14
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: appdb
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./database/init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      timeout: 5s
      retries: 5

  cache:
    image: redis:7-alpine
    networks:
      - app-network
    volumes:
      - redis-data:/data

volumes:
  postgres-data:
  redis-data:
  static-content:

networks:
  app-network:
    driver: bridge
```

---

## 1.6 Best Practices for Docker Compose

### 1. Environment Variables
```yaml
# Use .env file
services:
  web:
    environment:
      - DB_HOST=${DB_HOST:-localhost}
      - DB_PORT=${DB_PORT:-5432}
```

### 2. Health Checks
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost/health"]
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 40s
```

### 3. Resource Limits
```yaml
deploy:
  resources:
    limits:
      cpus: '1.0'
      memory: 1G
    reservations:
      cpus: '0.5'
      memory: 512M
```

### 4. Proper Dependency Management
```yaml
depends_on:
  db:
    condition: service_healthy
  redis:
    condition: service_started
```

### 5. Use Named Volumes
```yaml
volumes:
  - data-volume:/app/data  # Named
  - ./config:/app/config:ro  # Bind mount (read-only)
```

---

## 1.7 Exam Important Points ⭐

**Key Concepts:**
- Docker Compose simplifies multi-container management
- Uses declarative YAML configuration
- Automatic network creation between services
- `docker compose up` vs `docker-compose up` (v1 vs v2)
- Services communicate using service names
- Volumes persist data across container restarts

**Common Exam Questions:**
1. Difference between `docker run` and `docker compose up`
2. How to scale services in Docker Compose
3. Networking in Docker Compose
4. Volume management and persistence
5. Dependency management with `depends_on`
6. Health checks and restart policies

---

# ☁️ 2. Deploying Docker Compose to Azure Container Instances (ACI) {#azure-container-instances}

## 2.1 What is Azure Container Instances (ACI)?

**Definition:**
Azure Container Instances (ACI) is a **serverless container platform** that allows running Docker containers in Azure **without managing virtual machines or orchestrators**.

**Key Features:**
- **Serverless:** No infrastructure management
- **Fast startup:** Containers start in seconds
- **Per-second billing:** Pay only for exact runtime
- **Flexible sizing:** Choose CPU and memory per container
- **Public & Private networking:** Assign public IPs or use VNet
- **Persistent storage:** Mount Azure Files shares
- **Container groups:** Run multi-container pods

**Use Cases:**
- CI/CD build agents
- Batch processing jobs
- Event-driven applications
- Task automation
- Development/testing environments
- Microservices without orchestration overhead

---

## 2.2 ACI Architecture

```
┌─────────────────────────────────────────┐
│         Azure Container Instance        │
│                                         │
│  ┌─────────────┐    ┌─────────────┐   │
│  │  Container  │    │  Container  │   │
│  │   (Web)     │◄──►│   (Sidecar) │   │
│  └─────────────┘    └─────────────┘   │
│         │                  │           │
│         ▼                  ▼           │
│  ┌──────────────────────────────┐     │
│  │      Shared Resources        │     │
│  │  • Network                   │     │
│  │  • Volumes                   │     │
│  │  • Lifecycle                 │     │
│  └──────────────────────────────┘     │
└─────────────────────────────────────────┘
         │
         ▼
   Azure Network
```

---

## 2.3 Prerequisites

### Install Required Tools
```bash
# Install Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Verify installation
az --version

# Install Docker CLI Azure Integration
curl -L https://aka.ms/InstallAzureCLIDeb | sudo bash

# Login to Azure
az login

# Set subscription (if multiple)
az account set --subscription "Your Subscription Name"

# Verify login
az account show
```

---

## 2.4 Step-by-Step Deployment Process

### Step 1: Prepare Docker Compose File

**docker-compose.yml for ACI:**
```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    environment:
      - NGINX_HOST=example.com
      - NGINX_PORT=80
    volumes:
      - website-data:/usr/share/nginx/html
    
  api:
    image: myregistry.azurecr.io/myapi:latest
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - API_KEY=${API_KEY}
    depends_on:
      - web

volumes:
  website-data:
```

### Step 2: Create Azure Resources
```bash
# Set variables
RESOURCE_GROUP="myResourceGroup"
LOCATION="eastus"
CONTAINER_NAME="myapp-container"

# Create resource group
az group create \
  --name $RESOURCE_GROUP \
  --location $LOCATION

# Verify creation
az group show --name $RESOURCE_GROUP
```

### Step 3: Create Azure Context for Docker
```bash
# Create ACI context
docker context create aci myacicontext \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION

# List contexts
docker context ls

# Switch to ACI context
docker context use myacicontext

# Verify current context
docker context show
```

### Step 4: Deploy to ACI
```bash
# Deploy using docker compose
docker compose up

# Or specify file explicitly
docker compose -f docker-compose.yml up

# Check deployment status
docker compose ps

# View logs
docker compose logs
docker compose logs -f web
```

### Step 5: Verify Deployment
```bash
# List containers in resource group
az container list \
  --resource-group $RESOURCE_GROUP \
  --output table

# Get container details
az container show \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP

# Get public IP
az container show \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP \
  --query ipAddress.ip \
  --output tsv

# Check container status
az container show \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP \
  --query instanceView.state
```

### Step 6: Access the Application
```bash
# Get FQDN if configured
az container show \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP \
  --query ipAddress.fqdn

# Access via curl
curl http://<public-ip>

# Or open in browser
open http://<public-ip>
```

### Step 7: Monitor and Debug
```bash
# View container logs
az container logs \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP

# Follow logs
az container logs \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP \
  --follow

# Attach to container
az container attach \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP

# Execute commands in container
az container exec \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP \
  --exec-command "/bin/bash"
```

### Step 8: Update Deployment
```bash
# Update application
docker compose up

# Restart containers
az container restart \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP
```

### Step 9: Clean Up
```bash
# Stop and remove containers
docker compose down

# Switch back to default context
docker context use default

# Delete specific container
az container delete \
  --name $CONTAINER_NAME \
  --resource-group $RESOURCE_GROUP \
  --yes

# Delete entire resource group
az group delete \
  --name $RESOURCE_GROUP \
  --yes \
  --no-wait
```

---

## 2.5 Advanced ACI Features

### Container Groups
```bash
# Deploy container group with YAML
az container create \
  --resource-group $RESOURCE_GROUP \
  --file container-group.yaml
```

**container-group.yaml:**
```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup
properties:
  containers:
  - name: web
    properties:
      image: nginx
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  
  - name: sidecar
    properties:
      image: busybox
      command: ["/bin/sh", "-c", "while true; do echo hello; sleep 10; done"]
      resources:
        requests:
          cpu: 0.5
          memoryInGB: 0.5
  
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
  restartPolicy: Always
```

### Environment Variables and Secrets
```bash
# Deploy with environment variables
az container create \
  --resource-group $RESOURCE_GROUP \
  --name mycontainer \
  --image myimage:latest \
  --environment-variables \
    'API_KEY'='mykey123' \
    'ENVIRONMENT'='production' \
  --secure-environment-variables \
    'DB_PASSWORD'='supersecret'
```

### Mounting Azure Files
```bash
# Create storage account
az storage account create \
  --resource-group $RESOURCE_GROUP \
  --name mystorageaccount \
  --sku Standard_LRS

# Create file share
az storage share create \
  --name myfileshare \
  --account-name mystorageaccount

# Get storage key
STORAGE_KEY=$(az storage account keys list \
  --resource-group $RESOURCE_GROUP \
  --account-name mystorageaccount \
  --query "[0].value" -o tsv)

# Deploy container with mounted volume
az container create \
  --resource-group $RESOURCE_GROUP \
  --name mycontainer \
  --image nginx \
  --azure-file-volume-account-name mystorageaccount \
  --azure-file-volume-account-key $STORAGE_KEY \
  --azure-file-volume-share-name myfileshare \
  --azure-file-volume-mount-path /mnt/azure
```

### Using Azure Container Registry (ACR)
```bash
# Create ACR
az acr create \
  --resource-group $RESOURCE_GROUP \
  --name myregistry \
  --sku Basic

# Login to ACR
az acr login --name myregistry

# Build and push image
docker build -t myregistry.azurecr.io/myapp:v1 .
docker push myregistry.azurecr.io/myapp:v1

# Deploy from ACR
az container create \
  --resource-group $RESOURCE_GROUP \
  --name mycontainer \
  --image myregistry.azurecr.io/myapp:v1 \
  --registry-login-server myregistry.azurecr.io \
  --registry-username $(az acr credential show --name myregistry --query username -o tsv) \
  --registry-password $(az acr credential show --name myregistry --query "passwords[0].value" -o tsv)
```

---

## 2.6 ACI vs Other Services

| Feature | ACI | AKS | Azure App Service | VMs |
|---------|-----|-----|-------------------|-----|
| **Setup Time** | Seconds | Minutes | Minutes | Minutes |
| **Management** | Minimal | Complex | Moderate | High |
| **Scaling** | Manual | Auto | Auto | Manual |
| **Pricing** | Per-second | Per-node | Per-instance | Per-hour |
| **Orchestration** | Basic | Advanced | N/A | Manual |
| **Best For** | Simple tasks | Microservices | Web apps | Full control |

---

## 2.7 Exam Important Points ⭐

**Key Concepts:**
- ACI provides serverless container hosting
- No VM or orchestrator management required
- Pay-per-second billing model
- Container groups support multi-container deployments
- Integration with Docker Compose CLI
- Supports both public and private networking
- Can mount Azure Files for persistent storage

**Common Exam Questions:**
1. What is the difference between ACI and AKS?
2. How to deploy Docker Compose to ACI?
3. Container group configuration and use cases
4. Pricing model of ACI
5. How to mount persistent storage in ACI?
6. Security best practices for ACI deployments

**Commands to Remember:**
```bash
az container create
az container list
az container show
az container logs
az container delete
docker context create aci
docker compose up (in ACI context)
```

---

# ☸️ 3. Installing Kubernetes and Application Deployment {#kubernetes}

## 3.1 What is Kubernetes?

**Definition:**
Kubernetes (K8s) is an **open-source container orchestration platform** that automates deployment, scaling, and management of containerized applications across clusters of hosts.

**Origin:**
- Developed by Google (based on Borg)
- Open-sourced in 2014
- Now maintained by CNCF (Cloud Native Computing Foundation)

**Why Kubernetes?**
- **Automatic scaling:** Scale up/down based on demand
- **Self-healing:** Restart failed containers automatically
- **Load balancing:** Distribute traffic across containers
- **Rolling updates:** Update applications without downtime
- **Service discovery:** Automatic DNS and networking
- **Secret management:** Secure configuration handling
- **Multi-cloud:** Run anywhere (cloud, on-premise, hybrid)

---

## 3.2 Kubernetes Architecture

### Control Plane Components
```
┌──────────────────────────────────────────────┐
│           CONTROL PLANE (Master)             │
├──────────────────────────────────────────────┤
│  ┌────────────┐  ┌──────────────┐           │
│  │ API Server │  │  Scheduler   │           │
│  └─────┬──────┘  └──────┬───────┘           │
│        │                │                    │
│  ┌─────▼─────────┐  ┌──▼──────────────┐    │
│  │ etcd (Storage)│  │ Controller Mgr  │    │
│  └───────────────┘  └─────────────────┘    │
└──────────────────────────────────────────────┘
                    │
          ┌─────────┼─────────┐
          │         │         │
    ┌─────▼────┐ ┌──▼─────┐ ┌▼────────┐
    │  NODE 1  │ │ NODE 2 │ │ NODE 3  │
    └──────────┘ └────────┘ └─────────┘
```

**1. API Server (`kube-apiserver`)**
- Frontend for Kubernetes control plane
- All communication goes through API server
- RESTful interface
- Authenticates and validates requests

**2. etcd**
- Distributed key-value store
- Stores all cluster data
- Source of truth for cluster state
- Highly available and consistent

**3. Scheduler (`kube-scheduler`)**
- Assigns pods to nodes
- Considers resource requirements
- Applies constraints and policies
- Optimizes resource utilization

**4. Controller Manager (`kube-controller-manager`)**
- Runs controller processes
- Node controller: Monitors node health
- Replication controller: Maintains pod count
- Endpoints controller: Populates endpoints
- Service account controller: Creates default accounts

**5. Cloud Controller Manager**
- Manages cloud-specific controllers
- Node management
- Route management
- Service (load balancer) management

### Worker Node Components
```
┌─────────────────────────────────────┐
│           WORKER NODE               │
├─────────────────────────────────────┤
│  ┌────────────────────────────┐    │
│  │       kubelet              │    │
│  │  (Node agent)              │    │
│  └────────┬───────────────────┘    │
│           │                         │
│  ┌────────▼───────────────────┐    │
│  │   Container Runtime        │    │
│  │   (Docker/containerd)      │    │
│  └────────┬───────────────────┘    │
│           │                         │
│  ┌────────▼───────────────────┐    │
│  │        Pods                │    │
│  │  ┌──────┐  ┌──────┐       │    │
│  │  │ C1   │  │ C2   │       │    │
│  │  └──────┘  └──────┘       │    │
│  └────────────────────────────┘    │
│                                     │
│  ┌────────────────────────────┐    │
│  │      kube-proxy            │    │
│  │  (Network proxy)           │    │
│  └────────────────────────────┘    │
└─────────────────────────────────────┘
```

**1. kubelet**
- Agent running on each node
- Ensures containers are running in pods
- Reports node status to API server
- Mounts volumes
- Downloads secrets

**2. Container Runtime**
- Software for running containers
- Options: Docker, containerd, CRI-O
- Pulls images from registries
- Runs containers

**3. kube-proxy**
- Network proxy on each node
- Maintains network rules
- Enables service abstraction
- Handles load balancing

---

## 3.3 Core Kubernetes Objects

### 1. Pod
**Definition:** Smallest deployable unit in Kubernetes

**pod.yaml:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
    environment: production
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    env:
    - name: ENVIRONMENT
      value: "production"
    volumeMounts:
    - name: data
      mountPath: /usr/share/nginx/html
  volumes:
  - name: data
    emptyDir: {}
```

### 2. Deployment
**Definition:** Manages stateless applications

**deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
```

### 3. Service
**Definition:** Exposes pods to network traffic

**service.yaml:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer  # ClusterIP, NodePort, LoadBalancer
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80          # Service port
    targetPort: 80    # Container port
    nodePort: 30080   # For NodePort type
```

**Service Types:**
- **ClusterIP:** Internal cluster IP (default)
- **NodePort:** Exposes service on each node's IP
- **LoadBalancer:** Cloud provider load balancer
- **ExternalName:** Maps to DNS name

### 4. ConfigMap
**Definition:** Store configuration data

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database_url: "postgresql://db:5432"
  api_key: "abc123"
  config.json: |
    {
      "debug": true,
      "timeout": 30
    }
```

### 5. Secret
**Definition:** Store sensitive data

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=      # base64 encoded
  password: cGFzc3dvcmQ=
```

### 6. Namespace
**Definition:** Virtual cluster for resource isolation

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
```

---

## 3.4 Installing Kubernetes (Multiple Methods)

### Method 1: Minikube (Local Development)

**Installation:**
```bash
# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Verify kubectl
kubectl version --client

# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Verify Minikube
minikube version

# Start Minikube cluster
minikube start --driver=docker --cpus=2 --memory=4096

# Enable addons
minikube addons enable dashboard
minikube addons enable metrics-server
minikube addons enable ingress

# Verify installation
kubectl cluster-info
kubectl get nodes
```

**Minikube Commands:**
```bash
# Start cluster
minikube start

# Stop cluster
minikube stop

# Delete cluster
minikube delete

# SSH into node
minikube ssh

# Open dashboard
minikube dashboard

# Access service
minikube service <service-name>

# Get Minikube IP
minikube ip

# View logs
minikube logs

# Set resource limits
minikube start --cpus=4 --memory=8192 --disk-size=40g
```

### Method 2: kubeadm (Production Clusters)

**Prerequisites:**
```bash
# Update system
sudo apt-get update
sudo apt-get upgrade -y

# Disable swap
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab

# Load kernel modules
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# Set sysctl params
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

**Install Container Runtime:**
```bash
# Install containerd
sudo apt-get install -y containerd

# Configure containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd
```

**Install Kubernetes Components:**
```bash
# Add Kubernetes apt repository
sudo apt-get install -y apt-transport-https ca-certificates curl
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Install kubelet, kubeadm, kubectl
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

**Initialize Master Node:**
```bash
# Initialize cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# Set up kubeconfig
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Install Pod network (Calico)
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

# Verify
kubectl get nodes
kubectl get pods -A
```

**Join Worker Nodes:**
```bash
# On worker nodes, run the join command from master output
sudo kubeadm join <master-ip>:6443 --token <token> \
  --discovery-token-ca-cert-hash sha256:<hash>
```

### Method 3: Managed Kubernetes (Cloud)

**Azure Kubernetes Service (AKS):**
```bash
# Create resource group
az group create --name myResourceGroup --location eastus

# Create AKS cluster
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 3 \
  --enable-addons monitoring \
  --generate-ssh-keys

# Get credentials
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

# Verify
kubectl get nodes
```

**Google Kubernetes Engine (GKE):**
```bash
# Create cluster
gcloud container clusters create my-cluster \
  --zone us-central1-a \
  --num-nodes 3

# Get credentials
gcloud container clusters get-credentials my-cluster
```

**Amazon EKS:**
```bash
# Create cluster using eksctl
eksctl create cluster \
  --name my-cluster \
  --region us-west-2 \
  --nodegroup-name standard-workers \
  --node-type t3.medium \
  --nodes 3
```

---

## 3.5 First Application Deployment (Complete Example)

### Example 1: Simple Nginx Deployment

**Step 1: Create Deployment**
```bash
# Imperative way
kubectl create deployment nginx-deploy --image=nginx:latest --replicas=3

# Declarative way (recommended)
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
EOF
```

**Step 2: Verify Deployment**
```bash
# Check deployment
kubectl get deployments
kubectl describe deployment nginx-deploy

# Check replica sets
kubectl get replicasets
kubectl get rs

# Check pods
kubectl get pods
kubectl get pods -o wide
kubectl describe pod <pod-name>

# Check pod logs
kubectl logs <pod-name>
kubectl logs -f <pod-name>  # Follow logs
```

**Step 3: Expose as Service**
```bash
# Create service
kubectl expose deployment nginx-deploy --type=NodePort --port=80

# Get service details
kubectl get services
kubectl get svc
kubectl describe service nginx-deploy

# Get service URL (Minikube)
minikube service nginx-deploy --url

# Access service
curl $(minikube service nginx-deploy --url)
```

**Step 4: Scale Deployment**
```bash
# Scale up
kubectl scale deployment nginx-deploy --replicas=5

# Verify scaling
kubectl get pods
kubectl get deployment nginx-deploy

# Auto-scaling
kubectl autoscale deployment nginx-deploy --min=2 --max=10 --cpu-percent=80
```

**Step 5: Update Application**
```bash
# Update image
kubectl set image deployment/nginx-deploy nginx=nginx:1.21

# Check rollout status
kubectl rollout status deployment/nginx-deploy

# View rollout history
kubectl rollout history deployment/nginx-deploy

# Rollback if needed
kubectl rollout undo deployment/nginx-deploy
kubectl rollout undo deployment/nginx-deploy --to-revision=2
```

**Step 6: Clean Up**
```bash
# Delete service
kubectl delete service nginx-deploy

# Delete deployment
kubectl delete deployment nginx-deploy

# Delete all resources with label
kubectl delete all -l app=nginx
```

---

### Example 2: Full-Stack Application

**Project Structure:**
```
k8s/
├── namespace.yaml
├── configmap.yaml
├── secret.yaml
├── database-deployment.yaml
├── database-service.yaml
├── backend-deployment.yaml
├── backend-service.yaml
├── frontend-deployment.yaml
└── frontend-service.yaml
```

**1. namespace.yaml**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: myapp
```

**2. configmap.yaml**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: myapp
data:
  DATABASE_HOST: "postgres-service"
  DATABASE_PORT: "5432"
  DATABASE_NAME: "appdb"
  API_URL: "http://backend-service:5000"
```

**3. secret.yaml**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
  namespace: myapp
type: Opaque
stringData:
  POSTGRES_PASSWORD: "mysecretpassword"
  API_KEY: "secret-api-key-123"
```

**4. database-deployment.yaml**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:14
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_DB
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: DATABASE_NAME
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: POSTGRES_PASSWORD
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
      volumes:
      - name: postgres-storage
        persistentVolumeClaim:
          claimName: postgres-pvc
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-pvc
  namespace: myapp
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

**5. database-service.yaml**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres-service
  namespace: myapp
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432
  type: ClusterIP
```

**Deploy Everything:**
```bash
# Apply all configurations
kubectl apply -f namespace.yaml
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
kubectl apply -f database-deployment.yaml
kubectl apply -f database-service.yaml
kubectl apply -f backend-deployment.yaml
kubectl apply -f backend-service.yaml
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml

# Or apply entire directory
kubectl apply -f k8s/

# Verify all resources
kubectl get all -n myapp
kubectl get pods -n myapp
kubectl get services -n myapp
kubectl get configmaps -n myapp
kubectl get secrets -n myapp
```

---

## 3.6 Essential kubectl Commands

### Cluster Management
```bash
kubectl cluster-info
kubectl version
kubectl get nodes
kubectl describe node <node-name>
kubectl top nodes
kubectl cordon <node-name>      # Mark unschedulable
kubectl uncordon <node-name>    # Mark schedulable
kubectl drain <node-name>       # Evict pods
```

### Pod Operations
```bash
kubectl get pods
kubectl get pods -A                          # All namespaces
kubectl get pods -n <namespace>
kubectl get pods -o wide
kubectl get pods --show-labels
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs -f <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec <pod-name> -- ls /app
kubectl port-forward <pod-name> 8080:80
kubectl delete pod <pod-name>
kubectl delete pods --all
```

### Deployment Operations
```bash
kubectl get deployments
kubectl create deployment <name> --image=<image>
kubectl scale deployment <name> --replicas=5
kubectl autoscale deployment <name> --min=2 --max=10
kubectl set image deployment/<name> <container>=<new-image>
kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>
kubectl rollout undo deployment/<name>
kubectl delete deployment <name>
```

### Service Operations
```bash
kubectl get services
kubectl expose deployment <name> --port=80 --type=LoadBalancer
kubectl describe service <service-name>
kubectl delete service <service-name>
```

### ConfigMap & Secret
```bash
kubectl create configmap <name> --from-literal=key=value
kubectl create configmap <name> --from-file=config.txt
kubectl get configmaps
kubectl describe configmap <name>
kubectl create secret generic <name> --from-literal=password=secret
kubectl get secrets
kubectl describe secret <name>
```

### Debugging
```bash
kubectl get events
kubectl get events --sort-by='.lastTimestamp'
kubectl describe <resource-type> <resource-name>
kubectl logs <pod-name> --previous
kubectl top pods
kubectl top nodes
kubectl explain pods
kubectl api-resources
```

---

## 3.7 Kubernetes Networking

### Pod-to-Pod Communication
- Each pod gets unique IP
- Pods can communicate directly
- No NAT required

### Service Discovery
```yaml
# DNS resolution
<service-name>.<namespace>.svc.cluster.local
```

### Ingress Controller
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
```

---

## 3.8 Exam Important Points ⭐

**Key Concepts:**
- Kubernetes is a container orchestration platform
- Control plane manages cluster state
- Worker nodes run application containers
- Pod is the smallest deployable unit
- Deployment manages stateless applications
- Service provides stable networking
- ConfigMap and Secret store configuration
- kubectl is the CLI tool

**Architecture Components:**
- API Server, etcd, Scheduler, Controller Manager
- kubelet, kube-proxy, Container Runtime
- Master (Control Plane) vs Worker Nodes

**Common Exam Questions:**
1. Explain Kubernetes architecture
2. Difference between Pod, Deployment, and Service
3. How to install Kubernetes using Minikube/kubeadm
4. Deployment strategies (Rolling update, Recreate)
5. Service types (ClusterIP, NodePort, LoadBalancer)
6. How to scale applications in Kubernetes
7. Rollback process for failed deployments
8. Difference between ConfigMap and Secret

**Important Commands:**
```bash
kubectl create/apply/get/describe/delete
kubectl scale/autoscale
kubectl expose
kubectl rollout status/history/undo
kubectl logs/exec
minikube start/stop/delete
```

---

# 🔧 4. Configuration Management Tools {#configuration-management}

## 4.1 What is Configuration Management?

**Definition:**
Configuration Management (CM) is the practice of **systematically handling changes** to a system in a way that maintains **consistency** and **integrity** over time.

**Purpose:**
- Automate system configuration
- Ensure consistency across environments
- Version control for infrastructure
- Reduce human error
- Enable rapid scaling
- Disaster recovery

**Key Principles:**
1. **Idempotency:** Running multiple times produces same result
2. **Declarative:** Describe desired state, not steps
3. **Infrastructure as Code:** Configuration in version control
4. **Automation:** Reduce manual intervention

---

## 4.2 Puppet: Master-Agent Architecture

### What is Puppet?

**Definition:**
Puppet is a **declarative configuration management tool** using a **Master-Agent (Client-Server)** architecture. It uses a custom DSL (Domain-Specific Language) to define system configurations.

**Architecture:**
```
┌─────────────────────────────────────┐
│        PUPPET MASTER                │
│                                     │
│  ┌──────────────────────────────┐  │
│  │   Puppet Server              │  │
│  │   - Compiles catalogs        │  │
│  │   - Stores manifests         │  │
│  │   - Certificate Authority    │  │
│  └──────────────────────────────┘  │
│                                     │
│  ┌──────────────────────────────┐  │
│  │   PuppetDB                   │  │
│  │   - Stores facts & reports   │  │
│  └──────────────────────────────┘  │
└─────────────┬───────────────────────┘
              │
       ┌──────┴──────┬─────────┐
       │             │         │
┌──────▼──────┐ ┌────▼────┐ ┌─▼────────┐
│ Puppet Agent│ │ Puppet  │ │ Puppet   │
│   (Node 1)  │ │ Agent   │ │ Agent    │
│             │ │ (Node 2)│ │ (Node 3) │
└─────────────┘ └─────────┘ └──────────┘
```

### Puppet Workflow

**Step-by-Step Process:**

1. **Agent sends facts to Master**
   - System information (OS, IP, hardware)
   - Custom facts defined by user

2. **Master compiles catalog**
   - Reads manifests
   - Applies node-specific configuration
   - Creates catalog (list of resources)

3. **Master sends catalog to Agent**
   - Catalog contains desired state
   - Agent-specific configuration

4. **Agent applies catalog**
   - Compares current state with desired state
   - Makes necessary changes
   - Generates report

5. **Agent sends report to Master**
   - Success/failure status
   - Changes made
   - Stored in PuppetDB

---

### Installing Puppet

**Install Puppet Master (Server):**
```bash
# Add Puppet repository
wget https://apt.puppet.com/puppet7-release-focal.deb
sudo dpkg -i puppet7-release-focal.deb
sudo apt-get update

# Install Puppet Server
sudo apt-get install puppetserver

# Configure memory (edit /etc/default/puppetserver)
# JAVA_ARGS="-Xms2g -Xmx2g"

# Start Puppet Server
sudo systemctl start puppetserver
sudo systemctl enable puppetserver

# Verify
sudo systemctl status puppetserver
```

**Install Puppet Agent (Client):**
```bash
# Add repository (same as master)
wget https://apt.puppet.com/puppet7-release-focal.deb
sudo dpkg -i puppet7-release-focal.deb
sudo apt-get update

# Install Puppet Agent
sudo apt-get install puppet-agent

# Configure agent (/etc/puppetlabs/puppet/puppet.conf)
[main]
certname = node1.example.com
server = puppet.example.com

# Start agent
sudo systemctl start puppet
sudo systemctl enable puppet

# Test connection
sudo /opt/puppetlabs/bin/puppet agent --test
```

**Certificate Management:**
```bash
# On Master: List certificate requests
sudo puppetserver ca list

# Sign certificate
sudo puppetserver ca sign --certname node1.example.com

# Sign all
sudo puppetserver ca sign --all

# On Agent: Request certificate
sudo /opt/puppetlabs/bin/puppet agent --test
```

---

### Puppet Manifests

**Basic Syntax:**
```puppet
# Resource declaration
resource_type { 'resource_title':
  attribute => value,
  attribute => value,
}
```

**Example 1: Simple Package & Service**
```puppet
# /etc/puppetlabs/code/environments/production/manifests/site.pp

node 'webserver.example.com' {
  # Install Apache
  package { 'apache2':
    ensure => installed,
  }

  # Manage Apache service
  service { 'apache2':
    ensure => running,
    enable => true,
    require => Package['apache2'],
  }

  # Manage configuration file
  file { '/var/www/html/index.html':
    ensure  => file,
    content => "<h1>Hello from Puppet!</h1>\n",
    owner   => 'www-data',
    group   => 'www-data',
    mode    => '0644',
    require => Package['apache2'],
  }
}
```

**Example 2: Complete Web Server Setup**
```puppet
# manifests/webserver.pp

class webserver {
  # Install packages
  package { ['nginx', 'php-fpm', 'git']:
    ensure => installed,
  }

  # Manage nginx configuration
  file { '/etc/nginx/sites-available/default':
    ensure  => file,
    source  => 'puppet:///modules/webserver/nginx-default',
    require => Package['nginx'],
    notify  => Service['nginx'],
  }

  # Enable site
  file { '/etc/nginx/sites-enabled/default':
    ensure => link,
    target => '/etc/nginx/sites-available/default',
  }

  # Manage nginx service
  service { 'nginx':
    ensure  => running,
    enable  => true,
    require => [
      Package['nginx'],
      File['/etc/nginx/sites-enabled/default'],
    ],
  }

  # Manage PHP service
  service { 'php7.4-fpm':
    ensure  => running,
    enable  => true,
    require => Package['php-fpm'],
  }

  # Create web directory
  file { '/var/www/myapp':
    ensure => directory,
    owner  => 'www-data',
    group  => 'www-data',
    mode   => '0755',
  }
}

# Apply to nodes
node 'web1.example.com' {
  include webserver
}

node 'web2.example.com' {
  include webserver
}
```

**Example 3: Using Variables & Templates**
```puppet
# manifests/app.pp

class myapp (
  String $app_name = 'myapp',
  String $app_port = '8080',
  String $environment = 'production',
) {
  # Install dependencies
  package { 'nodejs':
    ensure => installed,
  }

  # Create app user
  user { 'appuser':
    ensure => present,
    home   => '/home/appuser',
    shell  => '/bin/bash',
  }

  # Create app directory
  file { '/opt/myapp':
    ensure => directory,
    owner  => 'appuser',
    group  => 'appuser',
    mode   => '0755',
  }

  # Deploy configuration from template
  file { '/opt/myapp/config.json':
    ensure  => file,
    content => template('myapp/config.json.erb'),
    owner   => 'appuser',
    group   => 'appuser',
    mode    => '0644',
  }

  # Manage systemd service
  file { '/etc/systemd/system/myapp.service':
    ensure  => file,
    content => epp('myapp/myapp.service.epp', {
      'app_name' => $app_name,
      'app_port' => $app_port,
    }),
    notify  => Exec['systemd-reload'],
  }

  exec { 'systemd-reload':
    command     => '/bin/systemctl daemon-reload',
    refreshonly => true,
  }

  service { 'myapp':
    ensure  => running,
    enable  => true,
    require => [
      Package['nodejs'],
      File['/etc/systemd/system/myapp.service'],
    ],
  }
}
```

**Puppet Template Example (ERB):**
```erb
# templates/config.json.erb
{
  "app_name": "<%= @app_name %>",
  "environment": "<%= @environment %>",
  "port": <%= @app_port %>,
  "database": {
    "host": "<%= @db_host %>",
    "port": <%= @db_port %>,
    "name": "<%= @db_name %>"
  }
}
```

---

### Puppet Facts & Hiera

**Facts (System Information):**
```bash
# View all facts
sudo facter

# Specific fact
sudo facter os
sudo facter networking
sudo facter memory

# Use in manifests
if $facts['os']['family'] == 'Debian' {
  package { 'apache2':
    ensure => installed,
  }
} elsif $facts['os']['family'] == 'RedHat' {
  package { 'httpd':
    ensure => installed,
  }
}
```

**Hiera (Hierarchical Data):**
```yaml
# /etc/puppetlabs/code/environments/production/data/common.yaml
---
myapp::app_port: 8080
myapp::environment: production
myapp::db_host: db.example.com

# Node-specific data
# /etc/puppetlabs/code/environments/production/data/nodes/web1.yaml
---
myapp::app_port: 8081
```

---

## 4.3 Ansible: Agentless Automation

### What is Ansible?

**Definition:**
Ansible is an **agentless automation tool** that uses **SSH** for Linux/Unix and **WinRM** for Windows. It uses **YAML** playbooks to define configurations.

**Key Features:**
- No agent installation required
- Uses SSH (secure)
- YAML syntax (human-readable)
- Push-based model
- Idempotent operations
- Large module library

**Architecture:**
```
┌─────────────────────────────────┐
│     CONTROL NODE (Ansible)      │
│                                 │
│  ┌──────────────────────────┐  │
│  │  Inventory               │  │
│  │  - hosts.ini             │  │
│  │  - group_vars/           │  │
│  │  - host_vars/            │  │
│  └──────────────────────────┘  │
│                                 │
│  ┌──────────────────────────┐  │
│  │  Playbooks               │  │
│  │  - site.yml              │  │
│  │  - webservers.yml        │  │
│  └──────────────────────────┘  │
│                                 │
│  ┌──────────────────────────┐  │
│  │  Roles                   │  │
│  │  - common/               │  │
│  │  - webserver/            │  │
│  └──────────────────────────┘  │
└────────┬────────────────────────┘
         │ SSH
    ┌────┴────┬──────────┐
┌───▼────┐ ┌──▼──────┐ ┌▼────────┐
│ Node 1 │ │ Node 2  │ │ Node 3  │
│ (No    │ │ (No     │ │ (No     │
│ Agent) │ │ Agent)  │ │ Agent)  │
└────────┘ └─────────┘ └─────────┘
```

---

### Installing Ansible

**On Control Node:**
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install ansible -y

# Or using pip
pip install ansible

# Verify installation
ansible --version

# Check available modules
ansible-doc -l
ansible-doc apt
```

**Setup SSH Keys:**
```bash
# Generate SSH key on control node
ssh-keygen -t rsa -b 4096

# Copy to managed nodes
ssh-copy-id user@node1.example.com
ssh-copy-id user@node2.example.com

# Test connection
ssh user@node1.example.com
```

---

### Ansible Inventory

**INI Format (/etc/ansible/hosts):**
```ini
# Individual hosts
web1.example.com
web2.example.com

# Group of hosts
[webservers]
web1.example.com
web2.example.com
web3.example.com

[databases]
db1.example.com
db2.example.com

[production:children]
webservers
databases

# With variables
[webservers]
web1.example.com ansible_host=192.168.1.10 ansible_user=ubuntu
web2.example.com ansible_host=192.168.1.11 ansible_user=ubuntu

# Port specification
[app]
app1.example.com:2222

# Variables for group
[webservers:vars]
ansible_python_interpreter=/usr/bin/python3
http_port=80
```

**YAML Format (inventory.yml):**
```yaml
all:
  hosts:
    localhost:
      ansible_connection: local
  
  children:
    webservers:
      hosts:
        web1.example.com:
          ansible_host: 192.168.1.10
        web2.example.com:
          ansible_host: 192.168.1.11
      vars:
        ansible_user: ubuntu
        http_port: 80
    
    databases:
      hosts:
        db1.example.com:
        db2.example.com:
      vars:
        db_port: 5432
```

---

### Ansible Ad-Hoc Commands

**Basic Syntax:**
```bash
ansible <host-pattern> -m <module> -a "<module-arguments>"
```

**Examples:**
```bash
# Ping all hosts
ansible all -m ping

# Ping specific group
ansible webservers -m ping

# Run shell command
ansible all -m shell -a "uptime"
ansible webservers -m shell -a "df -h"

# Install package
ansible webservers -m apt -a "name=nginx state=present" --become

# Copy file
ansible all -m copy -a "src=/tmp/file.txt dest=/tmp/ mode=0644"

# Manage service
ansible webservers -m service -a "name=nginx state=started enabled=yes" --become

# Gather facts
ansible all -m setup

# Create user
ansible all -m user -a "name=john state=present" --become

# Reboot servers
ansible all -m reboot --become

# Check disk space
ansible all -m shell -a "df -h" -o
```

---

### Ansible Playbooks

**Basic Playbook Structure:**
```yaml
---
- name: Playbook description
  hosts: target_hosts
  become: yes
  vars:
    variable_name: value
  
  tasks:
    - name: Task description
      module_name:
        parameter: value
```

**Example 1: Simple Web Server Setup**
```yaml
---
- name: Setup Web Server
  hosts: webservers
  become: yes
  
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600
    
    - name: Install Nginx
      apt:
        name: nginx
        state: present
    
    - name: Start and enable Nginx
      service:
        name: nginx
        state: started
        enabled: yes
    
    - name: Deploy index.html
      copy:
        content: |
          <html>
            <head><title>Welcome</title></head>
            <body><h1>Hello from Ansible!</h1></body>
          </html>
        dest: /var/www/html/index.html
        mode: '0644'
      notify: Reload Nginx
  
  handlers:
    - name: Reload Nginx
      service:
        name: nginx
        state: reloaded
```

**Run Playbook:**
```bash
# Basic execution
ansible-playbook webserver.yml

# Check syntax
ansible-playbook webserver.yml --syntax-check

# Dry run
ansible-playbook webserver.yml --check

# Verbose output
ansible-playbook webserver.yml -v
ansible-playbook webserver.yml -vvv

# Limit to specific hosts
ansible-playbook webserver.yml --limit web1.example.com

# Use specific inventory
ansible-playbook -i inventory.yml webserver.yml

# Pass extra variables
ansible-playbook webserver.yml --extra-vars "http_port=8080"
```

**Example 2: Complete Application Deployment**
```yaml
---
- name: Deploy Full-Stack Application
  hosts: all
  become: yes
  vars:
    app_name: myapp
    app_version: 1.0.0
    app_port: 3000
    db_name: appdb
    db_user: appuser
    db_password: "{{ vault_db_password }}"
  
  tasks:
    # System updates
    - name: Update system packages
      apt:
        upgrade: dist
        update_cache: yes
      tags: system
    
    # Install dependencies
    - name: Install required packages
      apt:
        name:
          - git
          - nodejs
          - npm
          - postgresql
          - nginx
        state: present
      tags: packages
    
    # Configure database
    - name: Start PostgreSQL
      service:
        name: postgresql
        state: started
        enabled: yes
      tags: database
    
    - name: Create database
      postgresql_db:
        name: "{{ db_name }}"
        state: present
      become_user: postgres
      tags: database
    
    - name: Create database user
      postgresql_user:
        name: "{{ db_user }}"
        password: "{{ db_password }}"
        db: "{{ db_name }}"
        priv: ALL
        state: present
      become_user: postgres
      tags: database
    
    # Deploy application
    - name: Create app directory
      file:
        path: "/opt/{{ app_name }}"
        state: directory
        owner: www-data
        group: www-data
      tags: app
    
    - name: Clone application from Git
      git:
        repo: https://github.com/example/myapp.git
        dest: "/opt/{{ app_name }}"
        version: "v{{ app_version }}"
      become_user: www-data
      tags: app
    
    - name: Install Node dependencies
      npm:
        path: "/opt/{{ app_name }}"
        state: present
      become_user: www-data
      tags: app
    
    - name: Deploy environment file
      template:
        src: templates/env.j2
        dest: "/opt/{{ app_name }}/.env"
        owner: www-data
        group: www-data
        mode: '0600'
      tags: app
      notify: Restart application
    
    # Configure systemd service
    - name: Deploy systemd service file
      template:
        src: templates/myapp.service.j2
        dest: /etc/systemd/system/myapp.service
        mode: '0644'
      tags: app
      notify:
        - Reload systemd
        - Restart application
    
    - name: Start application service
      service:
        name: myapp
        state: started
        enabled: yes
      tags: app
    
    # Configure Nginx
    - name: Deploy Nginx configuration
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/myapp
      tags: nginx
      notify: Reload Nginx
    
    - name: Enable Nginx site
      file:
        src: /etc/nginx/sites-available/myapp
        dest: /etc/nginx/sites-enabled/myapp
        state: link
      tags: nginx
      notify: Reload Nginx
    
    - name: Remove default Nginx site
      file:
        path: /etc/nginx/sites-enabled/default
        state: absent
      tags: nginx
      notify: Reload Nginx
    
    # Configure firewall
    - name: Allow HTTP traffic
      ufw:
        rule: allow
        port: '80'
        proto: tcp
      tags: security
    
    - name: Allow HTTPS traffic
      ufw:
        rule: allow
        port: '443'
        proto: tcp
      tags: security
    
    - name: Enable UFW
      ufw:
        state: enabled
      tags: security
  
  handlers:
    - name: Reload systemd
      systemd:
        daemon_reload: yes
    
    - name: Restart application
      service:
        name: myapp
        state: restarted
    
    - name: Reload Nginx
      service:
        name: nginx
        state: reloaded
```

**Template Example (templates/env.j2):**
```jinja2
# Application Configuration
NODE_ENV=production
PORT={{ app_port }}

# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME={{ db_name }}
DB_USER={{ db_user }}
DB_PASSWORD={{ db_password }}

# Application Settings
APP_NAME={{ app_name }}
APP_VERSION={{ app_version }}
```

---

### Ansible Roles

**Role Structure:**
```
roles/
└── webserver/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    │   ├── nginx.conf.j2
    │   └── index.html.j2
    ├── files/
    │   └── custom.conf
    ├── vars/
    │   └── main.yml
    ├── defaults/
    │   └── main.yml
    ├── meta/
    │   └── main.yml
    └── README.md
```

**Create Role:**
```bash
# Create role structure
ansible-galaxy init webserver

# Use role in playbook
---
- name: Setup Web Servers
  hosts: webservers
  roles:
    - common
    - webserver
    - monitoring
```

**roles/webserver/tasks/main.yml:**
```yaml
---
- name: Install Nginx
  apt:
    name: nginx
    state: present

- name: Deploy configuration
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Reload Nginx

- name: Ensure Nginx is running
  service:
    name: nginx
    state: started
    enabled: yes
```

---

### Ansible Vault (Secrets Management)

```bash
# Create encrypted file
ansible-vault create secrets.yml

# Edit encrypted file
ansible-vault edit secrets.yml

# Encrypt existing file
ansible-vault encrypt vars.yml

# Decrypt file
ansible-vault decrypt vars.yml

# View encrypted file
ansible-vault view secrets.yml

# Run playbook with vault
ansible-playbook site.yml --ask-vault-pass
ansible-playbook site.yml --vault-password-file ~/.vault_pass

# Encrypt specific string
ansible-vault encrypt_string 'mysecretpassword' --name 'db_password'
```

---

## 4.4 PalletOps: Functional DevOps

**Definition:**
PalletOps is a **Clojure-based** infrastructure automation tool using **functional programming** paradigms for data center provisioning and configuration.

**Key Features:**
- Written in Clojure (JVM-based)
- Functional approach to infrastructure
- Server provisioning and configuration
- Cloud provider integration
- Immutable infrastructure

**Example:**
```clojure
(require '[pallet.api :as api])
(require '[pallet.compute :as compute])

; Define server specification
(def nginx-server
  (api/server-spec
    :phases
    {:configure (api/plan-fn
                  (package "nginx")
                  (service "nginx" :action :start))}))

; Deploy to cloud
(def myservice
  (api/group-spec "webservers"
    :count 3
    :node-spec (api/node-spec
                 :image {:os-family :ubuntu
                         :os-version "20.04"})
    :extends [nginx-server]))
```

**Note:** PalletOps is less commonly used compared to Puppet and Ansible, and is primarily relevant for Clojure-based infrastructure.

---

## 4.5 Tool Comparison

| Feature | Puppet | Ansible | SaltStack | Chef | PalletOps |
|---------|--------|---------|-----------|------|-----------|
| **Architecture** | Master-Agent | Agentless | Master-Minion | Master-Client | Agentless |
| **Communication** | Pull | Push (SSH) | Push/Pull (ZeroMQ) | Pull | SSH |
| **Language** | Puppet DSL | YAML | YAML/Jinja2 | Ruby DSL | Clojure |
| **Agent Required** | Yes | No | Yes | Yes | No |
| **Learning Curve** | Moderate | Easy | Moderate | Steep | Steep |
| **Configuration** | Declarative | Declarative | Declarative | Procedural | Functional |
| **Speed** | Moderate | Moderate | Fast | Moderate | Moderate |
| **Scalability** | High | Moderate | Very High | High | Moderate |
| **Community** | Large | Very Large | Moderate | Large | Small |
| **Best For** | Enterprise, Compliance | DevOps, Quick setup | Large scale, Speed | Developer-friendly | Clojure shops |

---

## 4.6 Exam Important Points ⭐

**Key Concepts:**
- Configuration Management ensures consistency
- Puppet uses Master-Agent, Ansible is agentless
- Idempotency: Same result on multiple runs
- Infrastructure as Code (IaC) principle
- Declarative vs Procedural approaches
- Push vs Pull models

**Puppet:**
- Master-Agent architecture
- Puppet DSL (Domain-Specific Language)
- Manifests define configurations
- PuppetDB stores facts and reports
- Certificate-based authentication
- Pull-based model (agents pull from master)

**Ansible:**
- Agentless (uses SSH)
- YAML playbooks
- Inventory files define hosts
- Modules for different tasks
- Roles for code reusability
- Ansible Vault for secrets
- Push-based model

**Common Exam Questions:**
1. Difference between Puppet and Ansible
2. What is idempotency in CM?
3. Puppet Master-Agent workflow
4. How does Ansible work without agents?
5. When to use Puppet vs Ansible?
6. What are Ansible roles?
7. How to manage secrets in Ansible?
8. Explain Infrastructure as Code

------------------------------------------------------------------------

# Deploying with SaltStack, DevOps Best Practices, Tools: Ansible, SaltStack

## 1. Deploying with SaltStack

**Definition:**\
SaltStack is an **open-source configuration and orchestration tool**
using **Master--Minion** architecture.

**Example:**

``` yaml
nginx:
  pkg.installed: []
  service.running:
    - enable: True
```

**Advantages:** - Fast execution via ZeroMQ.\
- Parallel deployments.\
- Scalable management.

------------------------------------------------------------------------

## 2. DevOps Best Practices

1.  Infrastructure as Code (IaC)\
2.  Continuous Integration/Deployment (CI/CD)\
3.  Configuration Management\
4.  Monitoring and Logging\
5.  Version Control (Git)\
6.  Collaboration\
7.  Automation

**Benefits:** Faster delivery, fewer errors, reliable scaling.

------------------------------------------------------------------------

## 3. Tools: Ansible vs SaltStack

  Feature         Ansible     SaltStack
  --------------- ----------- ----------------
  Architecture    Agentless   Master--Minion
  Language        YAML        YAML
  Communication   SSH         ZeroMQ
  Speed           Moderate    High
  Mode            Push        Push/Pull

**Example:**

``` yaml
# Ansible
- hosts: webservers
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
```

``` yaml
# SaltStack
nginx:
  pkg.installed: []
  service.running:
    - enable: True
```

**Conclusion:**\
SaltStack and Ansible automate configuration and deployments
efficiently, implementing key **DevOps principles** for scalable,
reliable environments.
