# ARM Template – Debian 12 VM Deployment on Azure

**Student:** Olamileye Duduyemi
**Date:** May 2026
**Assignment:** Infrastructure as Code with Azure ARM Templates

---

## What This Template Deploys

This ARM template provisions a complete Virtual Machine environment on Microsoft Azure, including all required networking components. All resource names are automatically derived from the VM name parameter.

| Resource | Name | Purpose |
|---|---|---|
| Virtual Network | `ClemVM-vnet` | Isolated private network (`10.0.0.0/16`) |
| Subnet | `default` | Segment within the VNet (`10.0.0.0/24`) |
| Network Security Group | `ClemVM-nsg` | Firewall rules (SSH, HTTP, HTTPS) |
| Public IP Address | `ClemVM-pip` | Static external IP to access the VM |
| Network Interface | `ClemVM-nic` | Connects VM to VNet, Public IP, and NSG |
| Virtual Machine | `ClemVM` | Debian 12 (Bookworm), Standard_B2ts_v2 |
| OS Disk | `ClemVM-osdisk` | StandardSSD_LRS managed disk (auto-deleted on VM delete) |

---

## Prerequisites

### 1. Install Azure CLI

Download and install the Azure CLI for Windows:
👉 https://aka.ms/installazurecliwindows

Verify installation:
```bash
az --version
```

### 2. Log in to Azure

```bash
az login
```
A browser window will open — sign in with your Azure account credentials.

### 3. Set Your Active Subscription

```bash
az account list --output table
az account set --subscription "Azure subscription 1"
```

---

## Deployment Commands

### Step 1 – Navigate to the Template Directory

```powershell
cd "C:\Users\duduy\OneDrive\Documents\Assignment 2"
```

### Step 2 – (Optional) Validate the Template First

```bash
az deployment group validate \
  --resource-group OlamVMGroup \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

### Step 3 – Deploy the Template

```bash
az deployment group create \
  --resource-group OlamVMGroup \
  --name arm-vm-deployment \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json \
  --verbose
```

> ⏱️ Deployment takes approximately **3–5 minutes**. You will see live progress in the terminal.

### Step 4 – View Deployment Outputs

```bash
az deployment group show \
  --resource-group OlamVMGroup \
  --name arm-vm-deployment \
  --query properties.outputs \
  --output json
```

This returns:
- `vmName` – the VM name (`ClemVM`)
- `vmResourceId` – the full Azure resource ID of the VM
- `nicResourceId` – the NIC resource ID
- `publicIPAddress` – the static public IP address
- `sshCommand` – the ready-to-use SSH command

### Step 5 – Get the Public IP Address

Because the Public IP is **Static**, the address is allocated at deployment time. Retrieve it with:

```bash
az vm show \
  --resource-group OlamVMGroup \
  --name ClemVM \
  --show-details \
  --query publicIps \
  --output tsv
```

---

## Connecting to the VM via SSH

Use the `sshCommand` output value directly, or construct it manually:

```bash
ssh clemadmin@<public-ip>
```

When prompted, enter the password defined in the parameters file.

**Example:**
```bash
ssh clemadmin@20.101.47.172
```

---

## Resource Dependencies (Deployment Order)

The ARM template uses `dependsOn` to enforce this creation order:

```
NSG  ──────────────────────────────┐
Public IP  ───────────────────────►  NIC  ──► Virtual Machine
Virtual Network (VNet + Subnet)  ──┘
```

This ensures the NIC (which requires all three) is created only after the NSG, Public IP, and VNet are fully provisioned.

---

## Security Controls

The Network Security Group (`ClemVM-nsg`) enforces the following inbound rules:

| Rule Name | Direction | Port | Protocol | Priority | Action |
|---|---|---|---|---|---|
| `Allow-SSH` | Inbound | 22 | TCP | 300 | **Allow** |
| `Allow-HTTP` | Inbound | 80 | TCP | 320 | **Allow** |
| `Allow-HTTPS` | Inbound | 443 | TCP | 340 | **Allow** |

All other inbound traffic is implicitly denied by Azure's default rules.

---

## Template Parameters

| Parameter | Value | Description |
|---|---|---|
| `vmName` | `ClemVM` | Name of the VM and base for all resource names |
| `location` | `westeurope` | Azure region (West Europe) |
| `vmSize` | `Standard_B2ts_v2` | VM SKU |
| `adminUsername` | `clemadmin` | Administrator username |
| `adminPassword` | *(see parameters file)* | Administrator password |
| `osDiskType` | `StandardSSD_LRS` | OS managed disk storage tier |
| `ubuntuOSVersion` | `debian-12` | OS image (`debian-12` or `debian-11`) |

---

## Cleanup (Delete Resources When Done)

> ⚠️ **Important:** Delete the VM after the assignment to avoid exhausting your Free Tier compute hours.

```bash
# Delete just the VM and its disk (osDisk deleteOption is set to Delete automatically)
az vm delete --resource-group OlamVMGroup --name ClemVM --yes

# Or delete ALL resources in the resource group
az group delete --name OlamVMGroup --yes --no-wait
```

---

## Template Files

| File | Description |
|---|---|
| `azuredeploy.json` | Main ARM template (parameters, variables, resources, outputs) |
| `azuredeploy.parameters.json` | Parameter values for deployment |
| `output_log.md` | Captured deployment outputs and verification |
| `screenshots/` | Evidence screenshots of successful deployment |
