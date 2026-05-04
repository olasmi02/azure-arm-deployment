# Deployment Output Log

**Deployment Name:** `arm-vm-deployment`
**Resource Group:** `OlamVMGroup`
**Date:** May 2026
**Deployed By:** Olamileye Duduyemi

---

## Deployment Status

> ```bash
> az deployment group show --resource-group OlamVMGroup --name arm-vm-deployment --query properties.outputs --output json
> ```

```json
{  
  "nicResourceId": {
    "type": "String",
    "value": "/subscriptions/48a524af-aaa3-4ca6-b340-0ec493322ff4/resourceGroups/OlamVMGroup/providers/Microsoft.Network/networkInterfaces/ClemVM-nic"
  },
  "publicIPAddress": {
    "type": "String",
    "value": "20.101.47.172"
  },
  "sshCommand": {
    "type": "String",
    "value": "ssh clemadmin@20.101.47.172"
  },
  "vmName": {
    "type": "String",
    "value": "ClemVM"
  },
  "vmResourceId": {
    "type": "String",
    "value": "/subscriptions/48a524af-aaa3-4ca6-b340-0ec493322ff4/resourceGroups/OlamVMGroup/providers/Microsoft.Compute/virtualMachines/ClemVM"
  }
}
```

---

## Public IP Address

> Run the command below and paste the IP address here:
> ```bash
> az vm show --resource-group OlamVMGroup --name ClemVM --show-details --query publicIps --output tsv
> ```

**Public IP:** `20.101.47.172`

---

## SSH Connectivity Proof

The following command was used to connect to the VM:

```bash
ssh clemadmin@20.101.47.172
```

Connection was successful. See `screenshots/03_ssh_connection.png` for proof.

---

## Resources Provisioned

| Resource | Type | Name |
|---|---|---|
| Virtual Machine | `Microsoft.Compute/virtualMachines` | `ClemVM` |
| OS Disk | `Microsoft.Compute/disks` | `ClemVM-osdisk` |
| Network Interface | `Microsoft.Network/networkInterfaces` | `ClemVM-nic` |
| Virtual Network | `Microsoft.Network/virtualNetworks` | `ClemVM-vnet` |
| Subnet | (part of VNet) | `default` |
| Network Security Group | `Microsoft.Network/networkSecurityGroups` | `ClemVM-nsg` |
| Public IP Address | `Microsoft.Network/publicIPAddresses` | `ClemVM-pip` |
