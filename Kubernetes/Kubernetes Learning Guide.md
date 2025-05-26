## **Table of Contents**

1.  Prerequisites
    
2.  Step 1: Install Required Tools
    
    -   Install Docker
        
    -   Install Kind
        
    -   Install kubectl
        
3.  Step 2: Create a Multi-Node Cluster
    
    -   Define `kind-config.yaml`
        
    -   Start the Cluster
        
    -   Verify Nodes
        
4.  Step 3: Deploy & Manage Apps
    
    -   Deploy Nginx
        
    -   Expose with a Service
        
    -   Scale the Deployment
        
5.  Step 4: Explore Kubernetes Components
    
    -   Worker Node Details
        
    -   Control Plane Components
        
    -   Node Services (kube-proxy, kubelet)
        
6.  Step 5: Clean Up
    
7.  Next Steps
    
    -   Persistent Storage
        
    -   Ingress Controller
        
    -   Helm Package Manager
        

----------

## **1. Prerequisites**

Before starting, ensure you have:

-   **Linux/macOS** (Windows: Use WSL2 or a VM).
    
-   **Docker installed** (for container runtime).
    
-   **kubectl installed** (Kubernetes CLI).
    
-   Basic terminal knowledge.
    

----------

## **2. Step 1: Install Required Tools**

### **Install Docker**

Run these commands in your terminal:

```bash
sudo apt update && sudo apt install -y docker.io
sudo usermod -aG docker $USER  
newgrp docker  
docker --version  # Verify
```
### **Install Kind**

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind  
sudo mv ./kind /usr/local/bin/  
kind version  
```
### **Install kubectl**

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl  
sudo mv kubectl /usr/local/bin/  
kubectl version --client  
```
----------

## **3. Step 2: Create a Multi-Node Cluster**

### **Create `kind-config.yaml`**

```yaml
kind: Cluster  
apiVersion: kind.x-k8s.io/v1alpha4  
nodes:  
  - role: control-plane  
  - role: worker  
  - role: worker  
```
### **Start the Cluster**

```bash
kind create cluster --config kind-config.yaml  
```
### **Verify Nodes**

```bash
kubectl get nodes  
```
**Expected Output:**
```bash
NAME                  STATUS   ROLES           AGE   VERSION  
kind-control-plane    Ready    control-plane   20s   v1.27.3  
kind-worker           Ready    <none>          18s   v1.27.3  
kind-worker-2         Ready    <none>          15s   v1.27.3  
```
----------

## **4. Step 3: Deploy & Manage Apps**

### **Deploy Nginx**

```bash
kubectl create deployment nginx --image=nginx --replicas=3  
kubectl get pods -o wide  
```
### **Expose the App**
```bash

kubectl expose deployment nginx --port=80 --type=NodePort  
kubectl get svc  
```
### **Access the App**

```bash
kubectl get svc nginx -o jsonpath='{.spec.ports[0].nodePort}'  

Open `http://localhost:<NodePort>` in a browser.
```
### **Scale the Deployment**

```bash
kubectl scale deployment nginx --replicas=5  
kubectl get pods -o wide  
```
----------

## **5. Step 4: Explore Kubernetes Components**

### **Worker Node Details**

```bash
kubectl describe node kind-worker  
```
### **Control Plane Components**

```bash
kubectl get pods -n kube-system  
```
### **Node Services**

```bash
kubectl get pods -n kube-system | grep kube-proxy  
kubectl get pods -n kube-system | grep kubelet  
```
----------

## **6. Step 5: Clean Up**

```bash
kind delete cluster  
```
----------

## **7. Next Steps**

-   **Persistent Storage**:
    
    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/website/main/content/en/examples/pods/storage/pv-volume.yaml  
    ```
-   **Ingress Controller**:
    
    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml  
    ```
-   **Helm**:
    
    ```bash
    curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash  
    helm install prometheus stable/prometheus  
    ```

----------

### **📌 Notes**

-   This guide uses **Kind** for simplicity. For production, consider **kubeadm** or cloud providers (EKS/GKE/AKS).
    
-   All commands are for **Linux/macOS**. Adjust paths for Windows.
    

