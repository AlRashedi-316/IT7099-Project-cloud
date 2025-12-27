# Site-to-Site VPN Implementation (Azure ↔ On-Premises)

## Overview
This document describes the implementation of a Site-to-Site (S2S) VPN connecting an on-premises network to an Azure Virtual Network (VNet). The VPN provides encrypted, private connectivity using IPsec and IKEv2.

---

## Network Addressing

| Environment | Address Space |
|------------|---------------|
| Azure VNet | 10.0.0.0/16 |
| On-Premises Network | 192.168.100.0/24 |
| Gateway Subnet | 10.0.255.0/27 |

---

## Step 1: Azure VPN Gateway Creation

```powershell
az network vnet-gateway create `
  --name vpngw-cloud `
  --resource-group Management-RS `
  --vnet Brain_Tumor_Cloud `
  --gateway-type Vpn `
  --vpn-type RouteBased `
  --sku VpnGw1 `
  --public-ip-address pip-vpngw-cloud
```

Creates the Azure VPN Gateway inside the GatewaySubnet to terminate the VPN tunnel.

---

## Step 2: On-Premises VPN Gateway (Simulated)

```powershell
az network vnet-gateway create `
  --name vpngw-onprem `
  --resource-group Management-RS `
  --vnet OnPrem-Sim-VNet `
  --gateway-type Vpn `
  --vpn-type RouteBased `
  --sku VpnGw1 `
  --public-ip-address pip-vpngw-onprem
```

Simulates an on-premises VPN device for testing the Site-to-Site VPN.

---

## Step 3: Local Network Gateway (On-Premises)

```powershell
az network local-gateway create `
  --name lng-onprem `
  --resource-group Management-RS `
  --gateway-ip-address 172.201.182.91 `
  --local-address-prefixes 192.168.100.0/24
```

Defines the on-premises address space and public IP for Azure routing.

---

## Step 4: VPN Connection Creation

```powershell
az network vpn-connection create `
  --name conn-cloud-to-onprem `
  --resource-group Management-RS `
  --vnet-gateway1 vpngw-cloud `
  --local-gateway2 lng-onprem `
  --shared-key S2S-VPN-2025!
```

Establishes the encrypted Site-to-Site VPN tunnel.

---

## Security Configuration

- Key Exchange: IKEv2
- Encryption: IPsec ESP (AES-256)
- Integrity: SHA-256
- Diffie-Hellman Group: 14
- Tunnel Type: Always-On

---

## Outcome

The Site-to-Site VPN enables secure, private communication between Azure workloads and on-premises systems without public exposure.
