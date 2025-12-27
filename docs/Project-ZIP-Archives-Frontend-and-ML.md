
# Project Source Code ZIP Archives

## 1. Frontend Source Code (brain-tumor-frontend-main.zip)

This ZIP archive contains the complete client-side implementation of the Brain Tumor Detection System.

### Purpose
The frontend provides the user interface through which users can upload MRI images, view analysis results, and interact with the system. It communicates with the backend machine learning API using HTTP requests.

### Key Contents
- `src/` – React/Vite source code implementing application logic and UI components.
- `public/` – Static assets such as images and icons.
- `.env` – Build-time environment variables, including the backend API URL.
- `Dockerfile` – Multi-stage Docker configuration used to build and serve the application with NGINX.
- `package.json` – JavaScript dependencies and build scripts.

### Deployment Summary
The frontend is containerised using Docker and built locally to generate a production-ready static bundle. The container image is then pushed to Azure Container Registry (ACR) and deployed using Azure Container Apps with external ingress enabled to allow public access.

---

## 2. Machine Learning Backend API (ml-backend.zip)

This ZIP archive contains the backend service responsible for executing machine learning inference.

### Purpose
The backend exposes a RESTful API that performs image preprocessing, model inference, and result generation using pre-trained deep learning models. It acts as the core processing component of the system.

### Key Contents
- `app/` – FastAPI application defining API endpoints and inference logic.
- `models/` – Pre-trained TensorFlow and PyTorch model files used for tumor detection and segmentation.
- `requirements.txt` – Python dependencies required for API operation and machine learning workloads.
- `.env` – Runtime configuration values such as storage connection strings and model paths (excluded from GitHub).
- `Dockerfile` – Docker configuration for building the inference service container.

### Deployment Summary
The backend service is containerised using Docker and tested locally via Uvicorn. After validation, the image is pushed to Azure Container Registry and deployed to Azure Container Apps. Environment variables and secrets are configured directly within Azure to protect sensitive information.
