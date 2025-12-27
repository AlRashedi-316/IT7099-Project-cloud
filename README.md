# IT7099 – Azure Cloud Infrastructure Project

## Project Overview
This project designs and implements a secure Azure cloud infrastructure to support:

- Medical image (MRI) processing
- Machine learning inference workloads
- Secure access for healthcare users
- Compliance-aligned cloud architecture for healthcare environments

The infrastructure follows a **private-by-default** design using Azure networking, security, and monitoring services. Public exposure is minimized and tightly controlled.

---

## Repository Purpose
You use this repository to:

- Store exported Azure Resource Manager (ARM) templates
- Track Infrastructure-as-Code (IaC) artifacts
- Document networking and ML deployment steps
- Support academic evaluation, demonstration, and audit
- Provide appendix material for the thesis

This repository directly supports the **Implementation**, **Appendix**, and **System Documentation** sections of the IT7099 thesis.

---

## Repository Structure
<pre>
├── docs/
│ ├── ml/
│ │ └── ML-Containerization-Azure-Deployment.md
│ └── networking/
│ └── Site-to-Site-VPN-Implementation.md
│
├── infra/
│ └── arm-exports/
│ ├── backend/
│ ├── frontend/
│ ├── management/
│ ├── security/
│ ├── networking/
│ ├── ml/
│ └── misc/
│
├── infrastructure/
│ └── arm-templates/
│ ├── containers/
│ ├── monitoring/
│ └── virtual-machines/
│
└── README.md
</pre>



---

## Folder Description

### `docs/`
Contains written technical documentation explaining key design and implementation steps.

- **ml/**  
  Documentation related to Azure Machine Learning containerization and deployment.
- **networking/**  
  Documentation for Site-to-Site VPN configuration and network connectivity.

---

### `infra/arm-exports/`
Contains **ZIP exports of deployed Azure resources** generated directly from the Azure portal.

These files represent the **actual deployed state** of the infrastructure and are stored for:

- Audit and verification
- Recovery reference
- Academic documentation
- Architecture validation

Subfolders:
- **backend/** – Backend application resource groups and services
- **frontend/** – Frontend and web-facing infrastructure
- **management/** – Monitoring, logging, and management resources
- **security/** – Azure Firewall and security-related components
- **networking/** – VNets, subnets, Bastion, VMSS, VPN-related resources
- **ml/** – Azure Machine Learning workspace exports
- **misc/** – Databases, storage accounts, test VMs, and supporting resources

Each folder includes a `README.md` describing its contents.

---

### `infrastructure/arm-templates/`
Contains ARM template ZIP files used for **deployment and redeployment** of selected Azure services.

These templates represent **deployment artifacts**, not full environment exports.

Subfolders:
- **containers/** – Azure Container Apps deployments
- **monitoring/** – Application Insights and monitoring components
- **virtual-machines/** – VM and VM Scale Set deployments

---

## Technologies Used

- Azure Virtual Network (VNet)
- Azure Firewall
- Azure VPN Gateway (Site-to-Site)
- Azure Container Apps
- Azure Machine Learning
- Azure Blob Storage
- Azure Virtual Machines and VM Scale Sets
- Azure Resource Manager (ARM) templates
- Azure CLI
- Git and Git LFS

---

## Deployment Scope
This repository covers infrastructure-level components only:

- Virtual network and subnet design
- Secure network isolation
- Site-to-Site VPN connectivity
- Firewall and security enforcement
- Containerized application hosting
- Machine learning infrastructure
- Storage and database infrastructure

**Application source code and ML model logic are intentionally out of scope.**

---

## Notes
- ARM exports reflect the deployed state at the time of submission
- Templates may require to be tempred before being used.
- Repository structure is aligned with academic submission and demonstration requirements
