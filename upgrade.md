---
title: Upgrading Run AI
nav_order: 1
parent: Installs and Upgrades
---

# Upgrading The Cluster

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
