# ML Containerization and Azure Deployment — Step‑by‑Step Breakdown

This guide documents the **ML containerization** workflow used for this project: packaging the model/inference API into a Docker image, publishing it to **Azure Container Registry (ACR)**, and deploying it in Azure. It follows the same style as a GitHub README, but stays aligned to your Azure setup and the CLI‑first approach you used today.

> Note: Some steps are **GUI-only** (or far easier in the Azure Portal). Where that applies, it is called out.  
> Also note: **secrets (passwords/keys)** must not be committed to GitHub. Your thesis document contains the required passwords; GitHub should contain placeholders only.

---

## 1) What you are building

You are turning your ML inference workload into:

- a **Docker image** (portable, versionable)
- stored in **ACR** (private registry for images)
- deployed to one of:
  - **Option A: Azure Container Apps** (fast, simple, good for demos)
  - **Option B: Azure ML Managed Online Endpoint** (ML-native deployment and monitoring)

---

## 2) Prerequisites (local machine)

Install and verify:

- Docker Desktop
- Azure CLI
- (Optional) Azure ML tooling if you deploy to Azure ML endpoints

Quick checks:

```powershell
docker version
az version
az account show
```

---

## 3) Containerization: the Dockerfile (what each segment does)

Below is a standard pattern for ML inference containers. Your exact Dockerfile may differ, but the structure is the same.

### A) Base image
- Purpose: choose a Linux + Python (or ML runtime) base that supports your dependencies.

Example:
```dockerfile
FROM python:3.10-slim
```

If you deploy to Azure ML Managed Online Endpoints, you may instead use an Azure ML inference base image.

### B) Working directory
- Purpose: sets a clean folder inside the container.

```dockerfile
WORKDIR /app
```

### C) Copy dependency manifest first
- Purpose: improves Docker build cache (faster rebuilds).

```dockerfile
COPY requirements.txt .
RUN pip install -r requirements.txt
```

### D) Copy your source code
- Purpose: include inference server code (API) + model files.

```dockerfile
COPY . .
```

### E) Expose port + start command
- Purpose: allow inbound traffic and start your server process.

```dockerfile
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Key detail: `0.0.0.0` is required inside containers so the service is reachable from outside the container.

---

## 4) Build the image locally (and test it)

### A) Build
```powershell
docker build -t brain-tumor-ml:1.0 .
```

### B) Run locally
Map container port to your PC:

```powershell
docker run --rm -p 8000:8000 brain-tumor-ml:1.0
```

### C) Smoke test
```powershell
curl http://localhost:8000/health
```

---

## 5) Create/Use Azure Container Registry (ACR)

### A) Create the registry (one-time)
```powershell
az acr create `
  --name <ACR_NAME> `
  --resource-group <RG_NAME> `
  --location westeurope `
  --sku Basic
```

ACR creation and common operations are documented by Microsoft. citeturn4search14

### B) Log in to ACR (for Docker push)
```powershell
az acr login --name <ACR_NAME>
```

This authorizes Docker to push images to your ACR. citeturn4search14

---

## 6) Tag and push your image to ACR

### A) Get the ACR login server
```powershell
$ACR_LOGIN_SERVER = az acr show --name <ACR_NAME> --query loginServer -o tsv
```

### B) Tag the image
```powershell
docker tag brain-tumor-ml:1.0 $ACR_LOGIN_SERVER/brain-tumor-ml:1.0
```

### C) Push to ACR
```powershell
docker push $ACR_LOGIN_SERVER/brain-tumor-ml:1.0
```

### D) Confirm it exists in ACR
```powershell
az acr repository list --name <ACR_NAME> -o table
az acr repository show-tags --name <ACR_NAME> --repository brain-tumor-ml -o table
```

---

## 7) Deploy to Azure (two supported approaches)

### Option A — Deploy to Azure Container Apps (demo-friendly)

This approach works well when your container behaves like a standard HTTP API.

#### A1) Create or identify a Container Apps environment
Many deployments create the environment once in the Portal because it is easier to choose logging and networking settings there. Container Apps environments integrate with Log Analytics / Azure Monitor. citeturn6search20turn9search13

#### A2) Create the Container App (from your ACR image)
```powershell
az containerapp create `
  --name <APP_NAME> `
  --resource-group <RG_NAME> `
  --environment <ENV_NAME> `
  --image $ACR_LOGIN_SERVER/brain-tumor-ml:1.0 `
  --registry-server $ACR_LOGIN_SERVER `
  --ingress external `
  --target-port 8000
```

If your ACR is private, you typically use one of these patterns:
- registry username/password (quick but less clean), or
- managed identity + ACR pull role assignment (cleaner; commonly done in the Portal).

#### A3) View logs
For environment-level logging with Azure CLI, use `az containerapp env logs show`. citeturn9search14  
For deeper analysis and history, use Log Analytics queries in Azure Monitor. citeturn6search20

---

### Option B — Deploy to Azure ML Managed Online Endpoint (ML-native)

High-level flow:
1) Workspace exists  
2) Endpoint created  
3) Deployment created referencing the container image  
4) Traffic sent to the deployment  
5) Test the scoring URI  

Azure ML supports container-based inference patterns, including supported inference servers and guidance on container usage. citeturn4search16turn4search18

GUI note: Workspace networking, identity, and endpoint configuration are often easier in the Portal first, then updated via CLI.

---

## 8) Networking and security notes (matches your architecture)

Your architecture uses:
- private subnets for workloads
- firewall + UDR (default route to firewall)
- Site-to-Site VPN for an on‑prem simulation

For containerized inference, check:
- egress rules allow image pulls and required outbound calls
- DNS works (common blocker for private deployments)
- services are referenced by correct hostnames/IPs (avoid `127.0.0.1` unless the dependency runs inside the same container)

---

## 9) Common failure modes (fast checks)

- **Image push fails**: run `az acr login`, confirm the tag uses `$ACR_LOGIN_SERVER/...`
- **App not reachable**: wrong `--target-port`, or app binds to localhost instead of `0.0.0.0`
- **Redis/DB connection refused**: app uses `127.0.0.1` inside the container; switch to env vars pointing to the real service
- **Azure ML provisioning fails**: base image/environment mismatch, quota limits, or managed network restrictions

---

## 10) Configuration scripts and templates in this project

The configuration artifacts in this repository include:
- infrastructure deployment templates (commonly ARM JSON templates)
- CLI scripts for networking/security configuration (routing, firewall policy, VPN resources)

Not every operation is captured as a script:
- some settings are GUI-first in Azure
- some steps depend on resource IDs Azure generates at runtime

---

## 11) Where the rest of the work is documented

This repository contains only the scripts and templates appropriate for public version control.

For complete project materials:
- use the **GitHub repository** for the latest scripts, diagrams, and updates
- use the **thesis document** for passwords/secrets (not stored on GitHub)

---

## References (vendor documentation)

- Microsoft Learn — Azure Container Registry (ACR) documentation. citeturn4search14  
- Microsoft Learn — Azure Monitor / Log Analytics (logging and monitoring). citeturn6search20  
- Microsoft Learn — Azure Container Apps environment logging (CLI). citeturn9search14turn9search13  
- Microsoft Learn — Azure ML inference containers / supported inference servers (conceptual guidance). citeturn4search16turn4search18  
