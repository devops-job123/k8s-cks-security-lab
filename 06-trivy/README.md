# 06 - Trivy (Vulnerability Scanning)

## Objective

Scan the **SBOM files** generated in Step `08-sbom` for known **CVEs (Common Vulnerabilities and Exposures)** using **Trivy**.

This step demonstrates the value of SBOM: we scan inventory (SBOM) instead of the full image, making the process **faster** and **offline-capable**.

## Installation

    sudo apt-get install wget gnupg -y
    wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
    echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
    sudo apt-get update
    sudo apt-get install trivy -y
    trivy version

## Usage

### 1. Scan SBOM (fast, offline)

    trivy sbom alpine_3.19-sbom.json
    trivy sbom nginx_1.25-alpine-sbom.json

### 2. Scan image directly (slower, pulls latest CVE data)

    trivy image nginx:1.25-alpine

### 3. Filter by severity

    trivy sbom --severity CRITICAL,HIGH nginx_1.25-alpine-sbom.json

### 4. Output as JSON

    trivy sbom -f json -o nginx-scan.json nginx_1.25-alpine-sbom.json

## Results Summary

| Image | Packages | Critical | High | Medium | Low | Total |
|-------|----------|----------|------|--------|-----|-------|
| alpine:3.19 | 15 | 0 | 1 | 2 | 1 | **4** |
| nginx:1.25-alpine | 66 | 1 | 9 | 17 | 9 | **36** |

### Alpine:3.19 — Vulnerabilities Found (4)

| Package | CVE | Severity | Installed | Fixed |
|---------|-----|----------|-----------|-------|
| busybox | CVE-2024-58251 | MEDIUM | 1.36.1-r20 | 1.36.1-r21 |
| busybox | CVE-2025-46394 | LOW | 1.36.1-r20 | 1.36.1-r21 |
| musl | CVE-2026-40200 | HIGH | 1.2.4_git20230717-r5 | 1.2.4_git20230717-r6 |
| musl | CVE-2026-6042 | MEDIUM | 1.2.4_git20230717-r5 | 1.2.4_git20230717-r6 |

### Nginx:1.25-alpine — Critical Finding (1)

| Package | CVE | Severity | Installed | Fixed |
|---------|-----|----------|-----------|-------|
| libxml2 | CVE-2024-56171 | **CRITICAL** | 2.11.7-r0 | 2.11.8-r1 |

> **Action Required:** Upgrade `libxml2` to `2.11.8-r1` or later. This is a **Use-After-Free** vulnerability that can lead to remote code execution.

### Nginx:1.25-alpine — High Findings (9)

Including CVEs in `curl`, `libxslt`, `musl`, and `libxml2`. See `nginx-scan.txt` for full details.

> **Note:** `alpine:3.19.1` is no longer supported by the distribution. Security updates are not provided. Consider upgrading to `alpine:3.20` or `alpine:3.21` to receive future security patches.

## Why SBOM-based scanning?

- **Speed**: No image pull needed.
- **Offline**: Works in air-gapped environments.
- **Compliance**: Proves you scanned exactly what you shipped.
- **Supply Chain**: Detects vulnerabilities in transitive dependencies.

## Files Generated

| File | Description |
|------|-------------|
| `alpine-scan.txt` | Trivy table output for alpine SBOM |
| `nginx-scan.txt` | Trivy table output for nginx SBOM |
| `alpine-scan.json` | Trivy JSON output for automation |
| `nginx-scan.json` | Trivy JSON output for automation |

## Screenshots

### Headlamp - Trivy Results

![](../screenshots/09-headlamp-trivy.png)

## What I Learned

- How to install Trivy via APT repository
- How to scan SBOM files for vulnerabilities
- How to compare SBOM scan vs image scan
- How to filter by severity and output JSON
- Why SBOM-based scanning is faster and more supply-chain-aware
- How to read Trivy output (severity, fixed version, CVE)
- How to identify **CRITICAL** vulnerabilities that require immediate action
