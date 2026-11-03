# 04 - Sealed Secrets

## Objective
Encrypt Kubernetes Secrets with Sealed Secrets so they can be safely committed to Git.

## Resources

| Resource | Name | Purpose |
|----------|------|---------|
| SealedSecret | `app-secret` | Encrypted secret (safe for Git) |
| Secret | `app-secret` | Decrypted by controller |

## Installation

### 1. Sealed Secrets Controller

    wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.27.0/controller.yaml
    kubectl apply -f controller.yaml

    # Verify
    kubectl get pods -n kube-system -l app.kubernetes.io/name=sealed-secrets

### 2. kubeseal CLI

    wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.27.0/kubeseal-0.27.0-linux-amd64.tar.gz
    tar -xvzf kubeseal-0.27.0-linux-amd64.tar.gz
    sudo install -m 755 kubeseal /usr/local/bin/kubeseal

    kubeseal --version

## Usage

### 1. Create plain Secret (app-secret.yaml)

    apiVersion: v1
    kind: Secret
    metadata:
      name: app-secret
      namespace: secure-app
    type: Opaque
    data:
      password: c2VjdXJlLXBhc3N3b3JkLTEyMw==
      api-key: YWJjLTEyMy14eXotNDU2

### 2. Encrypt with kubeseal

    kubeseal --format yaml < app-secret.yaml > sealed-secret.yaml

### 3. Apply Sealed Secret

    kubectl apply -f sealed-secret.yaml

### 4. Verify

    kubectl get sealedsecret -n secure-app
    kubectl get secret -n secure-app

## Screenshots

### Headlamp - Sealed Secret

![](../screenshots/08-headlamp-sealed-secret.png)

## What I Learned
- How to install Sealed Secrets Controller
- How to encrypt Secrets with kubeseal
- How to safely commit encrypted secrets to Git
- How Sealed Secrets Controller decrypts them in-cluster
