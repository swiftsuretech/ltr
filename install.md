---
title: Installing Run AI
nav_order: 1
parent: Installs and Upgrades
---

# Installing a Self-Hosted Cluster

Note - Always refer to documentation - this is just a students' guide*

## Pre-Reqs

### Set some ENVs for your cluster

#### Note: Your instructor will provide you with a private key to access your instance

- Set your domain

    ```bash
    export MY_DOMAIN="[your_name].runailabs-cs.com"
    ```

### Connect to your lab

- SSH into your lab with the provided key

    ```bash
    ssh -i /my/key $MY_DOMAIN
    ```

- Change to the Artifacts directory

    ```bash
    cd Artifacts
    ```



### Create Namespaces

- Create runai-backend and runai namespaces

    ```bash
    kubectl create ns runai
    kubectl create ns runai-backend
    ```

### Enable registry secret

- We'll need to provide this secret to access the Run AI registry to pull all artifacts

    ```bash
    kubectl create -f ./install/runai-gcr-secret.yaml
    ```

### Certificates

#### Domain Certificate

- Create a certificate for your domain. For example, if your domain is `runai.dev.local`, you will need to create a certificate for `*.runai.dev.local`.
  
    ```bash
    kubectl create secret tls runai-backend-tls -n runai-backend \
        --cert ./certificates/fullchain.pem --key ./certificates/private.pem
    
    kubectl create secret tls runai-cluster-domain-tls-secret -n runai \
        --cert ./certificates/fullchain.pem --key ./certificates/private.pem
    ```

#### Cluster Installation when running with a local certificate authority

- Create a secret for the CA certificate.
  
    ```bash
    kubectl -n runai create secret generic runai-ca-cert \
        --from-file=runai-ca.pem=./certificates/fullchain.pem
    ```

### Install GPU Operator

- We will install a fake GPU operator rather than the official NVIDIA one as we have no real GPUs in the nodes

    ```bash
    helm repo add fake-gpu-operator https://fake-gpu-operator.storage.googleapis.com
    helm repo update
    helm upgrade -i gpu-operator fake-gpu-operator/fake-gpu-operator --namespace gpu-operator --create-namespace  --version 0.0.42
    ```

### Install Prometheus

- We'll need prometheus for our metrics

    ```bash
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    helm install prometheus prometheus-community/kube-prometheus-stack \
        -n monitoring --create-namespace --set grafana.enabled=false
    ```

### Install Ingress Controller

- We'll use nginx. As it's a KIND cluster, there's a manifest available for that. First we'll label the nodes appropriately for this particular manifest

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
    ```

### Run the pre-flight check script

- Download and run the script. Correct any errors before proceeding:

    ```bash
    wget -O preflight https://github.com/run-ai/preinstall-diagnostics/releases/download/v2.16.19/preinstall-diagnostics-linux-amd64
    chmod +x preflight
    ./preflight --cluster-domain $MY_DOMAIN --domain $MY_DOMAIN
    ```

## Install the Backend

- Helm install the backend

    ```bash
    helm repo add runai-backend https://runai.jfrog.io/artifactory/cp-charts-prod
    helm repo update
    helm upgrade -i runai-backend -n runai-backend runai-backend/control-plane --version "~2.20" --set global.domain=$MY_DOMAIN
    ```

### Open the Web UI

- Open a web browser and navigate to $MY_DOMAIN. Use credentials user: test@run.ai / password: Abcd!234

## Install the Cluster

- Follow the instructions after you log in
- Deploy to the same cluster as the control plane and set relevant matching version
- Copy the installation instructions to deploy
- Click "done"
- Wait for the cluster to connect

## Check for the correct nodes and GPUs

- Visit the nodes and page to ensure that all expected GPUs are registered
- View the dashboards to ensure that metrics are working as expected