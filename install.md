---
title: 🚀 Installing Run AI
nav_order: 2
parent: 📚 Learn to Run
---

# Installing a Self-Hosted Cluster

> ⚠ **Note:** Always refer to official documentation — this is just a students' guide.

---

## ✅ Pre-Requisites

### 🌍 Set Environment Variables for Your Cluster

> 💡 **Note:** Your instructor will provide you with a private key to access your instance.

- Set your domain:

    ```bash
    export MY_DOMAIN="[your_name].runailabs-cs.com"
    ```

---

## 💻 Connect to Your Lab

- SSH into your lab using the provided key:

    ```bash
    ssh -i /my/key $MY_DOMAIN
    ```

- Change to the **Artifacts** directory:

    ```bash
    cd Artifacts
    ```

---

## 🏗 Create Namespaces

- Create `runai` and `runai-backend` namespaces:

    ```bash
    kubectl create ns runai
    kubectl create ns runai-backend
    ```

---

## 🔒 Enable Registry Secret

- You'll need to provide a secret to access the Run AI registry and pull artifacts:

    ```bash
    kubectl create -f ./install/runai-gcr-secret.yaml
    ```

---

## 🔐 Certificates

### 🌍 Domain Certificate

- Create a certificate for your domain (`*.runai.dev.local`):

    ```bash
    kubectl create secret tls runai-backend-tls -n runai-backend \
        --cert ./certificates/fullchain.pem --key ./certificates/private.pem
    
    kubectl create secret tls runai-cluster-domain-tls-secret -n runai \
        --cert ./certificates/fullchain.pem --key ./certificates/private.pem
    ```

### 🏛 Cluster Certificate for Local CA

- If using a local Certificate Authority (CA):

    ```bash
    kubectl -n runai create secret generic runai-ca-cert \
        --from-file=runai-ca.pem=./certificates/fullchain.pem
    ```

---

## 🖥 Install GPU Operator

- 🚀 We'll use a **fake GPU Operator** since we have no real GPUs in the cluster:

    ```bash
    helm repo add fake-gpu-operator https://fake-gpu-operator.storage.googleapis.com
    helm repo update
    helm upgrade -i gpu-operator fake-gpu-operator/fake-gpu-operator \
        --namespace gpu-operator --create-namespace --version 0.0.42
    ```

---

## 📊 Install Prometheus

- 📊 Prometheus is required for monitoring and metrics:

    ```bash
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    helm
