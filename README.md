# IT7099-Project-Cloud
# Brain Tumor Detection – Azure Infrastructure Scripts

This repository contains Azure-generated and manually refined scripts used for the **Brain Tumor Detection and Analysis System** project.  
You use these scripts to deploy, configure, and manage cloud infrastructure components that support secure machine learning inference and web services.

The focus of this repository is **infrastructure and deployment**, not application source code.

---

## Project Overview

The project designs and implements a secure Azure cloud infrastructure to support:

- Medical image (MRI) processing
- Machine learning inference workloads
- Secure access for healthcare users
- Compliance with healthcare data protection standards

The infrastructure follows a **private-by-default** design with controlled access using Azure networking and security services.

---

## Repository Purpose

You use this repository to:

- Store Azure CLI–generated scripts
- Track Infrastructure-as-Code (IaC) artifacts
- Document deployment steps for reproducibility
- Support academic evaluation and demonstration

This repository supports documentation in the thesis **Implementation**, **Appendix**, and **System Documentation** sections.

---

## Contents

<pre>
├── azure-cli/
│   ├── vnet-and-subnets.sh
│   ├── vpn-gateway.sh
│   ├── nsg-rules.sh
│
├── container-apps/
│   ├── backend-containerapp.json
│   ├── frontend-containerapp.json
│
├── azure-ml/
│   ├── workspace-setup.sh
│   ├── endpoint-deploy.sh
│
├── storage/
│   ├── blob-storage-setup.sh
│
├── scripts/
│   ├── helper-commands.txt
│
└── README.md
</pre>




---

## Technologies Used

- Azure Virtual Network (VNet)
- Azure Container Apps
- Azure Machine Learning
- Azure Blob Storage
- Azure VPN Gateway (Site-to-Site)
- Azure CLI
- JSON ARM templates and shell scripts

---

## Deployment Scope

These scripts cover:

- Virtual network and subnet creation
- Private subnet isolation
- Secure VPN connectivity
- Containerized backend and frontend deployment
- Machine learning endpoint provisioning
- Secure storage configuration

Application logic and ML model code are **out of scope** for this repository.

---

## How to Use

You are expected to already have:

- An active Azure subscription
- Azure CLI installed
- Contributor access to the target resource group

Basic usage example:

```bash
az login
az account set --subscription <subscription-id>
bash vnet-and-subnets.sh

