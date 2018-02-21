---
title: "使用 Azure CLI 创建分区的 Linux VM | Microsoft Docs"
description: "使用 Azure CLI 在可用性区域中创建 Linux VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 280c31d00acc074653b6594235f78e4d569464b4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>使用 Azure CLI 在可用性区域中创建 Linux 虚拟机

本文逐步说明如何使用 Azure CLI 在 Azure 可用性区域中创建 Linux VM。 [可用性区域](../../availability-zones/az-overview.md)是 Azure 区域中物理上独立的区域。 使用可用性区域可以在整个数据中心发生故障或服务中断（这种情况很少见）时保护应用和数据。

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

确保已安装了最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/#az_login) 登录到 Azure 帐户。


## <a name="check-vm-sku-availability"></a>查看 VM SKU 可用性
VM 大小或 SKU 的可用性可能因地区和区域而异。 可以按 Azure 区域列出可用的 VM SKU，以便规划可用性区域的使用。 此功能可确保选择适当的 VM 大小，并跨区域获取所需的复原能力。 有关不同 VM 类型和大小的详细信息，请参阅 [VM 大小概述](sizes.md)。

可以使用 [az vm list-skus](/cli/azure/vm#az_vm_list_skus) 命令查看可用的 VM SKU。 以下示例列出了 *eastus2* 区域中可用的 VM SKU：

```azurecli
az vm list-skus --location eastus2 --output table
```

输出类似于以下浓缩版示例，其中显示了每种 VM 大小都可用的可用性区域：

```azurecli
ResourceType      Locations  Name               Tier       Size     Zones
----------------  ---------  -----------------  ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2    Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2    Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s       Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s       Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3    Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3    Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3     Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3     Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。  

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机前创建资源组。 在此示例中，在“eastus2”区域中创建了名为“myResourceGroupVM”的资源组。 “美国东部 2”是支持可用性区域预览版的 Azure 区域之一。

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus2
```

创建或修改 VM 时指定资源组，本文会对此进行演示。

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建虚拟机。 

创建虚拟机时，可使用多个选项，例如操作系统映像、磁盘大小调整和管理凭据。 在此示例中，创建了一个名为“myVM”的运行 Ubuntu Server 的虚拟机。 在可用性区域 *1* 中创建了一个 VM。 默认情况下，创建的 VM 大小为 *Standard_DS1_v2*。 可用性区域预览版支持此大小。

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

创建 VM 可能需要几分钟。 创建 VM 后，Azure CLI 会输出有关 VM 的信息。 请记下 `zones` 值，该值指示在其中运行 VM 的可用性区域。 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="zone-for-ip-address-and-managed-disk"></a>IP 地址和托管磁盘的区域

当 VM 部署在可用性区域中时，IP 地址和托管磁盘资源将部署在同一可用性区域中。 以下示例可获取有关这些资源的信息。

先使用 [az vm list-ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses) 命令返回 *myVM* 中公共 IP 地址资源的名称。 在此示例中，该名称存储在变量中，会在稍后的步骤中用到。

```azurecli-interactive
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

现在可以获取有关 IP 地址的信息：

```azurecli-interactive
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

输出显示，IP 地址与 VM 位于相同的可用性区域：

```azurecli-interactive
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

同样，请验证 VM 的托管磁盘是否位于该可用性区域中。 使用 [az vm show](/cli/azure/vm#az_vm_show) 命令返回磁盘 ID。在此示例中，磁盘 ID 存储在变量中，会在稍后的步骤中用到。 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
现在可以获取有关托管磁盘的信息：

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

输出显示，托管磁盘与 VM 位于相同的可用性区域中：

```azurecli-interactive
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/Subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2017-09-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```
 


## <a name="next-steps"></a>后续步骤

本文已介绍如何在可用性区域中创建 VM。 详细了解 Azure VM 的[区域和可用性](regions-and-availability.md)。




