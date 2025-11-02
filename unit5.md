# Getting Started with Docker Compose and Deploying Docker Compose Containers in ACI

## 1. Getting Started with Docker Compose

**Definition:**\
Docker Compose is a tool used to define and manage **multi-container
Docker applications**. It uses a **YAML configuration file
(`docker-compose.yml`)** to describe services, networks, and volumes
required for an application.

**Purpose:**\
It helps in running multiple containers together (e.g., web server +
database) with a single command.

**Key Features:** - Declarative YAML configuration file.\
- Easy management of multi-container applications.\
- Automatic creation of networks and volumes.\
- One-command deployment (`docker compose up`).

**Basic Workflow:** 1. **Create a `docker-compose.yml` file** 2. **Build
and start containers** -- `docker compose up -d` 3. **List running
containers** -- `docker ps` 4. **Stop and remove containers** --
`docker compose down`

**Example `docker-compose.yml`:**

``` yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: example
```

------------------------------------------------------------------------

## 2. Deploying Docker Compose Containers in Azure Container Instances (ACI)

**Definition:**\
Azure Container Instances (ACI) allow you to **run Docker containers in
the cloud** without managing virtual machines or Kubernetes clusters.

**Purpose:**\
To deploy multi-container applications defined in `docker-compose.yml`
directly to Azure's managed container service.

### Steps to Deploy:

1.  **Login to Azure and Docker CLI**

    ``` bash
    az login
    docker login azure
    ```

2.  **Create a Resource Group**

    ``` bash
    az group create --name myResourceGroup --location eastus
    ```

3.  **Deploy Application**

    ``` bash
    docker compose -f docker-compose.yml up
    ```

4.  **Verify Deployment**

    ``` bash
    az container list --resource-group myResourceGroup --output table
    ```

5.  **Access the Application**

    -   Use the public IP assigned by ACI.

6.  **Stop Deployment**

    ``` bash
    az container delete --name <container-name> --resource-group myResourceGroup
    ```

**Advantages:** - Simplified cloud container deployment.\
- No need for complex orchestration.\
- Pay only for runtime.

**Conclusion:**\
Docker Compose simplifies local multi-container app management, while
**ACI** provides scalable, managed cloud deployment.

------------------------------------------------------------------------

# Installing Kubernetes and First Example of Kubernetes Application Deployment

## 1. Installing Kubernetes (Using Minikube)

**Definition:**\
Kubernetes is an **open-source container orchestration system** that
automates deployment, scaling, and management.

### Steps:

1.  **Install Docker, kubectl, and Minikube**

2.  **Install kubectl:**

    ``` bash
    curl -LO https://.../kubectl
    chmod +x ./kubectl
    sudo mv ./kubectl /usr/local/bin/
    ```

3.  **Install Minikube:**

    ``` bash
    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    sudo install minikube-linux-amd64 /usr/local/bin/minikube
    ```

4.  **Start Cluster:**

    ``` bash
    minikube start
    ```

5.  **Verify Installation:**

    ``` bash
    kubectl get nodes
    ```

------------------------------------------------------------------------

## 2. Example Deployment (Nginx Web Server)

1.  **Create Deployment:**

    ``` bash
    kubectl create deployment nginx-deploy --image=nginx
    ```

2.  **Verify:**

    ``` bash
    kubectl get deployments
    kubectl get pods
    ```

3.  **Expose Deployment:**

    ``` bash
    kubectl expose deployment nginx-deploy --type=NodePort --port=80
    ```

4.  **Access App:**

    ``` bash
    minikube service nginx-deploy
    ```

**Key Components:** \| Component \| Description \|
\|------------\|--------------\| \| Pod \| Smallest unit running
containers \| \| Deployment \| Ensures replicas are running \| \|
Service \| Provides network access \|

**Conclusion:**\
Kubernetes with Minikube offers a practical setup for testing and
deploying containerized applications.

------------------------------------------------------------------------

# Deploying the Code: The Puppet Master and Puppet Agents, Ansible, PalletOps

## 1. The Puppet Master and Puppet Agents

**Definition:**\
Puppet is a **configuration management tool** using a **Master-Agent**
model to automate deployment and system configuration.

### Workflow:

1.  Master stores configuration manifests.\
2.  Agents pull configs and apply changes.\
3.  Reports sent back for verification.

**Example Manifest:**

``` puppet
package { 'nginx': ensure => installed, }
service { 'nginx': ensure => running, enable => true, }
```

------------------------------------------------------------------------

## 2. Ansible

**Definition:**\
Ansible is an **agentless automation tool** using SSH and YAML
playbooks.

**Example Playbook:**

``` yaml
- hosts: webservers
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
```

------------------------------------------------------------------------

## 3. PalletOps

**Definition:**\
PalletOps is a **Clojure-based DevOps automation tool** for data center
provisioning.

**Example:**

``` clojure
(server-spec :phases {:configure (plan-fn (package "nginx"))})
```

  Tool        Architecture    Agent Requirement   Language     Feature
  ----------- --------------- ------------------- ------------ ----------------------
  Puppet      Master--Agent   Yes                 Puppet DSL   Model-driven
  Ansible     Agentless       No                  YAML         Simple & lightweight
  PalletOps   Functional      No                  Clojure      Server provisioning

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
