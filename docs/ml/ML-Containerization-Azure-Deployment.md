# ML Model Containerization and Azure Deployment

## Overview
This document describes the full process used to containerize the machine learning inference service, validate it locally, and deploy it to Azure Container Apps. The steps reflect the actual implementation carried out during the project.

---

## 1. Dependency Management

### requirements.txt
```txt
fastapi
uvicorn[standard]
pydantic
azure-storage-blob
tensorflow-cpu
torch
torchvision
numpy
pillow
requests
segmentation-models-pytorch
efficientnet-pytorch
opencv-python
python-dotenv
google-generativeai
python-dotenv
```

**Purpose**
- Provides all libraries required for inference, preprocessing, and API exposure.
- Supports both TensorFlow and PyTorch models.
- Enables secure environment variable loading.

---

## 2. Environment Configuration

### .env file
```env
AZURE_STORAGE_CONNECTION_STRING=Connection string to Azure

TF_MODEL_PATH=models/brain_tumor_final.h5
TORCH_MODEL_PATH=models/best_unetpp_efficientb5_576.pth

USE_GPU=false
GOOGLE_API_KEY=insert your key
```

**Notes**
- Secrets are not stored in GitHub.
- Values are referenced at runtime by the container.
- GPU usage is disabled to align with Azure Consumption workload limits.

---

## 3. Dockerfile

```dockerfile
FROM python:3.10-slim

RUN apt-get update && apt-get install -y \
    libgl1 \
    libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload", "--env-file", ".env"]
```

**Explanation**
- Uses a lightweight Python base image.
- Installs Linux dependencies required by OpenCV.
- Exposes port 8000 for inference requests.
- Runs the FastAPI application via Uvicorn.

---

## 4. Local Build and Test

```bash
docker build -t ml-backend:v1 .
docker run -p 8000:8000 ml-backend:v1
```

**Validation**
- API tested locally before cloud deployment.
- Swagger UI accessed via `/docs`.
- Confirms correct model loading and endpoint behavior.

---

## 5. Azure Container Registry

```bash
az acr login --name braintumoracr
docker tag ml-backend:v1 braintumoracr.azurecr.io/ml-backend:v1
docker push braintumoracr.azurecr.io/ml-backend:v1
```

---

## 6. Azure Container Apps Deployment

```bash
az containerapp env create \
  --name ml-env \
  --resource-group 202203753_project \
  --location westeurope
```

```bash
az containerapp create \
  --name ml-backend \
  --resource-group 202203753_project \
  --environment ml-env \
  --image braintumoracr.azurecr.io/ml-backend:v1 \
  --registry-server braintumoracr.azurecr.io \
  --ingress external \
  --target-port 8000 \
  --cpu 0.5 \
  --memory 1.0Gi \
  --min-replicas 1 \
  --max-replicas 2
```

---

## 7. Endpoint Retrieval

```bash
az containerapp show \
  --name ml-backend \
  --resource-group 202203753_project \
  --query properties.configuration.ingress.fqdn \
  -o tsv
```

---

## Configuration Scripts Note

The configuration files included in this repository represent a subset of the overall system deployment. These files are primarily ARM-based templates and container definitions used to provision selected services.

Not all configurations are scripted, as several components (such as networking rules, scaling policies, and security settings) require interaction through the Azure Portal graphical interface.

All additional configuration artifacts and project documentation are available in the associated GitHub repository. Sensitive information, including credentials and secrets, is documented exclusively within the thesis submission and is intentionally excluded from GitHub for security reasons.

---

## Repository Reference

Further implementation details, diagrams, and supporting material can be found in the project GitHub repository. Readers are encouraged to review the repository to gain a complete understanding of the system architecture and deployment workflow.
