# Minikube Installation on Ubuntu with Docker

This guide provides step-by-step instructions to install and set up Minikube on Ubuntu using Docker as the driver, along with the `kubectl` command-line tool.

## Step 1: Install Docker

1. **Update the package index:**
   ```bash
   sudo apt update
   ```

2. **Install Docker:**
   ```bash
   sudo apt install docker.io
   ```

3. **Start Docker and enable it to run on startup:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

4. **(Optional) Add your user to the `docker` group:**
   ```bash
   sudo usermod -aG docker $USER
   newgrp docker
   ```

## Step 2: Install `kubectl`

1. **Download the latest `kubectl` release:**
   ```bash
   curl -LO "https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   ```

2. **Make the `kubectl` binary executable:**
   ```bash
   chmod +x kubectl
   ```

3. **Move the binary to a directory in your PATH:**
   ```bash
   sudo mv kubectl /usr/local/bin/
   ```

4. **Verify the installation:**
   ```bash
   kubectl version --client
   ```

## Step 3: Install Minikube

1. **Download the Minikube binary:**
   ```bash
   curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
   ```

2. **Install Minikube:**
   ```bash
   sudo install minikube-linux-amd64 /usr/local/bin/minikube
   ```

## Step 4: Start Minikube

Start Minikube with the Docker driver:
```bash
minikube start --driver=docker
```

## Step 5: Verify Installation

1. **Check the status of Minikube:**
   ```bash
   minikube status
   ```

2. **Access the Minikube dashboard (optional):**
   ```bash
   minikube dashboard
   ```

## Additional Commands

- **To stop Minikube:**
  ```bash
  minikube stop
  ```

- **To delete Minikube:**
  ```bash
  minikube delete
  ```
---